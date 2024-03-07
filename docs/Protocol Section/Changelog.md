# Changelog

## How to review changes from the previous contest?

You can read the full diff between the scope for the previous contest here:
- https://github.com/code-423n4/2024-03-zksync/compare/2023-10-zksync...main

## What's changed?

### Protocol

#### Added

* The Hyperchain - Moving from the single rollup architecture to the hyperchain, introducing `SharedBridge` and more.
* Custom base token support - Now newly created hyperchains can select different tokens as tokens in which fee will be paid.
* Validium support - Now newly created hyperchains can different Data Availability models to either send data to Ethereum or a third-party service.
* [RIP-7212](https://github.com/ethereum/RIPs/blob/master/RIPS/rip-7212.md) support
* [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) support

#### Changed

* Fee model changed.

### L1 Smart contracts

#### Added

* `L1SharedBridge.sol` - One contract that holds all funds across multiple rollups making the first step for fast messaging and cheap cross-chain transactions.
* `Bridgehub.sol` - The single point of communication with all hyperchain contracts.
* `StateTransitionManager.sol` - Contract that manages to create new chains, upgrading, freezing, and reverting batches on existing ones.
* `blobVersionedHashRetriever.yul` - Contract wrapper for calling `BLOB_HASH_OPCODE` from the old Solidity version.

#### Removed

* `L2WethBridge.sol` - Deprecated. In the future WETH token deposits will be natively supported by the shared bridge.
* `L2ERC20Bridge.sol` - Deprecated in favor of `L2SharedBridge`

#### Changed

* `L1ERC20Bridge.sol` - Remove the liquidity from the contract in favor of storing funds in `L1SharedBridge.sol`
* `Governance.sol` - Now only the owner can cancel scheduled operation. `execute`/`executeInstant` are `payable` now.
* `Admin.sol` - Changed the access control from Governance and Admin to State Transition Manager. Adopt functionality related to the Hyperchain changes.
* `COMMIT_TIMESTAMP_NOT_OLDER`/`COMMIT_TIMESTAMP_APPROXIMATION_DELTA` - Reduced constant to minimize the possibility for the operator to manipulate L2 timestamps.
* `ValidatorTimelock.sol` - Added support for multiple validators on different hyperchains.
* `Verifier.sol` - Verification keys are updated.
* `Getters.sol` - New getters added to reflect on other changes.
* `Executor.sol`: 
    - Support validiums and data blobs.
    - Added `commit`/`prove`/`execute` batch function mirrors to support the new Validator Timelock interface. 
    - Added check for protocol version consistency with the State Transition manager. 
    - Only one batch commitment is available at a time. 
    - State Transition Manager now can revert batches. 
    - Batch commitment changed.
* `Mailbox.sol`:
    - Added function to migrate funds from zkSync Era to the Shared Bridge `transferEthToSharedBridge`
    - `requestL2Transaction` is restricted for use only in zkSync Era chain.
    - `requestL2Transaction` is deprecated in favor `bridgehubRequestL2Transaction`, access is now restricted only to the BridgeHub.
    - L1 -> L2 transaction cost is changed to reflect the new fee model and support of custom base tokens.
    - `finalizeEthWithdrawal` is restricted to zkSync Era.
* `Diamond.sol` - `_addFunctions`/`_replaceFunctions` now checks that the added facet has deployed bytecode.
* `TransactionValidator.sol` - `validateUpgradeTransaction` has strict checks. `getOverheadForTransaction` is changed to reflect changes in the fee model.
* `BaseZkSyncUpgrade.sol`: `_setVerifierParams` skip changing parameters only if all parameters in `_newVerifierParams` are zeroes. Now the difference between protocol versions can't be bigger than 100.

### L2 Smart contracts

#### Added

* `L2SharedBridge.sol` - The L2 counterparty of L1SharedBridge.
* `L2StandardERC20.sol`  - Added function to reinitialize the getters variables.

#### Removed

* `L2WethBridge.sol` - Deprecated. In the future WETH token deposits will be natively supported by the shared bridge.
* `L2ERC20Bridge.sol` - Deprecated in favor of `L2SharedBridge`

#### Changed

* `ForceDeployUpgrader.sol` - `forceDeploy` function is payable now and it is forward sent ether to the Deployer.

### System contracts and Bootloader

#### Added

* `GasBoundCaller.sol` - Contract that limits the amount of gas and pubdata child called contract can spend.
* `PubdataChunkPublisher.sol` - Smart contract for chunking pubdata into the appropriate size for EIP-4844 blobs.
* `CodeOracle.yul` - The contract used to emulate EVM's `extcodecopy` behavior.
* `P256Verify.yul` - The contract that emulates RIP-7212's P256VERIFY precompile.

#### Changed

* `Bootloader.yul`:
    - Added `shouldMsgValueMimicCallBeSystem` as protection from calling system contracts with `isSystem` flag without need.
    - TODO
* `MsgValueSimulator.sol` - Added support of gas stipend to allow `.transfer`/`.send` Solidity features.
* `SystemContext.sol` - Added support of changing `chainID` during the upgrade.
* `Compressor.sol` - Added small protection of publishing very inefficient compression in `publishCompressedBytecode`
* `ContractDeployer.sol` - `extendedAccountVersion` returns `Version1` for all nonsystem contracts without bytecode.
* `DefaultAccount.sol` - Fixed the issue with delegate call to the default account.
* `L1Messenger.sol`:
    - Fixed the computational cost of sending logs in `sendL2ToL1Log`. 
    - Added burning of spend pubdata according to the latest changes in the fee model.
    - Added support for submitting blobs data into L1.
* `IEthToken`/`L2EthToken`/`L2_ETH_ADDRESS` - Changed the name to `IBaseToken`/`L2BaseToken`/`L2_BASE_TOKEN_ADDRESS`
* `SystemContractHelper.sol`:
    - `unsafePrecompileCall`/`burnGas` - now burns pubdata in addition to gas.
    - `getZkSyncMeta` - now returns `heapSize`/`pubdataPublished`/`auxHeapSize` fields.
* `Ecrecover.yul` - Cost changed.
* `Keccak256.yul` - Padding is calculated in the zk circuit directly now.
