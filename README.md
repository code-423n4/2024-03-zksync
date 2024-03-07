# zkSync Era audit details
- Total Prize Pool: $250,000 in USDC 
  - HM awards: $208,526 in USDC 
  - Analysis awards: $0 in USDC 
  - QA awards: $5,487 in USDC
  - Bot Race awards: $0 USDC 
  - Gas awards:  $5,487 in USDC
  - Judge awards: $18,000 in USDC 
  - Lookout awards: $12,000 in USDC
  - Scout awards: $500 in USDC
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-03-zksync-era/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts March 7, 2024 20:00 UTC
- Ends March 28, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-03-zksync/blob/main/4naly3er-report.md).

*Note for C4 wardens: Anything included in the 4naly3er report is considered a publicly known issue and is ineligible for awards.*

### DefaultAccount does not always return successfully

[DefaultAccount](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol), while it should always behave as an EOA (i.e. any call to it should return `success(0,0)`), if called with a selector of one of its methods and incorrect ABI-encoding of its parameters, will fail with empty error. This happens due to the fact that the ABI decoding fails before the modifier is triggered.

### extcodehash does not distinguish empty contracts on account balances

According to the [EIP-161](https://eips.ethereum.org/EIPS/eip-161), an account is “empty” when it meets the following conditions: it has no deployed code, its nonce value is zero, and it holds a balance of zero. According to [EIP-1052](https://eips.ethereum.org/EIPS/eip-1052), the extcodehash of an empty account should evaluate to `bytes32(0)`, otherwice it is `keccak256(deployedBytecode)`. On zkSync, `bytes32(0)` is returned in case there is no deployed code and nonce is zero regardless of the account balance.

### Validator can provide innefient bytecode compression

In the current implementation, the mechanism for bytecode compression is not strictly unambiguous. That means the validator has the flexibility to choose a less efficient compression for the bytecode to increase the deployment cost for the end user. Besides that, there is another non-fixed [issue](https://github.com/code-423n4/2023-10-zksync-findings/issues/805), that gives a way for the operator to forces the user to pay more for the bytecode compression or even burn all the transaction gas during bytecode compression verification.

### Aknowledged issues from the previous audits

All unfixed issues from the previous audits are considered out of scope.
- https://era.zksync.io/docs/reference/troubleshooting/audit-bug-bounty.html

### Known differences from Ethereum

More known differences from Ethereum can be found in our [documentation](https://era.zksync.io/docs/reference/architecture/differences-with-ethereum.html).

# Overview

# **zkSync Protocol Overview & Documentation**

zkSync Era is a fully-fledged Layer-2 scaling solution that enhances Ethereum's scalability and efficiency. It achieves this through a combination of smart contracts deployed on the Ethereum and the zkEVM, which facilitates EVM-compatible smart contract execution in a more efficient manner.

This repository is dedicated to providing comprehensive documentation and code pertaining exclusively to the Smart Contracts aspect of the zkSync Era Protocol. Below, you will find a high-level overview of the smart contracts section, accompanied by relevant documentation links. We highly encourage auditors to consult these resources for an in-depth understanding of the protocol's smart contracts framework before and during the audit.

As part of this security audit, we are focusing on the changes and updates made to the smart contracts since our last security review with code4rena. Our goal is to ensure the continued security, efficiency, and reliability of the zkSync Era platform as it evolves.

## **📁 Sections**

### **1. Protocol Section**

- **[Protocol overview](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Protocol%20Section/Overview.md)**
- **[Migration process](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Protocol%20Section/Migration%20process.md)**
- **[Changelog](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Protocol%20Section/Changelog.md)**

### **2. Smart Contracts Section**

Relevant Documentation:

- **[L1 smart contracts](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md)**
- **[L1 ecosystem contracts](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/L1%20ecosystem%20contracts.md)**
- **[System Contracts/Bootloader Description](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md)**
- **[zkSync Era Fee Model](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md)**
- **[Handling L1→L2 Ops on zkSync](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20L1→L2%20ops%20on%20zkSync.md)**
- **[Batches & L2 Blocks on zkSync](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20&%20L2%20blocks%20on%20zkSync.md)**
- **[Elliptic Curve Precompiles](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/Elliptic%20curve%20precompiles.md)**
- **[Handling Pubdata](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20pubdata.md)**
- **[Pubdata post EIP-4844](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/Smart%20contract%20Section/Pubdata%20Post%204844.md)**

### **3. VM Section**

The VM section is related to the zkSync Era Virtual Machine.

- **[ZkSync Era Virtual Machine Primer](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/VM%20Section/ZkSync%20Era%20Virtual%20Machine%20primer.md)**
    - This primer is designed to provide auditors with a foundational understanding of the zkSync Era Virtual Machine. It offers insights into the operational mechanics and integral components of EraVM, serving as an essential guide for those seeking to explore the zkSync EraVM environment.
- **[zkSync Era: The Equivalence Compiler Documentation](https://github.com/code-423n4/2024-03-zksync/blob/main/docs/VM%20Section/How%20compiler%20works/overview.md)**
    - The document describes how zkSync Solidity compiler represents high-level programming language constructions into low-level EraVM instruction set, how to use unique features without extending Solidity language with new syntax and why system contracts are needed.

## **🚀 Getting Started for Auditors**

- Ensure to go through each section and related documents thoroughly.
- Keep in mind the overall working of the zkSync protocol while reviewing individual components.
- Review the code and documentation with a focus on security, correctness, and optimization, particularly concerning gas consumption.

## **📢 Communication**

For any clarifications, doubts, or discussion, please contact Code4rena staff, and we will address your concerns promptly.

## Links

- **Documentation:** https://era.zksync.io/docs/
- **Website:** https://zksync.io/
- **Twitter:** https://twitter.com/zksync
- **Discord:** https://join.zksync.dev/
- **Previous Audits:** https://era.zksync.io/docs/reference/troubleshooting/audit-bug-bounty.html

# Scope

## L1 contracts

### Bridges

| Contract | SLOC | Libraries used |
| --- | --- | --- |
| [contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol) | 119 | @openzeppelin |
| [contracts/ethereum/contracts/bridge/L1SharedBridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol) | 448 | @openzeppelin |
| [contracts/ethereum/contracts/bridgehub/Bridgehub.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol) | 246 | @openzeppelin |

### Governance

| Contract | SLOC | Libraries used |
| --- | --- | --- |
| [contracts/ethereum/contracts/governance/Governance.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) | 133 | @openzeppelin |

### State transition (zkSync)

| Contract | SLOC | Libraries used |
| --- | --- | --- |
| [contracts/ethereum/contracts/state-transition/StateTransitionManager.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol) | 163 | @openzeppelin |
| [contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol) | 142 | @openzeppelin |
| [contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol) | 35 |  |
| [contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol) | 30 |  |
| [contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol) | 66 |  |
| [contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol) | 92 |  |
| [contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol) | 436 |  |
| [contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol) | 155 |  |
| [contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol) | 277 | @openzeppelin |
| [contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol) | 40 |  |

### Upgrades

| Contract | SLOC | Libraries used |  
| ----------- | ----------- | ----------- |
| [contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol) | 145 |  |
| [contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol) | 43 |  |
| [contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol) | 9 |  |
| [contracts/ethereum/contracts/upgrades/GenesisUpgrade.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/GenesisUpgrade.sol) | 10 |  |

### Libraries

| Contract | SLOC | Libraries used |
| --- | --- | --- |
| [contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol) | 38 |  |
| [contracts/ethereum/contracts/common/libraries/UncheckedMath.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol) | 18 |  |
| [contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol) | 40 |  |
| [contracts/ethereum/contracts/state-transition/libraries/Diamond.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol) | 174 | @openzeppelin |
| [contracts/ethereum/contracts/state-transition/libraries/LibMap.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/LibMap.sol) | 23 |  |
| [contracts/ethereum/contracts/state-transition/libraries/Merkle.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol) | 30 |  |
| [contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol) | 42 |  |
| [contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol) | 75 | @openzeppelin |

### Interfaces

| Contract | SLOC | Libraries used |
| --- | --- | --- |
| [contracts/ethereum/contracts/bridge/interfaces/IL1ERC20Bridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1ERC20Bridge.sol) | 55 |  |
| [contracts/ethereum/contracts/bridge/interfaces/IL1SharedBridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1SharedBridge.sol) | 113 |  |
| [contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol) | 14 |  |
| [contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol) | 5 |  |
| [contracts/ethereum/contracts/bridgehub/IBridgehub.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/IBridgehub.sol) | 90 |  |
| [contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol) | 16 |  |
| [contracts/ethereum/contracts/governance/IGovernance.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol) | 38 |  |
| [contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol) | 46 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol) | 32 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol) | 34 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol) | 93 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol) | 48 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol) | 9 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IMailbox.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IMailbox.sol) | 58 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IVerifier.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IVerifier.sol) | 14 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransition.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransition.sol) | 15 |  |
| [contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransitionBase.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransitionBase.sol) | 4 |  |
| [contracts/ethereum/contracts/state-transition/l2-deps/ISystemContext.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/l2-deps/ISystemContext.sol) | 4 |  |
| [contracts/ethereum/contracts/upgrades/IDefaultUpgrade.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/IDefaultUpgrade.sol) | 5 |  |

