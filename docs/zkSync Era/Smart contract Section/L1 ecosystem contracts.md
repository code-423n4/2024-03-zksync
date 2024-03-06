# Ecosystem contracts

![Hyperchain contracts](L1%20smart%20contracts/Hyperchain-scheme.png)

## Bridgehub

- Acts as a hub for bridges, so that they have a single point of communication with all hyperchain contracts. This allows L1 assets to be locked in the same contract for all hyperchains, including L3s and validiums. The `Bridgehub` also implements the following:
- `Registry`
    
    This is where hyperchains can register, starting in a permissioned manner, but with the goal to be permissionless in the future. This is where their `chainID` is determined. L3s will also register here. 
    
    This `Registry` is also where base tokens can be registered. Each hyperchain will have a base token that gas fees will be charged in. These external base token contracts will be fully customizable by their owners, they will only have to adhere to the interface with the `SharedBridge` which serves as the main bridge for ERC20 tokens and communicates with the`Bridgehub`.
    
    This `Registry` is also where State Transition Manager contracts should register. Each chain has to specify its desired ST when registering (Initially, only one will be available). 
    
    ```solidity
    function createNewChain(
            uint256 _chainId,
            address _stateTransitionManager,
            address _baseToken,
            uint256 _salt,
            address _l2Governor,
            bytes calldata _initData
        ) external nonReentrant returns (uint256 chainId);
    
    function addStateTransitionManager(address _stateTransitionManager) external;
    ```
    
