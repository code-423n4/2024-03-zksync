# Ecosystem contracts

This document described technical details of zkStack contracts that make it possible for multiple L2/L3 chains to share liquidity, and provide cost-effective communication among them. Before delving into the details of ecosystem contracts, please familiarize yourself with the concept of [Hyperchain](TODO) and read [L1 smart contracts page](TODO) for a better understanding of single instance hyperchain.

These contracts are a part of the hyperchain upgrade and are not yet in production.

![Hyperchain contracts](L1%20smart%20contracts/Hyperchain-scheme.png)

## State Transition

The State Transition contract is the source of truth of L2/L3 chain to Ethereum, it maintains all batch information pertinent to a specific chain, holds messages sent from the chain to Ethereum, performs the state transition (submit information to the DA layer, verifies the proof and finalize the state). In the context of the currently deployed zkSync Era, it functions as a main diamond proxy.

For an in-depth understanding of how this interacts with the L1 smart contracts and their operations, please refer to the documentation on the [L1 Smart Contracts](TODO) page.

> **NOTE**: Currently deployed zkSync Era also holds ether deposited to the system. After this upgrade, all state transition contracts, including zkSync Era diamond proxy, will no longer directly manage any funds. Instead, this responsibility will transition to the shared bridge. For details on how this migration will be implemented, please refer to [migration doc](TODO). This document described technical details of zkStack contracts that make it possible for multiple L2/L3 chains to share liquidity, and provide cost-effective communication among them. Before delving into the details of ecosystem contracts, please familiarize yourself with the concept of [Hyperchain](TODO) and read [L1 smart contracts page](TODO) for a better understanding of single instance hyperchain.

## STM (State Transition Manager)

Each State Transition contract controlled by the STM. While a single STM can manage multiple chains, each chain is only managed by one STM. Please note that currently there's only one STM version for all hyperchains, but other will be added in the future.

The first version of STM incorporates safeguarding features to limit the power of the chain validator and prevent unexpected issues. When the code proves its maturity these security features will be removed. As an example of such features, this STM version enforces each chain has a Validator Timelock. This means chain finalization is delayed by a few hours, giving zkSync governance time to address any security problems by freezing the chain and applying security upgrades when needed.

`StateTransitionManager` is responsible for:
* Creating new chains: It's responsible for adding new chains to the ecosystem. This process involves setting up the State Transition contract in response to requests from `BridgeHub` (defined later). The STM ensures that the setup adheres to system rules, preventing modification to the system that allows actions like withdrawing more funds than what was deposited.
* Upgrade mechanism: STM saves the protocol upgrade version together with upgrade data, so State Transition can validate that the upgrade was approved by zkSync Governance.
* Freezability: One of the STM's key powers is to temporarily halt operations on a chain if a security threat is detected. This freezing capability is vital for mitigating risks and addressing vulnerabilities in a timely manner for the period.
* Reverting batches: In response to discovered bugs or security loopholes, the STM can revert chain batches. Together with `ValidatorTimelock` in place, this allows continue to monitor the chain activity and have time to react to suspicious activity.

The STM has two privileged roles that control its critical functionality:
* owner - Controlled by `Governance.sol`. In its turn, `Governance.sol` is controlled by two multisigs: Admin multisig (see below) and Security Council multisig (well-respected contributors in the crypto space). Collaboratively, these entities hold the power to implement instant upgrades, whereas Matter Labs alone is limited to scheduling upgrades with a delay.
* admin - Matter Labs Safe multisig which permission is limited to reverting batches and setting validator timelock. In contrast to the owner, this multisig doesn't have a timelock for execution.

## Bridgehub

- Acts as a hub for bridges, so that they have a single point of communication with all hyperchain contracts. This allows L1 assets to be locked in the same contract for all hyperchains, including L3s and validiums. The `Bridgehub` also implements the following:
    
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

Only Owner can cancel the upgrade before its execution. 

The diagram below outlines the complete journey from the initiation of an operation to its execution.

![governance.png](L1%20smart%20contracts/Governance-scheme.jpg)

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

### Upgrade mechanism

Currently, there are three types of upgrades for zkSync Era. Normal upgrades (used for new features) are initiated by the Governor (a multisig) and are public for a certain timeframe before they can be applied. Shadow upgrades are similar to normal upgrades, but the data is not known at the moment the upgrade is proposed, but only when executed (they can be executed with the delay, or instantly if approved by the security council). Instant upgrades (used for security issues), on the other hand happen quickly and need to be approved by the Security Council in addition to the Governor. For hyperchains the difference is that upgrades now happen on multiple chains. This is only a problem for shadow upgrades - in this case, the chains have to tightly coordinate to make all the upgrades happen in a short time frame, as the content of the upgrade becomes public once the first chain is upgraded.
The actual upgrade process is as follows: 

1. Prepare Upgrade for all chains:
    - The new facets and upgrade contracts have to be deployed,
    - The upgrade’ calldata (diamondCut, initCalldata with ProposedUpgrade) is hashed on L1 and the hash is saved.
2. Upgrade specific chain
    - The upgrade has to be called on the specific chain. The upgrade calldata is passed in as calldata and verified. The protocol version is updated.
    - Ideally, the upgrade will be very similar for all chains. If it is not, a smart contract can calculate the differences. If this is also not possible, we have to set the `diamondCut` for each chain by hand.
3. Freeze not upgraded chains
    - After a certain time the chains that are not upgraded are frozen.