### Others

| Contract | SLOC | Libraries used |
| --- | --- | --- |
| [contracts/ethereum/contracts/common/Config.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Config.sol) | 33 |  |
| [contracts/ethereum/contracts/common/L2ContractAddresses.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol) | 9 |  |
| [contracts/ethereum/contracts/common/Messaging.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Messaging.sol) | 59 |  |
| [contracts/ethereum/contracts/common/ReentrancyGuard.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol) | 59 |  |
| [contracts/ethereum/contracts/vendor/AddressAliasHelper.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol) | 29 |  |
| [contracts/ethereum/contracts/state-transition/utils/BlobVersionedHashRetriever.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/utils/BlobVersionedHashRetriever.yul) | 21 |  |

## L2 contracts

### Bootloader

| Contract | SLOC | Libraries used |  
| ----------- | ----------- | ----------- |
| [system-contracts/bootloader/bootloader.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul) | 2314 |  |

### System Contracts

| Contract | SLOC | Libraries used |
| ----------- | ----------- | ----------- |
| [system-contracts/contracts/AccountCodeStorage.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol) | 75 |  |
| [system-contracts/contracts/BootloaderUtilities.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol) | 239 |  |
| [system-contracts/contracts/ComplexUpgrader.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol) | 21 |  |
| [system-contracts/contracts/Compressor.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol) | 147 |  |
| [system-contracts/contracts/Constants.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Constants.sol) | 73 |  |
| [system-contracts/contracts/ContractDeployer.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol) | 226 |  |
| [system-contracts/contracts/DefaultAccount.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol) | 133 |  |
| [system-contracts/contracts/EmptyContract.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol) | 11 |  |
| [system-contracts/contracts/EventWriter.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/EventWriter.yul) | 105 |  |
| [system-contracts/contracts/ImmutableSimulator.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol) | 30 |  |
| [system-contracts/contracts/KnownCodesStorage.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol) | 52 |  |
| [system-contracts/contracts/L1Messenger.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol) | 225 |  |
| [system-contracts/contracts/L2BaseToken.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol) | 79 |  |
| [system-contracts/contracts/MsgValueSimulator.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol) | 39 |  |
| [system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol) | 102 |  |
| [system-contracts/contracts/PubdataChunkPublisher.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol) | 37 |  |
| [system-contracts/contracts/SystemContext.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol) | 242 |  |
| [contracts/GasBoundCaller.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/GasBoundCaller.sol) | 33 |  |