- `BridgehubMailbox` routes messages to the Diamond proxy’s Mailbox facet based on chainID
    - This is where L2 transactions can be requested.
    - Similar as the current zkSync Era [Mailbox](https://github.com/matter-labs/era-contracts/blob/main/ethereum/contracts/zksync/facets/Mailbox.sol), just with chainId as an added parameter, and modified to allow interactions with the baseToken. There are two  external methods, and three structs that need to be known.
    
    ```solidity
    function requestL2TransactionDirect(
            L2TransactionRequestDirect calldata _request
        ) external payable returns (bytes32 canonicalTxHash);
    
        function requestL2TransactionTwoBridges(
            L2TransactionRequestTwoBridgesOuter calldata _request
        ) external payable returns (bytes32 canonicalTxHash);
    
    struct L2TransactionRequestDirect {
        uint256 chainId;
        uint256 mintValue;
        address l2Contract;
        uint256 l2Value;
        bytes l2Calldata;
        uint256 l2GasLimit;
        uint256 l2GasPerPubdataByteLimit;
        bytes[] factoryDeps;
        address refundRecipient;
    }
    
    struct L2TransactionRequestTwoBridgesOuter {
        uint256 chainId;
        uint256 mintValue;
        uint256 l2Value;
        uint256 l2GasLimit;
        uint256 l2GasPerPubdataByteLimit;
        address refundRecipient;
        address secondBridgeAddress;
        uint256 secondBridgeValue;
        bytes secondBridgeCalldata;
    }
    
    struct L2TransactionRequestTwoBridgesInner {
        bytes32 magicValue;
        address l2Contract;
        bytes l2Calldata;
        bytes[] factoryDeps;
        bytes32 txDataHash;
    }
    ```
    
    - `requestL2TransactionDirect` is similar to the current `requestL2Transaction` function on the `Mailbox`. It requests the transaction specified in `L2TransactionDirect` on the `L2TransactionDirect.chainId` chain. The `mintValue` variable is new, it specifies the  total amount of new base tokens minted on the L2, this includes `l2Value` and the gas fee (on the Mailbox this was specified by `msg.value` ). If the chains base token is ether, then `mintValue` has to be equal to `msg.value`, and this amount of ether is deposited to the `SharedBridge`. If the base token is custom, the `msg.sender` and `mintValue` is passed to the  `SharedBridge`, where a transfer of this amount of tokens is requested from `msg.sender` in the base token contract. The `msg.sender` is also used as the `msg.sender` on the L2 transaction, with the caveat that if it is not an EOA then the address is aliased (to avoid address collisions for smart contracts). The transaction call is sent to the hyperchains `Mailbox`, and the returning `txHash` is returned.
    - `requestL2TransactionTwoBridges` is slightly more complicated. Here the transfer of  `mintValue` is requested from the `SharedBridge` similarly to `requestL2Transaction`, and the L2 transactions parameters related to this value ( such as `l2Value`, `l2GasLimit`, `l2GasPerPubdataByteLimit`, `refundRecipient`) are specified directly in the call. But to determine the content of the call (`l2Contract`, `l2Calldata`, `factoreyDeps`) a second bridge is called. The `Bridgehub` makes a call to the second bridge, which returns the content of the call, as well as a `magicValue`, for security reasons. The `secondBridgeAddress` is used as the `msg.sender` on the L2Transaction. After the Bridgehub makes the forwards to the hyperchains `Mailbox`, the `txHash` is returned to the `secondBridge`, so that it can store that the tx happened. 
    
    The intution behind the `requestL2TransactionTwoBridges` is to allow the second bridge to make a call to the L2 without the user giving control over their baseToken to the contract. If for example, the user wants to deposit an alt token using the second bridge to the L2, then the user only has to approve a base token allowance for the base token bridge, and a alt token allowance for the second bridge. Having the second bridge not handle tokens makes the process simpler and more secure.

## State Transition Manager 

- `StateTransitionManager`
    
    A state transition manages proof verification and DA for multiple chains. It also implements the following functionalities: 
    
    - `StateTransitionManagerRegistry`
        
        The STM is shared for multiple chains, as initialization and upgrades have to be the same for all chains. Registration is not permissionless but happens based on the registrations in the bridgehub’s `Registry`. At registration a `DiamondProxy` is deployed and initialized with the appropriate `Facets` for each Hyperchain. To integrate already deployed chains such as Era, an extra function is called.
        
    - `Facets` and `Verifier`
        
        are shared across chains that relies on the same STM: `Base`, `Executor` , `Getters`,  `Admin` , `Mailbox.`The `Verifier`  is the contract that actually verifies the proof, and is called by the `Executor`. 
        
    - Upgrade Mechanism
        
        The system requires all chains to be up-to-date with the latest implementation, so whenever an update is needed, we have to “force” each chain to update, but due to decentralization, we have to give each chain a time frame (more information in the [Upgrade Mechanism](https://www.notion.so/ZK-Stack-shared-bridge-alpha-version-a37c4746f8b54fb899d67e474bfac3bb?pvs=21) section). This is done in the update mechanism contract, this is where the bootloader and system contracts are published, and the `ProposedUpgrade` is stored. Then each chain can call this upgrade for themselves as needed. After the deadline is over, the not-updated chains are frozen, that is, cannot post new proofs. Frozen chains can unfreeze by updating their proof system.

## Bridges

Bridges are completely separate contracts from the Bridgehub. They are a wrapper for L1 <-> L2 communication on contracts
on both L1 and L2. Upon locking assets on one layer, a request is sent to mint these bridged assets on the other layer.
Upon burning assets on one layer, a request is sent to unlock them on the other. The `SharedBridge` is the current main bridge, that is used to store the base tokens of each participating chain. 

### L1SharedBridge

The base token of every hyperchain needs to connect to the L1 SharedBridge so that bridging to and from the L2 and other hyperchains can happen seamlessly. It also makes sense to natively support some special tokens (ETH, WETH, etc), and to support some generally accepted token standards (ERC20 tokens), as this makes it easy to bridge those tokens (and ensures a single version of them exists on the hyperchain). These cannonical asset contracts are deployed to L2 from L1 by a bridge shared by all hyperchains. This is where assets are locked on L1. These bridges use the Bridgehub to communicate with all hyperchains. 

The "standard" implementation of the ERC20 token and Ether bridge. Works only with Ether and regular ERC20 tokens, i.e. not with
fee-on-transfer tokens or other custom logic for handling user balances. It is expected to be called from the `Bridgehub`. 

- `bridgehubDepositBaseToken` - called by the `Bridgehub` to deposit base tokens in the bridge.
- `bridgehubDeposit` - called by the `Bridgehub` in the `requestL2TransactionTwoBridges`, and calldata is provided by the original `msg.sender`. The L2 transaction's `msg.sender` will be the aliased `L1SharedBridge` address.
- `claimFailedDeposit` - unlock funds if the deposit was initiated but then failed on L2.
- `finalizeWithdrawal` - unlock funds for the valid withdrawal request from L2.

The owner of the L1SharedBridge is the Governance contract.

### L2SharedBridge

The L2 counterpart of the L1 ERC20 bridge.

- `withdraw` - initiate a withdrawal by burning funds on the contract and sending a corresponding message to L1.
- `finalizeDeposit` - finalize the deposit and mint funds on L2. The function is only callable by L1 bridge. 

The owner of the L2ERC20Bridge and the contracts related to it is the Governance contract.

### L1ERC20Bridge

The legacy bridge used to trasfer tokens to Era only. All the assets will be held in the L1SharedBridge going forward, and this bridge will only make calls to the L1SharedBridge, which is designed to interoperate with this bridge.

- `deposit` - lock funds inside the contract and send a request to mint bridged assets on L2.
- `claimFailedDeposit` - unlock funds if the deposit was initiated but then failed on L2.
- `finalizeWithdrawal` - unlock funds for the valid withdrawal request from L2.

## Governance

This contract manages calls for all governed zkSync Era contracts on L1 and L2. Mostly, it is used for upgradability and changing critical system parameters. The contract has minimum delay settings for the call execution. 

Each upgrade consists of two steps:

- Scheduling - The owner can schedule upgrades in two different manners:
    - Fully transparent data. All the targets, calldata, and upgrade conditions are known to the community before upgrade execution.
    - Shadow upgrade. The owner only shows the commitment to the upgrade. This upgrade type is mostly useful for fixing critical issues in the production environment.
- Upgrade execution - the Owner or Security council can perform the upgrade with previously scheduled parameters.
    - Upgrade with delay. Scheduled operations should elapse the delay period. Both the owner and Security Council can execute this type of upgrade.
    - Instant upgrade. Scheduled operations can be executed at any moment. Only the Security Council can perform this type of upgrade.

Please note, that both the Owner and Security council can cancel the upgrade before its execution. 

The diagram below outlines the complete journey from the initiation of an operation to its execution.

![governance.png](L1%20smart%20contracts/governance.jpg)

## ValidatorTimelock

An intermediate smart contract between the validator EOA account and the diamon proxy state transition smart contracts.
Its primary purpose is
to provide a trustless means of delaying batch execution without modifying the main state transition contract. zkSync actively
monitors the chain activity and reacts to any suspicious activity by freezing the chain. This allows time for
investigation and mitigation before resuming normal operations.

It is a temporary solution to prevent any significant impact of the validator hot key leakage, while the network is in
the Alpha stage.

This contract consists of four main functions `commitBatchesSharedBridge`, `proveBatchesSharedBridge`, `executeBatchesSharedBridge`, and `revertBatchesSharedBridge`, which can be called only by the validator of the given chain.

When the validator calls `commitBatchesSharedBridge`, the same calldata will be propagated to the zkSync contract with the correct chainId (`DiamondProxy` through `call` where it invokes the `ExecutorFacet` through `delegatecall`), and also a timestamp is assigned to these batches to track
the time these batches are committed by the validator to enforce a delay between committing and execution of batches. Then, the
validator can prove the already committed batches regardless of the mentioned timestamp, and again the same calldata (related
to the `proveBatches` function) will be propagated to the zkSync contract. After the `delay` is elapsed, the validator
is allowed to call `executeBatches` to propagate the same calldata to zkSync contract. 

The owner of the ValidatorTimelock contract is the same as the owner of the Governance contract - Matter Labs multisig.