### Precompiles

| Contract | SLOC | Libraries used |
| ----------- | ----------- | ----------- |
| [system-contracts/contracts/precompiles/EcAdd.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/EcAdd.yul) | 234 |  |
| [system-contracts/contracts/precompiles/EcMul.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/EcMul.yul) | 270 |  |
| [system-contracts/contracts/precompiles/Ecrecover.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/Ecrecover.yul) | 68 |  |
| [system-contracts/contracts/precompiles/Keccak256.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/Keccak256.yul) | 67 |  |
| [system-contracts/contracts/precompiles/SHA256.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/SHA256.yul) | 65 |  |
| [contracts/precompiles/CodeOracle.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/CodeOracle.yul) | 66 |  |
| [contracts/precompiles/P256Verify.yul](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/precompiles/P256Verify.yul) | 68 |  |


### Libraries

| Contract | SLOC | Libraries used |
| ----------- | ----------- | ----------- |
| [system-contracts/contracts/libraries/EfficientCall.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol) | 174 |  |
| [system-contracts/contracts/libraries/RLPEncoder.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol) | 80 |  |
| [system-contracts/contracts/libraries/SystemContractHelper.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol) | 184 |  |
| [system-contracts/contracts/libraries/SystemContractsCaller.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol) | 150 |  |
| [system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol) | 263 | @openzeppelin |
| [system-contracts/contracts/libraries/UnsafeBytesCalldata.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol) | 44 |  |
| [system-contracts/contracts/libraries/Utils.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol) | 56 |  |

### System contract Interfaces

| Contract | SLOC | Libraries used |
| ----------- | ----------- | ----------- |
| [system-contracts/contracts/interfaces/IAccount.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IAccount.sol) | 26 |  |
| [system-contracts/contracts/interfaces/IAccountCodeStorage.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IAccountCodeStorage.sol) | 9 |  |
| [system-contracts/contracts/interfaces/IBaseToken.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IBaseToken.sol) | 21 |  |
| [system-contracts/contracts/interfaces/IBootloaderUtilities.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IBootloaderUtilities.sol) | 7 |  |
| [system-contracts/contracts/interfaces/IComplexUpgrader.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IComplexUpgrader.sol) | 9 |  |
| [system-contracts/contracts/interfaces/ICompressor.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/ICompressor.sol) | 22 |  |
| [system-contracts/contracts/interfaces/IContractDeployer.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IContractDeployer.sol) | 54 |  |
| [system-contracts/contracts/interfaces/IImmutableSimulator.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IImmutableSimulator.sol) | 9 |  |
| [system-contracts/contracts/interfaces/IKnownCodesStorage.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IKnownCodesStorage.sol) | 13 |  |
| [system-contracts/contracts/interfaces/IL1Messenger.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IL1Messenger.sol) | 25 |  |
| [system-contracts/contracts/interfaces/IL2StandardToken.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IL2StandardToken.sol) | 9 |  |
| [system-contracts/contracts/interfaces/IMailbox.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IMailbox.sol) | 10 |  |
| [system-contracts/contracts/interfaces/INonceHolder.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/INonceHolder.sol) | 22 |  |
| [system-contracts/contracts/interfaces/IPaymaster.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IPaymaster.sol) | 22 |  |
| [system-contracts/contracts/interfaces/IPaymasterFlow.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IPaymasterFlow.sol) | 12 |  |
| [system-contracts/contracts/interfaces/IPubdataChunkPublisher.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/IPubdataChunkPublisher.sol) | 9 |  |
| [system-contracts/contracts/interfaces/ISystemContext.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContext.sol) | 31 |  |
| [system-contracts/contracts/interfaces/ISystemContextDeprecated.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContextDeprecated.sol) | 11 |  |
| [system-contracts/contracts/interfaces/ISystemContract.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContract.sol) | 40 |  |

### Bridges

| Contract | SLOC | Libraries used |  
| ----------- | ----------- | ----------- |
| [contracts/zksync/contracts/bridge/L2SharedBridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2SharedBridge.sol) | 103 | @openzeppelin |
| [contracts/zksync/contracts/bridge/L2StandardERC20.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol) | 97 | @openzeppelin |


### Bridge interfaces

| Contract | SLOC | Libraries used |  
| ----------- | ----------- | ----------- |
| [contracts/zksync/contracts/bridge/interfaces/IL1ERC20Bridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/interfaces/IL1ERC20Bridge.sol) | 10 |  |
| [contracts/zksync/contracts/bridge/interfaces/IL1SharedBridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/interfaces/IL1SharedBridge.sol) | 11 |  |
| [contracts/zksync/contracts/bridge/interfaces/IL2SharedBridge.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/interfaces/IL2SharedBridge.sol) | 27 |  |
| [contracts/zksync/contracts/bridge/interfaces/IL2StandardToken.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/interfaces/IL2StandardToken.sol) | 10 |  |
| [contracts/zksync/contracts/interfaces/IPaymaster.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/interfaces/IPaymaster.sol) | 22 |  |
| [contracts/zksync/contracts/interfaces/IPaymasterFlow.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/interfaces/IPaymasterFlow.sol) | 13 |  |

### Other

| Contract | SLOC | Libraries used |
| ----------- | ----------- | ----------- |
| [contracts/zksync/contracts/vendor/AddressAliasHelper.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol) | 29 |  |
| [contracts/zksync/contracts/ForceDeployUpgrader.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/ForceDeployUpgrader.sol) | 7 |  |
| [contracts/zksync/contracts/L2ContractHelper.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol) | 88 |  |
| [contracts/zksync/contracts/SystemContractsCaller.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol) | 103 |  |


## Out of scope

| Contract | SLOC | Libraries used |  
| ----------- | ----------- | ----------- |
|  | | |
| [contracts/ethereum/contracts/state-transition/Verifier.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/Verifier.sol) | 1161 |  |
| [contracts/ethereum/contracts/upgrades/Upgrade_4844.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/Upgrade_4844.sol) | 11 |  |
| [contracts/ethereum/contracts/upgrades/Upgrade_v1_4_1.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/Upgrade_v1_4_1.sol) | 27 |  |
| [contracts/ethereum/contracts/common/Dependencies.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Dependencies.sol) | 3 | @openzeppelin |
| [contracts/zksync/contracts/Dependencies.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/Dependencies.sol) | 3 | @openzeppelin |
| [contracts/zksync/contracts/Config.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/Config.sol) | 3 |  |
| [contracts/zksync/contracts/TestnetPaymaster.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/TestnetPaymaster.sol) | 51 | @openzeppelin |
| [contracts/zksync/contracts/bridge/L2WrappedBaseToken.sol](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WrappedBaseToken.sol) | 116 | @openzeppelin |


## Attack ideas (Where to look for bugs)

### Access control and permissions

It is important to examine access control and permissions for any contract that contains potentially dangerous logic (including upgrades). While the assumption is that either governance or security council are not malicious, neither governance, nor the security council should be able to circumvent the limitations imposed on them.

Special scrutiny should be paid to the powers of the operator. While currently the operator is controlled by Matter Labs and is also partially trusted (for instance, it is responsible for supplying the correct L1 gas price), it should never be able to directly steal users' funds or conduct malicious upgrades. An [example](https://github.com/code-423n4/2024-03-zksync/blob/main//docs/Smart%20contract%20Section/Handling%20L1%E2%86%92L2%20ops%20on%20zkSync.md) of such an issue, which was detected and resolved by the team before the contest. 

### Data availability issues

Another important invariant is that the state of the rollup can be restored based on the pubdata sent to L1. Make sure that for a block that gets executed regardless of what a potentially malicious operator does:

- Users can always get preimages for all the bytecodes that were deployed to the system.
- Users can always recover the leaves of the Merkle tree of L2->L1 logs.
- Users can always recover the storage merkle tree.

In general, there should be always a possibility to have a new operator that fully recovers the state available solely from L1 and is able to execute transactions successfully.

### EVM compatibility attacks

Make sure that access to any dangerous logic is well-constrained. For instance:

- Access to potentially dangerous system contracts' methods is protected by the `isSystemCall` flag, permitting only the contracts that are aware of the zkSync-specific features to call it.
- Using innocent Solidity code without zkSync-specific features should not lead to unexpected behaviour. An [example](https://code4rena.com/reports/2023-03-zksync#h-01-the-call-to-msgvaluesimulator-with-non-zero-msgvalue-will-call-to-sender-itself-which-will-bypass-the-onlyself-check) of a relevant finding.

## Scoping Details 

```
- If you have a public code repo, please share it here:  https://github.com/matter-labs/era-contracts
- How many contracts are in scope?:   114
- Total SLoC for these contracts?:  10,663
- How many external imports are there?:  13
- How many separate interfaces and struct definitions are there for the contracts within scope?:  94
- Does most of your code generally use composition or inheritance?:   inheritance
- Is this an upgrade of an existing system?: Yes
- Check all that apply (e.g. timelock, NFT, AMM, ERC20, rollups, etc.): timelock, ERC20, rollups
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?:   No
- Please describe required context:   ZK rollup
- Does it use an oracle?:  No
- Describe any novel or unique curve logic or mathematical models your code uses: No
- Is this either a fork of or an alternate implementation of another project?: No
- Does it use a side-chain?: No
- Describe any specific areas you would like addressed: Logical errors, malicious input for bootloader, EVM compatibility issues
```

# Tests

## (Foundry) L1 contracts one liner

```
rm -Rf 2024-03-zksync || true && git clone https://github.com/code-423n4/2024-03-zksync.git && cd 2024-03-zksync/code/contracts/ethereum && yarn --ignore-engines && yarn test:foundry
```

## (Hardhat) L1 contracts one liner

```
rm -Rf 2024-03-zksync || true && git clone https://github.com/code-423n4/2024-03-zksync.git && cd 2024-03-zksync/code/contracts/ethereum && yarn --ignore-engines && yarn test
```

## (Hardhat) L2 System contracts

Run the test-node from the `code/system-contracts` in the first terminal:
```
yarn # if first time
yarn test-node
```

Then run the tests in the second:
```
yarn test
```

> :warning: you should rerun the test-node for every tests run

## (Hardhat) L2 contracts

Run the test-node from the `code/system-contracts` in the first terminal:
```
yarn # if first time
yarn test-node
```

Then run the tests from the `code/contracts/zksync` in the second:
```
yarn # if first time
yarn test
```

> :warning: you should rerun the test-node for every tests run

## Miscellaneous

Employees of zkSync and employees' family members are ineligible to participate in this audit.
