# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 35 |
| [GAS-2](#GAS-2) | Comparing to a Boolean constant | 1 |
| [GAS-3](#GAS-3) | Using bools for storage incurs overhead | 8 |
| [GAS-4](#GAS-4) | Cache array length outside of loop | 11 |
| [GAS-5](#GAS-5) | For Operations that will not overflow, you could use unchecked | 459 |
| [GAS-6](#GAS-6) | Use Custom Errors instead of Revert Strings to save Gas | 250 |
| [GAS-7](#GAS-7) | Avoid contract existence checks by using low level calls | 10 |
| [GAS-8](#GAS-8) | Functions guaranteed to revert when called by normal users can be marked `payable` | 55 |
| [GAS-9](#GAS-9) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 7 |
| [GAS-10](#GAS-10) | Using `private` rather than `public` for constants, saves gas | 5 |
| [GAS-11](#GAS-11) | Use shift right/left instead of division/multiplication if possible | 17 |
| [GAS-12](#GAS-12) | Splitting require() statements that use && saves gas | 2 |
| [GAS-13](#GAS-13) | Use of `this` instead of marking as `public` an `external` function | 1 |
| [GAS-14](#GAS-14) | `uint256` to `bool` `mapping`: Utilizing Bitmaps to dramatically save on Gas | 4 |
| [GAS-15](#GAS-15) | Increments/decrements can be unchecked in for-loops | 17 |
| [GAS-16](#GAS-16) | Use != 0 instead of > 0 for unsigned integer comparison | 25 |
| [GAS-17](#GAS-17) | WETH address definition can be use directly | 1 |
### <a name="GAS-1"></a>[GAS-1] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (35)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

160:             chainBalance[_chainId][_l1Token] += _amount;

207:             chainBalance[_chainId][_l1Token] += amount;

563:             chainBalance[ERA_CHAIN_ID][_l1Token] += _amount;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

602:         for (uint256 i = 0; i < _pubdataCommitments.length; i += PUBDATA_COMMITMENT_SIZE) {

623:             versionedHashIndex += 1;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

83:             costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);

86:             costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;

98:             costForPubdata += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

97:                 vInt += 8 + block.chainid * 2;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

61:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

127:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

141:             stateDiffPtr += 32;

153:             stateDiffPtr += len;

159:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

172:             stateDiffPtr += _enumerationIndexSize;

175:             stateDiffPtr += 1;

184:             stateDiffPtr += len;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

271:             sumOfValues += _deployments[i].value;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

206:         calldataPtr += 4;

214:             calldataPtr += L2_TO_L1_LOG_SERIALIZE_SIZE;

238:         calldataPtr += 4;

242:             calldataPtr += 4;

246:             calldataPtr += currentMessageLength;

256:         calldataPtr += 4;

262:             calldataPtr += 4;

271:             calldataPtr += currentBytecodeLength;

291:         calldataPtr += 3;

298:         calldataPtr += compressedStateDiffSize;

305:         calldataPtr += 4;

309:         calldataPtr += numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

46:             balance[_to] += _amount;

65:         totalSupply += _amount;

66:         balance[_account] += _amount;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

272:         virtualBlockInfo.number += _maxVirtualBlocksToCreate;

460:         txNumberInBlock += 1;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="GAS-2"></a>[GAS-2] Comparing to a Boolean constant
Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value.

Consider using `if(directValue)` instead of `if(directValue == true)` and `if(!directValue)` instead of `if(directValue == false)`

*Instances (1)*:
```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

68:         require(validators[_chainId][msg.sender] == true, "ValidatorTimelock: only validator");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

### <a name="GAS-3"></a>[GAS-3] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (8)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

27:     mapping(uint256 l2BatchNumber => mapping(uint256 l2ToL1MessageNumber => bool isFinalized))

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

57:     mapping(uint256 chainId => mapping(uint256 l2BatchNumber => mapping(uint256 l2ToL1MessageNumber => bool isFinalized)))

61:     mapping(uint256 chainId => bool enabled) internal hyperbridgingEnabled;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

21:     mapping(address _stateTransitionManager => bool) public stateTransitionManagerIsRegistered;

23:     mapping(address _token => bool) public tokenIsRegistered;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

50:     mapping(uint256 _chainId => mapping(address _validator => bool)) public validators;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol

74:     mapping(address validatorAddress => bool isValidator) validators;

114:     mapping(uint256 l2BatchNumber => mapping(uint256 l2ToL1MessageNumber => bool isFinalized)) isEthWithdrawalFinalized;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol)

### <a name="GAS-4"></a>[GAS-4] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (11)*:
```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

225:         for (uint256 i = 0; i < _calls.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

116:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

135:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

185:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

209:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

137:         for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

252:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

284:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

602:         for (uint256 i = 0; i < _pubdataCommitments.length; i += PUBDATA_COMMITMENT_SIZE) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

226:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

61:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

### <a name="GAS-5"></a>[GAS-5] For Operations that will not overflow, you could use unchecked

*Instances (459)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

141:         require(_amount != 0, "0T"); // empty deposit

143:         require(amount == _amount, "3T"); // The token has non-standard transfer logic

165:         return balanceAfter - balanceBefore;

186:         require(amount != 0, "2T"); // empty deposit

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

125:             require(balanceAfter - balanceBefore == amount, "ShB: wrong amount transferred");

126:             chainBalance[ERA_CHAIN_ID][_token] = chainBalance[ERA_CHAIN_ID][_token] + amount;

133:         chainBalance[ERA_CHAIN_ID][ETH_TOKEN_ADDRESS] = chainBalance[ERA_CHAIN_ID][ETH_TOKEN_ADDRESS] + msg.value;

155:             uint256 amount = _depositFunds(_prevMsgSender, IERC20(_l1Token), _amount); // note if _prevMsgSender is this contract, this will return 0. This does not happen.

156:             require(amount == _amount, "3T"); // The token has non-standard transfer logic

160:             chainBalance[_chainId][_l1Token] += _amount;

173:         return balanceAfter - balanceBefore;

180:         uint256, // l2Value, needed for Weth deposits in the future

201:             require(withdrawAmount == _depositAmount, "5T"); // The token has non-standard transfer logic

203:         require(amount != 0, "6T"); // empty deposit amount

207:             chainBalance[_chainId][_l1Token] += amount;

254:             return abi.encode(name, symbol, decimals); // when depositing eth to a non-eth based chain it is an ERC20

345:             chainBalance[_chainId][_l1Token] -= _amount;

434:             require(chainBalance[_chainId][l1Token] >= amount, "ShB not enough funds 2"); // not enought funds

435:             chainBalance[_chainId][l1Token] -= amount;

479:         require(success, "ShB withd w proof"); // withdrawal wrong proof

496:         require(_l2ToL1message.length >= 56, "ShB wrong msg len"); // wrong messsage length

563:             chainBalance[ERA_CHAIN_ID][_l1Token] += _amount;

582:                 mintValue: msg.value, // l2 gas + l2 msg.Value the bridgehub will withdraw the mintValue from the base token bridge for gas

583:                 l2Value: 0, // L2 msg.value, this contract doesn't support base token deposits or wrapping functionality, for direct deposits use bridgehub

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL1ERC20Bridge.sol

5: import "./IL1SharedBridge.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL1SharedBridge.sol

5: import {L2TransactionRequestTwoBridgesInner} from "../../bridgehub/IBridgehub.sol";

6: import {IBridgehub} from "../../bridgehub/IBridgehub.sol";

7: import {IL1ERC20Bridge} from "./IL1ERC20Bridge.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

98:         uint256, //_salt

205:                 require(msg.value == 0, "Bridgehub: non-eth bridge with msg.value");

250:                     msg.value == _request.mintValue + _request.secondBridgeValue,

283:         ); // to avoid calls to precompiles

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/IBridgehub.sol

5: import {IL1SharedBridge} from "../bridge/interfaces/IL1SharedBridge.sol";

6: import "../common/Messaging.sol";

7: import "../state-transition/IStateTransitionManager.sol";

8: import "../state-transition/libraries/Diamond.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/IBridgehub.sol)

```solidity
File: contracts/ethereum/contracts/common/Config.sol

14: uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

112: bytes32 constant TWO_BRIDGES_MAGIC_VALUE = bytes32(uint256(keccak256("TWO_BRIDGES_MAGIC_VALUE")) - 1);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Config.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

25:         uint256 bytecodeLenInWords = _bytecode.length / 32;

26:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

27:         require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

41:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

43:         require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd

50:         codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/UncheckedMath.sol

13:             return _number + 1;

19:             return _lhs + _rhs;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

219:         timestamps[_id] = block.timestamp + _delay;

225:         for (uint256 i = 0; i < _calls.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol

5: import {Diamond} from "./libraries/Diamond.sol";

6: import {L2CanonicalTransaction} from "../common/Messaging.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

94:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

116:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

135:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

183:         uint256 delay = executionDelay; // uint32

185:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

195:                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed

207:         uint256 delay = executionDelay; // uint32

209:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

217:                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

5: import {Diamond} from "../libraries/Diamond.sol";

6: import {ZkSyncStateTransitionBase} from "./facets/ZkSyncStateTransitionBase.sol";

7: import {FeeParams} from "./ZkSyncStateTransitionStorage.sol";

8: import {L2_TO_L1_LOG_SERIALIZE_SIZE, MAX_GAS_PER_TRANSACTION} from "../../common/Config.sol";

9: import {InitializeData, IDiamondInit} from "../chain-interfaces/IDiamondInit.sol";

10: import {VerifierParams} from "../chain-interfaces/IVerifier.sol";

12: import "../l2-deps/ISystemContext.sol";

51:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol

5: import {Diamond} from "../libraries/Diamond.sol";

30:         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

31:         require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol

5: import {IVerifier, VerifierParams} from "../chain-interfaces/IVerifier.sol";

6: import {PriorityQueue} from "../../state-transition/libraries/PriorityQueue.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

5: import {IAdmin} from "../../chain-interfaces/IAdmin.sol";

6: import {Diamond} from "../../libraries/Diamond.sol";

7: import {MAX_GAS_PER_TRANSACTION} from "../../../common/Config.sol";

8: import {FeeParams} from "../ZkSyncStateTransitionStorage.sol";

9: import {ZkSyncStateTransitionBase} from "./ZkSyncStateTransitionBase.sol";

10: import {IStateTransitionManager} from "../../IStateTransitionManager.sol";

13: import {IZkSyncStateTransitionBase} from "../../chain-interfaces/IZkSyncStateTransitionBase.sol";

34:         require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

130:         require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

140:         require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

5: import {ZkSyncStateTransitionBase} from "./ZkSyncStateTransitionBase.sol";

6: import {COMMIT_TIMESTAMP_NOT_OLDER, COMMIT_TIMESTAMP_APPROXIMATION_DELTA, EMPTY_STRING_KECCAK, L2_TO_L1_LOG_SERIALIZE_SIZE, MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, PACKED_L2_BLOCK_TIMESTAMP_MASK, PUBLIC_INPUT_SHIFT, POINT_EVALUATION_PRECOMPILE_ADDR} from "../../../common/Config.sol";

7: import {IExecutor, L2_LOG_ADDRESS_OFFSET, L2_LOG_KEY_OFFSET, L2_LOG_VALUE_OFFSET, SystemLogKey, LogProcessingOutput, PubdataSource, BLS_MODULUS, PUBDATA_COMMITMENT_SIZE, PUBDATA_COMMITMENT_CLAIMED_VALUE_OFFSET, PUBDATA_COMMITMENT_COMMITMENT_OFFSET, MAX_NUMBER_OF_BLOBS} from "../../chain-interfaces/IExecutor.sol";

8: import {PriorityQueue, PriorityOperation} from "../../libraries/PriorityQueue.sol";

9: import {UncheckedMath} from "../../../common/libraries/UncheckedMath.sol";

10: import {UnsafeBytes} from "../../../common/libraries/UnsafeBytes.sol";

11: import {VerifierParams} from "../../chain-interfaces/IVerifier.sol";

12: import {L2_BOOTLOADER_ADDRESS, L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, L2_PUBDATA_CHUNK_PUBLISHER_ADDR} from "../../../common/L2ContractAddresses.sol";

13: import {IStateTransitionManager} from "../../IStateTransitionManager.sol";

16: import {IZkSyncStateTransitionBase} from "../../chain-interfaces/IZkSyncStateTransitionBase.sol";

36:         require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

58:                     keccak256(_newBatch.pubdataCommitments[1:_newBatch.pubdataCommitments.length - 32]),

63:                 _newBatch.pubdataCommitments[_newBatch.pubdataCommitments.length - 32:_newBatch

117:         require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small

118:         require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

139:             (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);

140:             (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);

141:             (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

203:         uint256, // _chainId

228:         require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data

242:         s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;

318:         require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order

321:             "exe10" // executing batch should be committed

325:         require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x"); // priority operations hash does not match to expected

351:         uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;

353:         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

373:         uint256, // _chainId

444:         require(successVerifyProof, "p"); // Proof verification fail

477:         require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch

478:         require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

515:                 uint64(0), // index repeated storage changes in zkPorter

516:                 bytes32(0) // zkPorter batch hash

598:         require(_pubdataCommitments.length <= PUBDATA_COMMITMENT_SIZE * MAX_NUMBER_OF_BLOBS, "bd");

602:         for (uint256 i = 0; i < _pubdataCommitments.length; i += PUBDATA_COMMITMENT_SIZE) {

610:                 uint256(uint128(bytes16(_pubdataCommitments[i:i + PUBDATA_COMMITMENT_CLAIMED_VALUE_OFFSET])))

616:                 _pubdataCommitments[i + PUBDATA_COMMITMENT_CLAIMED_VALUE_OFFSET:i + PUBDATA_COMMITMENT_SIZE]

621:                 abi.encodePacked(blobVersionedHash, _pubdataCommitments[i:i + PUBDATA_COMMITMENT_COMMITMENT_OFFSET])

623:             versionedHashIndex += 1;

633:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

5: import {ZkSyncStateTransitionBase} from "./ZkSyncStateTransitionBase.sol";

6: import {VerifierParams} from "../../../state-transition/chain-interfaces/IVerifier.sol";

7: import {Diamond} from "../../libraries/Diamond.sol";

8: import {PriorityQueue, PriorityOperation} from "../../../state-transition/libraries/PriorityQueue.sol";

9: import {UncheckedMath} from "../../../common/libraries/UncheckedMath.sol";

10: import {IGetters} from "../../chain-interfaces/IGetters.sol";

11: import {ILegacyGetters} from "../../chain-interfaces/ILegacyGetters.sol";

14: import {IZkSyncStateTransitionBase} from "../../chain-interfaces/IZkSyncStateTransitionBase.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

154:         return l2GasPrice * _l2GasLimit;

164:         uint256 l1GasPriceConverted = (_l1GasPrice * s.baseTokenGasPriceMultiplierNominator) /

168:             pubdataPriceBaseToken = L1_GAS_PER_PUBDATA_BYTE * l1GasPriceConverted;

171:         uint256 batchOverheadBaseToken = uint256(feeParams.batchOverheadL1Gas) * l1GasPriceConverted;

172:         uint256 fullPubdataPriceBaseToken = pubdataPriceBaseToken +

173:             batchOverheadBaseToken /

176:         uint256 l2GasPrice = feeParams.minimalL2GasPrice + batchOverheadBaseToken / uint256(feeParams.maxL2GasPerBatch);

177:         uint256 minL2GasPriceBaseToken = (fullPubdataPriceBaseToken + _gasPerPubdata - 1) / _gasPerPubdata;

283:         uint256 baseCost = _params.l2GasPrice * _params.l2GasLimit;

284:         require(_mintValue >= baseCost + _params.l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

295:         _params.expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast

350:                 layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol

5: import {ZkSyncStateTransitionStorage} from "../ZkSyncStateTransitionStorage.sol";

6: import {ReentrancyGuard} from "../../../common/ReentrancyGuard.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol

5: import {IZkSyncStateTransitionBase} from "../chain-interfaces/IZkSyncStateTransitionBase.sol";

7: import {Diamond} from "../libraries/Diamond.sol";

8: import {FeeParams} from "../chain-deps/ZkSyncStateTransitionStorage.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol

5: import {L2CanonicalTransaction} from "../../common/Messaging.sol";

6: import {IVerifier, VerifierParams} from "./IVerifier.sol";

7: import {FeeParams} from "../chain-deps/ZkSyncStateTransitionStorage.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol

5: import {IZkSyncStateTransitionBase} from "./IZkSyncStateTransitionBase.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol

5: import {PriorityOperation} from "../libraries/PriorityQueue.sol";

6: import {VerifierParams} from "../chain-interfaces/IVerifier.sol";

7: import {IZkSyncStateTransitionBase} from "./IZkSyncStateTransitionBase.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol

5: import {IZkSyncStateTransitionBase} from "./IZkSyncStateTransitionBase.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IMailbox.sol

5: import {IZkSyncStateTransitionBase} from "./IZkSyncStateTransitionBase.sol";

6: import {L2CanonicalTransaction, L2Log, L2Message, TxStatus, BridgehubL2TransactionRequest} from "../../common/Messaging.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IMailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransition.sol

5: import {IAdmin} from "./IAdmin.sol";

6: import {IExecutor} from "./IExecutor.sol";

7: import {IGetters} from "./IGetters.sol";

8: import {IMailbox} from "./IMailbox.sol";

9: import {Verifier} from "../Verifier.sol";

10: import {VerifierParams} from "./IVerifier.sol";

13: import "../libraries/Diamond.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransition.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

18:         0x33774e659306e47509050e97cb651e731180a42d458212294d30751925c551a2; // keccak256("diamond.zksync.init") - 1

22:         0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b; // keccak256("diamond.standard.diamond.storage") - 1;

107:             require(selectors.length > 0, "B"); // no functions for diamond cut

116:                 revert("C"); // undefined diamond cut action

141:             require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

161:             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

175:         require(_facet == address(0), "a1"); // facet address must be zero

181:             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

233:         uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;

262:         uint256 lastFacetPosition = ds.facets.length - 1;

280:             require(_calldata.length == 0, "H"); // Non-empty calldata for zero address

287:                     revert("I"); // delegatecall failed

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/LibMap.sol

23:             uint256 mapValue = _map.map[_index / 8];

27:             uint256 bitOffset = (_index & 7) * 32;

43:             uint256 mapIndex = _index / 8;

48:             uint256 bitOffset = (_index & 7) * 32;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/LibMap.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Merkle.sol

5: import {UncheckedMath} from "../../common/libraries/UncheckedMath.sol";

33:             _index /= 2;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol

46:         return uint256(_queue.tail - _queue.head);

60:         _queue.tail = tail + 1;

65:         require(!_queue.isEmpty(), "D"); // priority queue is empty

73:         require(!_queue.isEmpty(), "s"); // priority queue is empty

80:         _queue.head = head + 1;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

30:         require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= _priorityTxMaxPubdata, "uk");

83:             costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);

86:             costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;

95:             costForPubdata = L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata;

98:             costForPubdata += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;

101:         return costForComputation + costForPubdata;

115:         require(_totalGasLimit >= overhead, "my"); // provided gas limit doesn't cover transaction overhead

118:             txBodyGasLimit = _totalGasLimit - overhead;

135:         uint256 overheadForLength = MEMORY_OVERHEAD_GAS * _encodingLength;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

5: import {ZkSyncStateTransitionBase} from "../state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol";

6: import {IMailbox} from "../state-transition/chain-interfaces/IMailbox.sol";

7: import {VerifierParams} from "../state-transition/chain-interfaces/IVerifier.sol";

8: import {IVerifier} from "../state-transition/chain-interfaces/IVerifier.sol";

9: import {L2ContractHelper} from "../common/libraries/L2ContractHelper.sol";

10: import {TransactionValidator} from "../state-transition/libraries/TransactionValidator.sol";

11: import {MAX_NEW_FACTORY_DEPS, SYSTEM_UPGRADE_L2_TX_TYPE, MAX_ALLOWED_PROTOCOL_VERSION_DELTA} from "../common/Config.sol";

12: import {L2CanonicalTransaction} from "../common/Messaging.sol";

226:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {

243:             _newProtocolVersion - previousProtocolVersion <= MAX_ALLOWED_PROTOCOL_VERSION_DELTA,

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol

5: import "../state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol";

6: import "../state-transition/chain-interfaces/IMailbox.sol";

7: import "../state-transition/chain-interfaces/IVerifier.sol";

8: import "../common/libraries/L2ContractHelper.sol";

9: import "../common/Messaging.sol";

10: import "../state-transition/libraries/TransactionValidator.sol";

11: import {MAX_NEW_FACTORY_DEPS, SYSTEM_UPGRADE_L2_TX_TYPE, MAX_ALLOWED_PROTOCOL_VERSION_DELTA} from "../common/Config.sol";

12: import {ProposedUpgrade, BaseZkSyncUpgrade} from "./BaseZkSyncUpgrade.sol";

28:             _newProtocolVersion - previousProtocolVersion <= MAX_ALLOWED_PROTOCOL_VERSION_DELTA,

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

5: import {Diamond} from "../state-transition/libraries/Diamond.sol";

6: import {BaseZkSyncUpgrade, ProposedUpgrade} from "./BaseZkSyncUpgrade.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/GenesisUpgrade.sol

5: import "../state-transition/libraries/Diamond.sol";

6: import "./BaseZkSyncUpgradeGenesis.sol";

7: import "./IDefaultUpgrade.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/GenesisUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/IDefaultUpgrade.sol

5: import {ProposedUpgrade} from "./BaseZkSyncUpgrade.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/IDefaultUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

30:             l2Address = address(uint160(l1Address) + offset);

40:             l1Address = address(uint160(l2Address) - offset);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

5: import "./interfaces/IAccountCodeStorage.sol";

6: import "./libraries/Utils.sol";

7: import {DEPLOYER_SYSTEM_CONTRACT, NONCE_HOLDER_SYSTEM_CONTRACT, CURRENT_MAX_PRECOMPILE_ADDRESS} from "./Constants.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

5: import {IBootloaderUtilities} from "./interfaces/IBootloaderUtilities.sol";

6: import {Transaction, TransactionHelper, EIP_712_TX_TYPE, LEGACY_TX_TYPE, EIP_2930_TX_TYPE, EIP_1559_TX_TYPE} from "./libraries/TransactionHelper.sol";

7: import {RLPEncoder} from "./libraries/RLPEncoder.sol";

8: import {EfficientCall} from "./libraries/EfficientCall.sol";

17:     using TransactionHelper for *;

97:                 vInt += 8 + block.chainid * 2;

105:             uint256 listLength = encodedNonce.length +

106:                 encodedGasParam.length +

107:                 encodedTo.length +

108:                 encodedValue.length +

109:                 encodedDataLength.length +

110:                 _transaction.data.length +

111:                 rEncoded.length +

112:                 sEncoded.length +

193:             vEncoded = RLPEncoder.encodeUint256(vInt - 27);

198:             uint256 listLength = encodedFixedLengthParams.length +

199:                 encodedDataLength.length +

200:                 _transaction.data.length +

201:                 encodedAccessListLength.length +

202:                 rEncoded.length +

203:                 sEncoded.length +

288:             vEncoded = RLPEncoder.encodeUint256(vInt - 27);

293:             uint256 listLength = encodedFixedLengthParams.length +

294:                 encodedDataLength.length +

295:                 _transaction.data.length +

296:                 encodedAccessListLength.length +

297:                 rEncoded.length +

298:                 sEncoded.length +

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

5: import {IComplexUpgrader} from "./interfaces/IComplexUpgrader.sol";

6: import {FORCE_DEPLOYER} from "./Constants.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

5: import {ICompressor, OPERATION_BITMASK, LENGTH_BITS_OFFSET, MAX_ENUMERATION_INDEX_SIZE} from "./interfaces/ICompressor.sol";

6: import {ISystemContract} from "./interfaces/ISystemContract.sol";

7: import {Utils} from "./libraries/Utils.sol";

8: import {UnsafeBytesCalldata} from "./libraries/UnsafeBytesCalldata.sol";

9: import {EfficientCall} from "./libraries/EfficientCall.sol";

10: import {L1_MESSENGER_CONTRACT, INITIAL_WRITE_STARTING_POSITION, COMPRESSED_INITIAL_WRITE_SIZE, STATE_DIFF_ENTRY_SIZE, STATE_DIFF_ENUM_INDEX_OFFSET, STATE_DIFF_FINAL_VALUE_OFFSET, STATE_DIFF_DERIVED_KEY_OFFSET, DERIVED_KEY_LENGTH, VALUE_LENGTH, ENUM_INDEX_LENGTH, KNOWN_CODE_STORAGE_CONTRACT} from "./Constants.sol";

52:                 encodedData.length * 4 == _bytecode.length,

57:                 dictionary.length / 8 <= encodedData.length / 2,

61:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

62:                 uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

66:                 uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

127:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

128:             bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];

135:             numInitialWritesProcessed++;

141:             stateDiffPtr += 32;

144:             stateDiffPtr++;

151:                 _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]

153:             stateDiffPtr += len;

159:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

160:             bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];

169:                 _compressedStateDiffs[stateDiffPtr:stateDiffPtr + _enumerationIndexSize]

172:             stateDiffPtr += _enumerationIndexSize;

175:             stateDiffPtr += 1;

182:                 _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]

184:             stateDiffPtr += len;

203:             dictionary = _rawCompressedData[2:2 + dictionaryLen * 8];

204:             encodedData = _rawCompressedData[2 + dictionaryLen * 8:];

233:                     _initialValue + convertedValue == _finalValue,

238:                     _initialValue - convertedValue == _finalValue,

253:         number >>= (256 - (_calldataSlice.length * 8));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/Constants.sol

5: import {IAccountCodeStorage} from "./interfaces/IAccountCodeStorage.sol";

6: import {INonceHolder} from "./interfaces/INonceHolder.sol";

7: import {IContractDeployer} from "./interfaces/IContractDeployer.sol";

8: import {IKnownCodesStorage} from "./interfaces/IKnownCodesStorage.sol";

9: import {IImmutableSimulator} from "./interfaces/IImmutableSimulator.sol";

10: import {IBaseToken} from "./interfaces/IBaseToken.sol";

11: import {IL1Messenger} from "./interfaces/IL1Messenger.sol";

12: import {ISystemContext} from "./interfaces/ISystemContext.sol";

13: import {ICompressor} from "./interfaces/ICompressor.sol";

14: import {IComplexUpgrader} from "./interfaces/IComplexUpgrader.sol";

15: import {IBootloaderUtilities} from "./interfaces/IBootloaderUtilities.sol";

16: import {IPubdataChunkPublisher} from "./interfaces/IPubdataChunkPublisher.sol";

20: uint160 constant SYSTEM_CONTRACTS_OFFSET = {{SYSTEM_CONTRACTS_OFFSET}}; // 2^15

24: uint160 constant MAX_SYSTEM_CONTRACT_ADDRESS = 0xffff; // 2^16 - 1

36: address payable constant BOOTLOADER_FORMAL_ADDRESS = payable(address(SYSTEM_CONTRACTS_OFFSET + 0x01));

38:     address(SYSTEM_CONTRACTS_OFFSET + 0x02)

40: INonceHolder constant NONCE_HOLDER_SYSTEM_CONTRACT = INonceHolder(address(SYSTEM_CONTRACTS_OFFSET + 0x03));

41: IKnownCodesStorage constant KNOWN_CODE_STORAGE_CONTRACT = IKnownCodesStorage(address(SYSTEM_CONTRACTS_OFFSET + 0x04));

43:     address(SYSTEM_CONTRACTS_OFFSET + 0x05)

45: IContractDeployer constant DEPLOYER_SYSTEM_CONTRACT = IContractDeployer(address(SYSTEM_CONTRACTS_OFFSET + 0x06));

49: address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);

50: IL1Messenger constant L1_MESSENGER_CONTRACT = IL1Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

51: address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

53: IBaseToken constant BASE_TOKEN_SYSTEM_CONTRACT = IBaseToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));

60: ISystemContext constant SYSTEM_CONTEXT_CONTRACT = ISystemContext(payable(address(SYSTEM_CONTRACTS_OFFSET + 0x0b)));

62: IBootloaderUtilities constant BOOTLOADER_UTILITIES = IBootloaderUtilities(address(SYSTEM_CONTRACTS_OFFSET + 0x0c));

65: address constant EVENT_WRITER_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x0d);

67: ICompressor constant COMPRESSOR_CONTRACT = ICompressor(address(SYSTEM_CONTRACTS_OFFSET + 0x0e));

69: IComplexUpgrader constant COMPLEX_UPGRADER_CONTRACT = IComplexUpgrader(address(SYSTEM_CONTRACTS_OFFSET + 0x0f));

72:     address(SYSTEM_CONTRACTS_OFFSET + 0x11)

80: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;

121: uint256 constant COMPRESSED_INITIAL_WRITE_SIZE = DERIVED_KEY_LENGTH + VALUE_LENGTH;

123: uint256 constant COMPRESSED_REPEATED_WRITE_SIZE = ENUM_INDEX_LENGTH + VALUE_LENGTH;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Constants.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

5: import {ImmutableData} from "./interfaces/IImmutableSimulator.sol";

6: import {IContractDeployer} from "./interfaces/IContractDeployer.sol";

7: import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, BASE_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT, COMPLEX_UPGRADER_CONTRACT, KECCAK256_SYSTEM_CONTRACT} from "./Constants.sol";

9: import {Utils} from "./libraries/Utils.sol";

10: import {EfficientCall} from "./libraries/EfficientCall.sol";

11: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";

12: import {ISystemContract} from "./interfaces/ISystemContract.sol";

187:         bytes32, // salt

270:         for (uint256 i = 0; i < deploymentsLength; ++i) {

271:             sumOfValues += _deployments[i].value;

275:         for (uint256 i = 0; i < deploymentsLength; ++i) {

292:             "Code hash is non-zero"

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

5: import "./interfaces/IAccount.sol";

6: import "./libraries/TransactionHelper.sol";

7: import "./libraries/SystemContractHelper.sol";

8: import "./libraries/EfficientCall.sol";

9: import {BOOTLOADER_FORMAL_ADDRESS, NONCE_HOLDER_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, INonceHolder} from "./Constants.sol";

20:     using TransactionHelper for *;

68:         bytes32, // _txHash

100:         require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");

113:         bytes32, // _txHash

114:         bytes32, // _suggestedSignedHash

197:         bytes32, // _txHash

198:         bytes32, // _suggestedSignedHash

213:         bytes32, // _txHash

214:         bytes32, // _suggestedSignedHash

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

5: import {IImmutableSimulator, ImmutableData} from "./interfaces/IImmutableSimulator.sol";

6: import {DEPLOYER_SYSTEM_CONTRACT} from "./Constants.sol";

38:             for (uint256 i = 0; i < immutablesLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

5: import {IKnownCodesStorage} from "./interfaces/IKnownCodesStorage.sol";

6: import {ISystemContract} from "./interfaces/ISystemContract.sol";

7: import {Utils} from "./libraries/Utils.sol";

8: import {COMPRESSOR_CONTRACT, L1_MESSENGER_CONTRACT} from "./Constants.sol";

30:             for (uint256 i = 0; i < hashesLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

5: import {IL1Messenger, L2ToL1Log, L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, L2_TO_L1_LOG_SERIALIZE_SIZE, STATE_DIFF_COMPRESSION_VERSION_NUMBER} from "./interfaces/IL1Messenger.sol";

6: import {ISystemContract} from "./interfaces/ISystemContract.sol";

7: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";

8: import {EfficientCall} from "./libraries/EfficientCall.sol";

9: import {Utils} from "./libraries/Utils.sol";

10: import {SystemLogKey, SYSTEM_CONTEXT_CONTRACT, KNOWN_CODE_STORAGE_CONTRACT, COMPRESSOR_CONTRACT, STATE_DIFF_ENTRY_SIZE, MAX_ALLOWED_PUBDATA_PER_BATCH, L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, PUBDATA_CHUNK_PUBLISHER} from "./Constants.sol";

51:         return KECCAK_ROUND_GAS_COST * (_length / KECCAK_ROUND_NUMBER_OF_BYTES + 1);

62:         return SHA256_ROUND_GAS_COST * ((_length + 8) / SHA256_ROUND_NUMBER_OF_BYTES + 1);

89:         uint256 gasToPay = keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) + 2 * keccakGasCost(64);

109:         numberOfLogsToProcess++;

119:         uint256 gasSpentOnMessageHashing = gasBeforeMessageHashing - gasleft();

143:             pubdataLen = 4 + _message.length + L2_TO_L1_LOG_SERIALIZE_SIZE;

151:         uint256 gasToPay = pubdataLen *

152:             gasPerPubdataBytes +

153:             keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) +

154:             3 *

155:             keccakGasCost(64) +

178:             pubdataLen = 4 + bytecodeLen;

182:         uint256 gasToPay = pubdataLen * gasPerPubdataBytes + sha256GasCost(bytecodeLen) + keccakGasCost(64);

204:         uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

205:         require(numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, "Too many L2->L1 logs");

206:         calldataPtr += 4;

210:         for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {

212:                 _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + L2_TO_L1_LOG_SERIALIZE_SIZE]

214:             calldataPtr += L2_TO_L1_LOG_SERIALIZE_SIZE;

222:         for (uint256 i = numberOfL2ToL1Logs; i < L2_TO_L1_LOGS_MERKLE_TREE_LEAVES; ++i) {

227:             nodesOnCurrentLevel /= 2;

228:             for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

230:                     abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])

237:         uint32 numberOfMessages = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

238:         calldataPtr += 4;

240:         for (uint256 i = 0; i < numberOfMessages; ++i) {

241:             uint32 currentMessageLength = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

242:             calldataPtr += 4;

244:                 _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + currentMessageLength]

246:             calldataPtr += currentMessageLength;

255:         uint32 numberOfBytecodes = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

256:         calldataPtr += 4;

258:         for (uint256 i = 0; i < numberOfBytecodes; ++i) {

260:                 bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4])

262:             calldataPtr += 4;

267:                         _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + currentBytecodeLength]

271:             calldataPtr += currentBytecodeLength;

288:         calldataPtr++;

290:         uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]));

291:         calldataPtr += 3;

294:         calldataPtr++;

296:         bytes calldata compressedStateDiffs = _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr +

298:         calldataPtr += compressedStateDiffSize;

304:         uint32 numberOfStateDiffs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

305:         calldataPtr += 4;

307:         bytes calldata stateDiffs = _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr +

308:             (numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE)];

309:         calldataPtr += numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

5: import {IBaseToken} from "./interfaces/IBaseToken.sol";

6: import {ISystemContract} from "./interfaces/ISystemContract.sol";

7: import {MSG_VALUE_SYSTEM_CONTRACT, DEPLOYER_SYSTEM_CONTRACT, BOOTLOADER_FORMAL_ADDRESS, L1_MESSENGER_CONTRACT} from "./Constants.sol";

8: import {IMailbox} from "./interfaces/IMailbox.sol";

43:             balance[_from] = fromBalance - _amount;

46:             balance[_to] += _amount;

65:         totalSupply += _amount;

66:         balance[_account] += _amount;

106:             balance[address(this)] -= amount;

107:             totalSupply -= amount;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

5: import {Utils} from "./libraries/Utils.sol";

6: import {EfficientCall} from "./libraries/EfficientCall.sol";

7: import {ISystemContract} from "./interfaces/ISystemContract.sol";

8: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";

9: import {MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT, BASE_TOKEN_SYSTEM_CONTRACT} from "./Constants.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

5: import {INonceHolder} from "./interfaces/INonceHolder.sol";

6: import {IContractDeployer} from "./interfaces/IContractDeployer.sol";

7: import {ISystemContract} from "./interfaces/ISystemContract.sol";

8: import {DEPLOYER_SYSTEM_CONTRACT} from "./Constants.sol";

28:     uint256 private constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;

31:     uint256 private constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;

72:             rawNonces[addressAsKey] = (oldRawNonce + _value);

89:             require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");

118:             rawNonces[addressAsKey] = oldRawNonce + 1;

144:             rawNonces[addressAsKey] = (oldRawNonce + DEPLOY_NONCE_MULTIPLIER);

180:         deploymentNonce = _rawMinNonce / DEPLOY_NONCE_MULTIPLIER;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

4: import {IPubdataChunkPublisher} from "./interfaces/IPubdataChunkPublisher.sol";

5: import {ISystemContract} from "./interfaces/ISystemContract.sol";

6: import {L1_MESSENGER_CONTRACT, BLOB_SIZE_BYTES, MAX_NUMBER_OF_BLOBS} from "./Constants.sol";

7: import {EfficientCall} from "./libraries/EfficientCall.sol";

8: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";

9: import {SystemLogKey} from "./Constants.sol";

22:         require(_pubdata.length <= BLOB_SIZE_BYTES * MAX_NUMBER_OF_BLOBS, "pubdata should fit in 2 blobs");

28:         bytes memory totalBlobs = new bytes(BLOB_SIZE_BYTES * MAX_NUMBER_OF_BLOBS);

36:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

37:             uint256 start = BLOB_SIZE_BYTES * i;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

5: import {ISystemContext} from "./interfaces/ISystemContext.sol";

6: import {ISystemContract} from "./interfaces/ISystemContract.sol";

7: import {ISystemContextDeprecated} from "./interfaces/ISystemContextDeprecated.sol";

8: import {SystemContractHelper} from "./libraries/SystemContractHelper.sol";

9: import {BOOTLOADER_FORMAL_ADDRESS, SystemLogKey} from "./Constants.sol";

121:         if (blockNumber <= _block || blockNumber - _block > 256) {

225:             bytes32 correctPrevBlockHash = _calculateLegacyL2BlockHash(_l2BlockNumber - 1);

232:             _setL2BlockHash(_l2BlockNumber - 1, correctPrevBlockHash);

265:             _maxVirtualBlocksToCreate -= 1;

272:         virtualBlockInfo.number += _maxVirtualBlocksToCreate;

296:         _setL2BlockHash(_l2BlockNumber - 1, _prevL2BlockHash);

347:                 _expectedPrevL2BlockHash == _getLatest257L2blockHash(_l2BlockNumber - 1),

351:         } else if (currentL2BlockNumber + 1 == _l2BlockNumber) {

353:             bytes32 prevL2BlockHash = _getLatest257L2blockHash(currentL2BlockNumber - 1);

429:         require(previousBatchNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

436:         BlockInfo memory newBlockInfo = BlockInfo({number: previousBatchNumber + 1, timestamp: _newTimestamp});

460:         txNumberInBlock += 1;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="GAS-6"></a>[GAS-6] Use Custom Errors instead of Revert Strings to save Gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

Additionally, custom errors can be used inside and outside of contracts (including interfaces and libraries).

Source: <https://blog.soliditylang.org/2021/04/21/custom-errors/>:

> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Consider replacing **all revert strings** with custom errors in the solution, and particularly those that have multiple occurrences:

*Instances (250)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

64:         require(msg.sender == address(sharedBridge), "Not shared bridge");

66:         require(amount == _amount, "Incorrect amount");

141:         require(_amount != 0, "0T"); // empty deposit

143:         require(amount == _amount, "3T"); // The token has non-standard transfer logic

186:         require(amount != 0, "2T"); // empty deposit

215:         require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

69:         require(msg.sender == address(bridgehub), "ShB not BH");

84:         require(msg.sender == address(legacyBridge), "ShB not legacy bridge");

108:         require(_owner != address(0), "ShB owner 0");

122:             require(amount > 0, "ShB: 0 amount to transfer");

125:             require(balanceAfter - balanceBefore == amount, "ShB: wrong amount transferred");

132:         require(msg.sender == ERA_DIAMOND_PROXY, "ShB: not era diamond proxy");

150:             require(msg.value == _amount, "L1SharedBridge: msg.value not equal to amount");

153:             require(msg.value == 0, "ShB m.v > 0 b d.it");

156:             require(amount == _amount, "3T"); // The token has non-standard transfer logic

183:         require(l2BridgeAddress[_chainId] != address(0), "ShB l2 bridge not deployed");

189:         require(_l1Token != l1WethAddress, "ShB: WETH deposit not supported");

190:         require(bridgehub.baseToken(_chainId) != _l1Token, "ShB: baseToken deposit not supported");

195:             require(_depositAmount == 0, "ShB wrong withdraw amount");

197:             require(msg.value == 0, "ShB m.v > 0 for BH d.it 2");

201:             require(withdrawAmount == _depositAmount, "5T"); // The token has non-standard transfer logic

203:         require(amount != 0, "6T"); // empty deposit amount

232:         require(depositHappened[_chainId][_txHash] == 0x00, "ShB tx hap");

320:             require(proofValid, "yn");

322:         require(_amount > 0, "y1");

337:                 require(dataHash == txDataHash, "ShB: d.it not hap");

344:             require(chainBalance[_chainId][_l1Token] >= _amount, "ShB n funds");

355:             require(callSuccess, "ShB: claimFailedDeposit failed");

391:             require(!legacyBridge.isWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex), "ShB: legacy withdrawal");

412:         require(!isWithdrawalFinalized[_chainId][_l2BatchNumber][_l2MessageIndex], "Withdrawal is already finalized");

422:             require(!alreadyFinalized, "Withdrawal is already finalized 2");

434:             require(chainBalance[_chainId][l1Token] >= amount, "ShB not enough funds 2"); // not enought funds

444:             require(callSuccess, "ShB: withdraw failed");

479:         require(success, "ShB withd w proof"); // withdrawal wrong proof

496:         require(_l2ToL1message.length >= 56, "ShB wrong msg len"); // wrong messsage length

512:             require(_l2ToL1message.length == 76, "ShB wrong msg len 2");

517:             revert("ShB Incorrect message function selector");

558:         require(l2BridgeAddress[ERA_CHAIN_ID] != address(0), "ShB b. n dep");

559:         require(_l1Token != l1WethAddress, "ShB: WETH deposit not supported 2");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

43:         require(msg.sender == deployer || msg.sender == owner(), "Bridgehub: not owner or deployer");

82:         require(!tokenIsRegistered[_token], "Bridgehub: token already registered");

102:         require(_chainId != 0, "Bridgehub: chainId cannot be 0");

103:         require(_chainId <= type(uint48).max, "Bridgehub: chainId too large");

109:         require(tokenIsRegistered[_baseToken], "Bridgehub: token not registered");

110:         require(address(sharedBridge) != address(0), "Bridgehub: weth bridge not set");

112:         require(stateTransitionManager[_chainId] == address(0), "Bridgehub: chainId already registered");

203:                 require(msg.value == _request.mintValue, "Bridgehub: msg.value mismatch 1");

205:                 require(msg.value == 0, "Bridgehub: non-eth bridge with msg.value");

255:                 require(msg.value == _request.secondBridgeValue, "Bridgehub: msg.value mismatch 3");

276:         require(outputRequest.magicValue == TWO_BRIDGES_MAGIC_VALUE, "Bridgehub: magic value mismatch");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/common/ReentrancyGuard.sol

58:         require(lockSlotOldValue == 0, "1B");

75:         require(_status == _NOT_ENTERED, "r1");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

23:         require(_bytecode.length % 32 == 0, "pq");

26:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

27:         require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

41:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

43:         require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

42:         require(_admin != address(0), "Admin should be non zero address");

59:         require(msg.sender == address(this), "Only governance contract itself is allowed to call this function");

65:         require(msg.sender == securityCouncil, "Only security council is allowed to call this function");

155:         require(isOperationPending(_id), "Operation must be pending");

172:         require(isOperationReady(id), "Operation must be ready before execution");

177:         require(isOperationReady(id), "Operation must be ready after execution");

191:         require(isOperationPending(id), "Operation must be pending before execution");

196:         require(isOperationPending(id), "Operation must be pending after execution");

216:         require(!isOperation(_id), "Operation with this proposal id already exists");

217:         require(_delay >= minDelay, "Proposed delay is less than minimum delay");

240:         require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

58:         require(msg.sender == bridgehub, "StateTransition: only bridgehub");

70:         require(_initializeData.governor != address(0), "StateTransition: governor zero");

204:         require(cutHashInput == initialCutHash, "StateTransition: initial cutHash mismatch");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

62:         require(msg.sender == stateTransitionManager.getChainAdmin(_chainId), "ValidatorTimelock: only chain admin");

68:         require(validators[_chainId][msg.sender] == true, "ValidatorTimelock: only validator");

195:                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed

217:                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

25:         require(address(_initializeData.verifier) != address(0), "vt");

26:         require(_initializeData.admin != address(0), "vy");

27:         require(_initializeData.validatorTimelock != address(0), "hc");

28:         require(_initializeData.priorityTxMaxGasLimit <= MAX_GAS_PER_TRANSACTION, "vu");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol

14:         require(_chainId == block.chainid, "pr");

25:         require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

30:         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

31:         require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

34:         require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

59:         require(_newPriorityTxMaxGasLimit <= MAX_GAS_PER_TRANSACTION, "n5");

70:         require(_newFeeParams.maxPubdataPerBatch >= _newFeeParams.priorityTxMaxPubdata, "n6");

130:         require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

140:         require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

36:         require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

39:         require(pubdataSource == uint8(PubdataSource.Calldata) || pubdataSource == uint8(PubdataSource.Blob), "us");

69:         require(_previousBatch.batchHash == logOutput.previousBatchHash, "l");

71:         require(logOutput.chainedPriorityTxsHash == _newBatch.priorityOperationsHash, "t");

73:         require(logOutput.numberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");

105:         require(batchTimestamp == _expectedBatchTimestamp, "tb");

109:         require(_previousBatchTimestamp < batchTimestamp, "h3");

117:         require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small

118:         require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

144:             require(!_checkBit(processedLogs, uint8(logKey)), "kp");

149:                 require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");

152:                 require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln");

155:                 require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb");

158:                 require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc");

161:                 require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv");

164:                 require(logSender == L2_BOOTLOADER_ADDRESS, "bl");

167:                 require(logSender == L2_BOOTLOADER_ADDRESS, "bk");

170:                 require(logSender == L2_PUBDATA_CHUNK_PUBLISHER_ADDR, "pc");

173:                 require(logSender == L2_PUBDATA_CHUNK_PUBLISHER_ADDR, "pd");

176:                 require(logSender == L2_BOOTLOADER_ADDRESS, "bu");

177:                 require(_expectedSystemContractUpgradeTxHash == logValue, "ut");

179:                 revert("ul");

187:             require(processedLogs == 511, "b7");

189:             require(processedLogs == 1023, "b8");

226:         require(_newBatchesData.length == 1, "e4");

228:         require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data

279:         require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");

318:         require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order

325:         require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x"); // priority operations hash does not match to expected

353:         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

397:         require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

416:         require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");

437:         require(proofPublicInput.length == 1, "t4");

444:         require(successVerifyProof, "p"); // Proof verification fail

477:         require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch

478:         require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

530:         require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu");

582:         require(success, "failed to call point evaluation precompile");

584:         require(result == BLS_MODULUS, "precompile unexpected output");

597:         require(_pubdataCommitments.length > 0, "pl");

598:         require(_pubdataCommitments.length <= PUBDATA_COMMITMENT_SIZE * MAX_NUMBER_OF_BLOBS, "bd");

599:         require(_pubdataCommitments.length % PUBDATA_COMMITMENT_SIZE == 0, "bs");

605:             require(blobVersionedHash != bytes32(0), "vh");

629:         require(versionedHash == bytes32(0), "lh");

647:         require(success, "vc");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

182:         require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

39:         require(s.chainId == ERA_CHAIN_ID, "transferEthToSharedBridge only available for Era on mailbox");

48:         require(callSuccess, "ShB: transferEthToSharedBridge failed");

115:         require(_batchNumber <= s.totalBatchesExecuted, "xx");

122:         require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

163:         require(s.baseTokenGasPriceMultiplierDenominator > 0, "Mailbox: baseTokenGasPriceDenominator not set");

190:         require(s.chainId == ERA_CHAIN_ID, "finalizeEthWithdrawal only available for Era on mailbox");

211:         require(s.chainId == ERA_CHAIN_ID, "legacy interface only available for eth base token");

248:         require(_request.l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

276:         require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");

284:         require(_mintValue >= baseCost + _params.l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol

16:         require(msg.sender == s.admin, "StateTransition Chain: not admin");

22:         require(s.validators[msg.sender], "StateTransition Chain: not validator");

27:         require(msg.sender == s.stateTransitionManager, "StateTransition Chain: not state transition manager");

32:         require(msg.sender == s.bridgehub, "StateTransition Chain: not bridgehub");

53:         require(msg.sender == s.baseTokenBridge, "Only shared bridge can call this function");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

107:             require(selectors.length > 0, "B"); // no functions for diamond cut

116:                 revert("C"); // undefined diamond cut action

132:         require(_facet.code.length > 0, "G");

141:             require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

155:         require(_facet.code.length > 0, "K");

161:             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

175:         require(_facet == address(0), "a1"); // facet address must be zero

181:             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

215:             require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");

280:             require(_calldata.length == 0, "H"); // Non-empty calldata for zero address

287:                     revert("I"); // delegatecall failed

297:             require(data.length == 32, "lp");

298:             require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Merkle.sol

24:         require(pathLength > 0, "xc");

25:         require(pathLength < 256, "bt");

26:         require(_index < (1 << pathLength), "px");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol

65:         require(!_queue.isEmpty(), "D"); // priority queue is empty

73:         require(!_queue.isEmpty(), "s"); // priority queue is empty

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

28:         require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");

30:         require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= _priorityTxMaxPubdata, "uk");

48:         require(_transaction.from <= type(uint16).max, "ua");

49:         require(_transaction.to <= type(uint160).max, "ub");

50:         require(_transaction.paymaster == 0, "uc");

51:         require(_transaction.value == 0, "ud");

52:         require(_transaction.maxFeePerGas == 0, "uq");

53:         require(_transaction.maxPriorityFeePerGas == 0, "ux");

54:         require(_transaction.reserved[0] == 0, "ue");

55:         require(_transaction.reserved[1] <= type(uint160).max, "uf");

56:         require(_transaction.reserved[2] == 0, "ug");

57:         require(_transaction.reserved[3] == 0, "uo");

58:         require(_transaction.signature.length == 0, "uh");

59:         require(_transaction.paymasterInput.length == 0, "ul1");

60:         require(_transaction.reservedDynamic.length == 0, "um");

115:         require(_totalGasLimit >= overhead, "my"); // provided gas limit doesn't cover transaction overhead

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

72:         require(block.timestamp >= _proposedUpgrade.upgradeTimestamp, "Upgrade is not ready yet");

190:         require(_l2ProtocolUpgradeTx.txType == SYSTEM_UPGRADE_L2_TX_TYPE, "L2 system upgrade tx type is wrong");

223:         require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");

224:         require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

249:         require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol

33:         require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized");

52:         require(block.timestamp >= _proposedUpgrade.upgradeTimestamp, "Upgrade is not ready yet");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

26:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

37:         require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");

48:         require(Utils.isContractConstructed(_hash), "Code hash is not for a constructed contract");

57:         require(Utils.isContractConstructing(codeHash), "Code hash is not for a contract on constructor");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

37:             revert("Unsupported tx type");

92:             require(vInt == 27 || vInt == 28, "Invalid v value");

191:             require(vInt == 27 || vInt == 28, "Invalid v value");

286:             require(vInt == 27 || vInt == 28, "Invalid v value");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

22:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER");

24:         require(_delegateTo.code.length > 0, "Delegatee is an EOA");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

63:                 require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

68:                 require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");

119:         require(_enumerationIndexSize <= MAX_ENUMERATION_INDEX_SIZE, "enumeration index size is too large");

140:             require(derivedKey == _compressedStateDiffs.readBytes32(stateDiffPtr), "iw: initial key mismatch");

156:         require(numInitialWritesProcessed == numberOfInitialWrites, "Incorrect number of initial storage diffs");

171:             require(enumIndex == compressedEnumIndex, "rw: enum key mismatch");

187:         require(stateDiffPtr == _compressedStateDiffs.length, "Extra data in _compressedStateDiffs");

230:                 require(convertedValue == _finalValue, "transform or no compression: compressed and final mismatch");

242:                 revert("unsupported operation");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

29:         require(msg.sender == address(this), "Callable only by self");

273:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

286:         require(_bytecodeHash != bytes32(0x0), "BytecodeHash cannot be zero");

287:         require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");

295:         require(NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(_newAddress) == 0x00, "Account is occupied");

325:         require(knownCodeMarker > 0, "The code hash is not known");

372:             require(value == 0, "The value must be zero if we do not call the constructor");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

100:         require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");

160:         require(_signature.length == 65, "Signature length is incorrect");

173:         require(v == 27 || v == 28, "v is neither 27 nor 28");

184:         require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");

202:         require(success, "Failed to pay the fee to the operator");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

35:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

20:         require(msg.sender == address(COMPRESSOR_CONTRACT), "Callable only by the compressor");

76:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

78:         require(Utils.bytecodeLenInWords(_bytecodeHash) % 2 == 1, "Code length in words must be odd");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

205:         require(numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, "Too many L2->L1 logs");

302:         require(calldataPtr <= MAX_ALLOWED_PUBDATA_PER_BATCH, "L1 Messenger pubdata is too long");

319:         require(calldataPtr == _totalL2ToL1PubdataAndStateDiffs.length, "Extra data in the totalL2ToL1Pubdata array");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

41:         require(fromBalance >= _amount, "Transfer amount exceeds balance");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

43:         require(to != address(this), "MsgValueSimulator calls itself");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

66:         require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

85:         require(_value != 0, "Nonce value cannot be set to 0");

89:             require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");

115:         require(oldMinNonce == _expectedNonce, "Incorrect nonce");

170:             revert("Reusing the same nonce twice");

172:             revert("The nonce was not set as used");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

22:         require(_pubdata.length <= BLOB_SIZE_BYTES * MAX_NUMBER_OF_BLOBS, "pubdata should fit in 2 blobs");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

219:         require(_isFirstInBatch, "Upgrade transaction must be first");

222:         require(_l2BlockNumber > 0, "L2 block number is never expected to be zero");

226:             require(correctPrevBlockHash == _expectedPrevL2BlockHash, "The previous L2 block hash is incorrect");

264:             require(_maxVirtualBlocksToCreate > 0, "Can't initialize the first virtual block");

332:             require(_maxVirtualBlocksToCreate > 0, "There must be a virtual block created at the start of the batch");

344:             require(!_isFirstInBatch, "Can not reuse L2 block number from the previous batch");

345:             require(currentL2BlockTimestamp == _l2BlockTimestamp, "The timestamp of the same L2 block must be same");

350:             require(_maxVirtualBlocksToCreate == 0, "Can not create virtual blocks in the middle of the miniblock");

362:             require(_expectedPrevL2BlockHash == pendingL2BlockHash, "The current L2 block hash is incorrect");

371:             revert("Invalid new L2 block number");

390:         require(currentBatchNumber > 0, "The current batch number must be greater than 0");

428:         require(_newTimestamp > previousBatchTimestamp, "Timestamps should be incremental");

429:         require(previousBatchNumber + 1 == _expectedNewNumber, "The provided block number is not correct");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="GAS-7"></a>[GAS-7] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*Instances (10)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

65:         uint256 amount = IERC20(_token).balanceOf(address(this));

161:         uint256 balanceBefore = _token.balanceOf(address(sharedBridge));

163:         uint256 balanceAfter = _token.balanceOf(address(sharedBridge));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

120:             uint256 balanceBefore = IERC20(_token).balanceOf(address(this));

121:             uint256 amount = IERC20(_token).balanceOf(address(legacyBridge));

124:             uint256 balanceAfter = IERC20(_token).balanceOf(address(this));

169:         uint256 balanceBefore = _token.balanceOf(address(this));

171:         uint256 balanceAfter = _token.balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

283:             (bool success, bytes memory data) = _init.delegatecall(_calldata);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

25:         (bool success, bytes memory returnData) = _delegateTo.delegatecall(_calldata);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol)

### <a name="GAS-8"></a>[GAS-8] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (55)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

116:     function transferTokenFromERC20Bridge(address _token) external onlyOwner {

137:     function initializeChainGovernance(uint256 _chainId, address _l2BridgeAddress) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

57:     function setDeployer(address _newDeployer) external onlyOwner {

62:     function addStateTransitionManager(address _stateTransitionManager) external onlyOwner {

72:     function removeStateTransitionManager(address _stateTransitionManager) external onlyOwner {

81:     function addToken(address _token) external onlyOwner {

88:     function setSharedBridge(address _sharedBridge) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

129:     function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {

142:     function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {

154:     function cancel(bytes32 _id) external onlyOwner {

249:     function updateDelay(uint256 _newDelay) external onlySelf {

256:     function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

98:     function setValidatorTimelock(address _validatorTimelock) external onlyOwner {

103:     function setInitialCutHash(Diamond.DiamondCutData calldata _diamondCut) external onlyOwner {

126:     function freezeChain(uint256 _chainId) external onlyOwner {

131:     function revertBatches(uint256 _chainId, uint256 _newLastBatch) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

73:     function setStateTransitionManager(IStateTransitionManager _stateTransitionManager) external onlyOwner {

78:     function addValidator(uint256 _chainId, address _newValidator) external onlyChainAdmin(_chainId) {

87:     function removeValidator(uint256 _chainId, address _validator) external onlyChainAdmin(_chainId) {

96:     function setExecutionDelay(uint32 _executionDelay) external onlyOwner {

146:     function revertBatches(uint256) external onlyValidator(ERA_CHAIN_ID) {

153:     function revertBatchesSharedBridge(uint256 _chainId, uint256) external onlyValidator(_chainId) {

182:     function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator(ERA_CHAIN_ID) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

23:     function setPendingAdmin(address _newPendingAdmin) external onlyAdmin {

45:     function setValidator(address _validator, bool _active) external onlyStateTransitionManager {

51:     function setPorterAvailability(bool _zkPorterIsAvailable) external onlyStateTransitionManager {

58:     function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyStateTransitionManager {

67:     function changeFeeParams(FeeParams calldata _newFeeParams) external onlyAdminOrStateTransitionManager {

79:     function setTokenMultiplier(uint128 _nominator, uint128 _denominator) external onlyAdminOrStateTransitionManager {

117:     function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyStateTransitionManager {

127:     function freezeDiamond() external onlyAdminOrStateTransitionManager {

137:     function unfreezeDiamond() external onlyAdminOrStateTransitionManager {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

340:     function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {

467:     function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidatorOrStateTransitionManager {

472:     function revertBatchesSharedBridge(uint256, uint256 _newLastBatch) external nonReentrant onlyValidator {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

38:     function transferEthToSharedBridge() external onlyBaseTokenBridge {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

35:     function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

46:     function storeAccountConstructedCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

54:     function markAccountCodeHashAsConstructed(address _address) external override onlyDeployer {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

65:     function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {

74:     function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

27:     function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyCallFromBootloader {

39:     function markBytecodeAsPublished(bytes32 _bytecodeHash) external onlyCompressor {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

64:     function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

65:     function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {

82:     function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {

110:     function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

21:     function chunkAndPublishPubdata(bytes calldata _pubdata) external onlyCallFrom(address(L1_MESSENGER_CONTRACT)) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

84:     function setChainId(uint256 _newChainId) external onlyCallFromForceDeployer {

93:     function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {

99:     function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {

379:     function appendTransactionToCurrentL2Block(bytes32 _txHash) external onlyCallFromBootloader {

385:     function publishTimestampDataToL1() external onlyCallFromBootloader {

459:     function incrementTxNumberInBatch() external onlyCallFromBootloader {

463:     function resetTxNumberInBatch() external onlyCallFromBootloader {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="GAS-9"></a>[GAS-9] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

*Saves 5 gas per instance*

*Instances (7)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

633:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

135:             numInitialWritesProcessed++;

144:             stateDiffPtr++;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

109:         numberOfLogsToProcess++;

288:         calldataPtr++;

294:         calldataPtr++;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

36:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

### <a name="GAS-10"></a>[GAS-10] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (5)*:
```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

26:     string public constant override getName = "ValidatorTimelock";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

20:     string public constant override getName = "AdminFacet";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

26:     string public constant override getName = "ExecutorFacet";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

24:     string public constant override getName = "GettersFacet";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

35:     string public constant override getName = "MailboxFacet";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

### <a name="GAS-11"></a>[GAS-11] Use shift right/left instead of division/multiplication if possible
While the `DIV` / `MUL` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`
- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`
- ...
- Use `>> 5` instead of `/ 2^5 == / 32`
- Use `<< 6` instead of `* 2^6 == * 64`

TL;DR:
- Shifting left by N is like multiplying by 2^N (Each bits to the left is an increased power of 2)
- Shifting right by N is like dividing by 2^N (Each bits to the right is a decreased power of 2)

*Saves around 2 gas + 20 for unchecked per instance*

*Instances (17)*:
```solidity
File: contracts/ethereum/contracts/common/Config.sol

14: uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Config.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

25:         uint256 bytecodeLenInWords = _bytecode.length / 32;

50:         codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

94:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

51:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/LibMap.sol

23:             uint256 mapValue = _map.map[_index / 8];

27:             uint256 bitOffset = (_index & 7) * 32;

43:             uint256 mapIndex = _index / 8;

48:             uint256 bitOffset = (_index & 7) * 32;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/LibMap.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

97:                 vInt += 8 + block.chainid * 2;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

52:                 encodedData.length * 4 == _bytecode.length,

57:                 dictionary.length / 8 <= encodedData.length / 2,

62:                 uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

66:                 uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

203:             dictionary = _rawCompressedData[2:2 + dictionaryLen * 8];

204:             encodedData = _rawCompressedData[2 + dictionaryLen * 8:];

253:         number >>= (256 - (_calldataSlice.length * 8));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

### <a name="GAS-12"></a>[GAS-12] Splitting require() statements that use && saves gas

*Instances (2)*:
```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

41:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

76:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

### <a name="GAS-13"></a>[GAS-13] Use of `this` instead of marking as `public` an `external` function
Using `this.` is like making an expensive external call. Consider marking the called function as public

*Saves around 2000 gas per instance*

*Instances (1)*:
```solidity
File: system-contracts/contracts/ContractDeployer.sol

276:             this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

### <a name="GAS-14"></a>[GAS-14] `uint256` to `bool` `mapping`: Utilizing Bitmaps to dramatically save on Gas
https://soliditydeveloper.com/bitmaps

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/BitMaps.sol

- [BitMaps.sol#L5-L16](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/BitMaps.sol#L5-L16):

```solidity
/**
 * @dev Library for managing uint256 to bool mapping in a compact and efficient way, provided the keys are sequential.
 * Largely inspired by Uniswap's https://github.com/Uniswap/merkle-distributor/blob/master/contracts/MerkleDistributor.sol[merkle-distributor].
 *
 * BitMaps pack 256 booleans across each bit of a single 256-bit slot of `uint256` type.
 * Hence booleans corresponding to 256 _sequential_ indices would only consume a single slot,
 * unlike the regular `bool` which would consume an entire slot for a single value.
 *
 * This results in gas savings in two ways:
 *
 * - Setting a zero value to non-zero only once every 256 times
 * - Accessing the same warm slot for every 256 _sequential_ indices
 */
```

*Instances (4)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

27:     mapping(uint256 l2BatchNumber => mapping(uint256 l2ToL1MessageNumber => bool isFinalized))

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

57:     mapping(uint256 chainId => mapping(uint256 l2BatchNumber => mapping(uint256 l2ToL1MessageNumber => bool isFinalized)))

61:     mapping(uint256 chainId => bool enabled) internal hyperbridgingEnabled;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol

114:     mapping(uint256 l2BatchNumber => mapping(uint256 l2ToL1MessageNumber => bool isFinalized)) isEthWithdrawalFinalized;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol)

### <a name="GAS-15"></a>[GAS-15] Increments/decrements can be unchecked in for-loops
In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

The change would be:

```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

These save around **25 gas saved** per instance.

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256`.

*Instances (17)*:
```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

225:         for (uint256 i = 0; i < _calls.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

116:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

135:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

185:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

209:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

633:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

226:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

270:         for (uint256 i = 0; i < deploymentsLength; ++i) {

275:         for (uint256 i = 0; i < deploymentsLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

38:             for (uint256 i = 0; i < immutablesLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

30:             for (uint256 i = 0; i < hashesLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

210:         for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {

222:         for (uint256 i = numberOfL2ToL1Logs; i < L2_TO_L1_LOGS_MERKLE_TREE_LEAVES; ++i) {

228:             for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

240:         for (uint256 i = 0; i < numberOfMessages; ++i) {

258:         for (uint256 i = 0; i < numberOfBytecodes; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

36:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

### <a name="GAS-16"></a>[GAS-16] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (25)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

122:             require(amount > 0, "ShB: 0 amount to transfer");

153:             require(msg.value == 0, "ShB m.v > 0 b d.it");

197:             require(msg.value == 0, "ShB m.v > 0 for BH d.it 2");

322:         require(_amount > 0, "y1");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

309:         } else if (_refundRecipient.code.length > 0) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

424:         if (_proof.serializedProof.length > 0) {

559:         return (_bitMap & (1 << _index)) > 0;

597:         require(_pubdataCommitments.length > 0, "pl");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

163:         require(s.baseTokenGasPriceMultiplierDenominator > 0, "Mailbox: baseTokenGasPriceDenominator not set");

289:         if (refundRecipient.code.length > 0) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

107:             require(selectors.length > 0, "B"); // no functions for diamond cut

132:         require(_facet.code.length > 0, "G");

155:         require(_facet.code.length > 0, "K");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Merkle.sol

24:         require(pathLength > 0, "xc");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

102:         if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

24:         require(_delegateTo.code.length > 0, "Delegatee is an EOA");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

325:         require(knownCodeMarker > 0, "The code hash is not known");

354:             if (value > 0) {

361:             if (value > 0) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

156:         return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

129:             currentVirtualBlockUpgradeInfo.virtualBlockFinishL2Block > 0

222:         require(_l2BlockNumber > 0, "L2 block number is never expected to be zero");

264:             require(_maxVirtualBlocksToCreate > 0, "Can't initialize the first virtual block");

332:             require(_maxVirtualBlocksToCreate > 0, "There must be a virtual block created at the start of the batch");

390:         require(currentBatchNumber > 0, "The current batch number must be greater than 0");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="GAS-17"></a>[GAS-17] WETH address definition can be use directly
WETH is a wrap Ether contract with a specific address in the Ethereum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

    Advantages of defining a specific contract directly:
    
    It saves gas,
    Prevents incorrect argument definition,
    Prevents execution on a different chain and re-signature issues,
    WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

*Instances (1)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

34:     address public immutable override l1WethAddress;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | `require()` should be used instead of `assert()` | 4 |
| [NC-2](#NC-2) | Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked` | 10 |
| [NC-3](#NC-3) | Constants should be in CONSTANT_CASE | 1 |
| [NC-4](#NC-4) | `constant`s should be defined rather than using magic numbers | 91 |
| [NC-5](#NC-5) | Control structures do not follow the Solidity Style Guide | 168 |
| [NC-6](#NC-6) | Critical Changes Should Use Two-step Procedure | 2 |
| [NC-7](#NC-7) | Default Visibility for constants | 91 |
| [NC-8](#NC-8) | Consider disabling `renounceOwnership()` | 5 |
| [NC-9](#NC-9) | Functions should not be longer than 50 lines | 352 |
| [NC-10](#NC-10) | Change int to int256 | 18 |
| [NC-11](#NC-11) | Change uint to uint256 | 1 |
| [NC-12](#NC-12) | Lines are too long | 8 |
| [NC-13](#NC-13) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 24 |
| [NC-14](#NC-14) | Consider using named mappings | 8 |
| [NC-15](#NC-15) | `address`s shouldn't be hard-coded | 1 |
| [NC-16](#NC-16) | Take advantage of Custom Error's return value property | 1 |
| [NC-17](#NC-17) | TODO Left in the code | 1 |
| [NC-18](#NC-18) | Some require descriptions are not clear | 111 |
| [NC-19](#NC-19) | Use Underscores for Number Literals (add an underscore every 3 digits) | 5 |
| [NC-20](#NC-20) | Constants should be defined rather than using magic numbers | 11 |
| [NC-21](#NC-21) | Variables need not be initialized to zero | 37 |
| [NC-22](#NC-22) | No need to check that `v == 27` or `v == 28` with `ecrecover` | 4 |
### <a name="NC-1"></a>[NC-1] `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Additionally, a require statement (or a custom error) are more friendly in terms of understanding what happened."

*Instances (4)*:
```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

94:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

51:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

421:         assert(block.chainid != 1);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

222:         assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

### <a name="NC-2"></a>[NC-2] Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked`
Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

Solidity version 0.8.12 introduces `string.concat()` (vs `abi.encodePacked(<str>,<str>), which catches concatenation errors (in the event of a `bytes` data mixed in the concatenation)`)

*Instances (10)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

456:                     abi.encodePacked(

512:             abi.encodePacked(

521:         return abi.encodePacked(s.zkPorterIsAvailable, s.l2BootloaderBytecodeHash, s.l2DefaultAccountBytecodeHash);

576:         bytes memory precompileInput = abi.encodePacked(_versionedHash, _openingPoint, _openingValueCommitmentProof);

621:                 abi.encodePacked(blobVersionedHash, _pubdataCommitments[i:i + PUBDATA_COMMITMENT_COMMITMENT_OFFSET])

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

118:             abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

96:             abi.encodePacked(

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

113:         return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);

123:         return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount, _sender, _additionalData);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

211:         return keccak256(abi.encodePacked(uint32(_blockNumber)));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="NC-3"></a>[NC-3] Constants should be in CONSTANT_CASE
For `constant` variable names, each word should use all capital letters, with underscores separating each word (CONSTANT_CASE)

*Instances (1)*:
```solidity
File: contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

22:     uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol)

### <a name="NC-4"></a>[NC-4] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (91)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

197:             require(msg.value == 0, "ShB m.v > 0 for BH d.it 2");

422:             require(!alreadyFinalized, "Withdrawal is already finalized 2");

434:             require(chainBalance[_chainId][l1Token] >= amount, "ShB not enough funds 2"); // not enought funds

496:         require(_l2ToL1message.length >= 56, "ShB wrong msg len"); // wrong messsage length

512:             require(_l2ToL1message.length == 76, "ShB wrong msg len 2");

559:         require(_l1Token != l1WethAddress, "ShB: WETH deposit not supported 2");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

251:                     "Bridgehub: msg.value mismatch 2"

255:                 require(msg.value == _request.secondBridgeValue, "Bridgehub: msg.value mismatch 3");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

23:         require(_bytecode.length % 32 == 0, "pq");

25:         uint256 bytecodeLenInWords = _bytecode.length / 32;

26:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

27:         require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

30:         hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));

32:         hashedBytecode = hashedBytecode | bytes32(bytecodeLenInWords << 224);

43:         require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd

50:         codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol

21:             offset := add(_start, 4)

28:             offset := add(_start, 20)

35:             offset := add(_start, 32)

42:             offset := add(_start, 32)

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

94:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

51:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol

25:         require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

58:                     keccak256(_newBatch.pubdataCommitments[1:_newBatch.pubdataCommitments.length - 32]),

63:                 _newBatch.pubdataCommitments[_newBatch.pubdataCommitments.length - 32:_newBatch

104:         uint256 batchTimestamp = _packedBatchAndL2BlockTimestamp >> 128;

187:             require(processedLogs == 511, "b7");

189:             require(processedLogs == 1023, "b8");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

373:                 mstore(add(hashedFactoryDeps, mul(add(i, 1), 32)), hashedBytecode)

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

286:                 if (data.length <= 4) {

297:             require(data.length == 32, "lp");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/LibMap.sol

23:             uint256 mapValue = _map.map[_index / 8];

27:             uint256 bitOffset = (_index & 7) * 32;

43:             uint256 mapIndex = _index / 8;

48:             uint256 bitOffset = (_index & 7) * 32;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/LibMap.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Merkle.sol

25:         require(pathLength < 256, "bt");

30:             currentHash = (_index % 2 == 0)

33:             _index /= 2;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

83:             costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

224:         require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

92:             require(vInt == 27 || vInt == 28, "Invalid v value");

97:                 vInt += 8 + block.chainid * 2;

191:             require(vInt == 27 || vInt == 28, "Invalid v value");

193:             vEncoded = RLPEncoder.encodeUint256(vInt - 27);

286:             require(vInt == 27 || vInt == 28, "Invalid v value");

288:             vEncoded = RLPEncoder.encodeUint256(vInt - 27);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

52:                 encodedData.length * 4 == _bytecode.length,

53:                 "Encoded data length should be 4 times shorter than the original bytecode"

57:                 dictionary.length / 8 <= encodedData.length / 2,

61:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

62:                 uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

66:                 uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

123:         uint256 stateDiffPtr = 2;

141:             stateDiffPtr += 32;

146:             uint8 len = operation == 0 ? 32 : metadata >> LENGTH_BITS_OFFSET;

177:             uint8 len = operation == 0 ? 32 : metadata >> LENGTH_BITS_OFFSET;

203:             dictionary = _rawCompressedData[2:2 + dictionaryLen * 8];

204:             encodedData = _rawCompressedData[2 + dictionaryLen * 8:];

229:             if (_operation == 0 || _operation == 3) {

236:             } else if (_operation == 2) {

253:         number >>= (256 - (_calldataSlice.length * 8));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

139:         if (to == address(DEPLOYER_SYSTEM_CONTRACT) && data.length >= 4) {

160:         require(_signature.length == 65, "Signature length is incorrect");

173:         require(v == 27 || v == 28, "v is neither 27 nor 28");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

78:         require(Utils.bytecodeLenInWords(_bytecodeHash) % 2 == 1, "Code length in words must be odd");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

62:         return SHA256_ROUND_GAS_COST * ((_length + 8) / SHA256_ROUND_NUMBER_OF_BYTES + 1);

89:         uint256 gasToPay = keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) + 2 * keccakGasCost(64);

143:             pubdataLen = 4 + _message.length + L2_TO_L1_LOG_SERIALIZE_SIZE;

154:             3 *

178:             pubdataLen = 4 + bytecodeLen;

204:         uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

206:         calldataPtr += 4;

227:             nodesOnCurrentLevel /= 2;

237:         uint32 numberOfMessages = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

238:         calldataPtr += 4;

241:             uint32 currentMessageLength = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

242:             calldataPtr += 4;

255:         uint32 numberOfBytecodes = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

256:         calldataPtr += 4;

260:                 bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4])

262:             calldataPtr += 4;

290:         uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]));

291:         calldataPtr += 3;

304:         uint32 numberOfStateDiffs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

305:         calldataPtr += 4;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

141:         return 18;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

22:         require(_pubdata.length <= BLOB_SIZE_BYTES * MAX_NUMBER_OF_BLOBS, "pubdata should fit in 2 blobs");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

44:     uint256 public difficulty = 2.5e15;

121:         if (blockNumber <= _block || blockNumber - _block > 256) {

393:         uint256 packedTimestamps = (uint256(currentBatchTimestamp) << 128) | currentL2BlockTimestamp;

475:         blockInfo = (uint256(blockNumber) << 128) | uint256(blockTimestamp);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="NC-5"></a>[NC-5] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (168)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

155:             uint256 amount = _depositFunds(_prevMsgSender, IERC20(_l1Token), _amount); // note if _prevMsgSender is this contract, this will return 0. This does not happen.

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/common/Config.sol

47: uint256 constant PUBLIC_INPUT_SHIFT = 32;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Config.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

54:                             MODIFIERS

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

168:             verifier: address(0),

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

10: import {VerifierParams} from "../chain-interfaces/IVerifier.sol";

25:         require(address(_initializeData.verifier) != address(0), "vt");

37:         s.verifier = _initializeData.verifier;

42:         s.verifierParams = _initializeData.verifierParams;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol

5: import {IVerifier, VerifierParams} from "../chain-interfaces/IVerifier.sol";

76:     IVerifier verifier;

81:     uint256 totalBatchesVerified;

94:     VerifierParams verifierParams;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

6: import {COMMIT_TIMESTAMP_NOT_OLDER, COMMIT_TIMESTAMP_APPROXIMATION_DELTA, EMPTY_STRING_KECCAK, L2_TO_L1_LOG_SERIALIZE_SIZE, MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, PACKED_L2_BLOCK_TIMESTAMP_MASK, PUBLIC_INPUT_SHIFT, POINT_EVALUATION_PRECOMPILE_ADDR} from "../../../common/Config.sol";

11: import {VerifierParams} from "../../chain-interfaces/IVerifier.sol";

53:             blobCommitments = _verifyBlobInformation(_newBatch.pubdataCommitments[1:], blobHashes);

76:         _verifyBatchTimestamp(logOutput.packedBatchAndL2BlockTimestamp, _newBatch.timestamp, _previousBatch.timestamp);

79:         bytes32 commitment = _createBatchCommitment(_newBatch, logOutput.stateDiffHash, blobCommitments, blobHashes);

98:     function _verifyBatchTimestamp(

156:                 logOutput.stateDiffHash = logValue;

353:         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

387:         uint256 currentTotalBatchesVerified = s.totalBatchesVerified;

391:         VerifierParams memory verifierParams = s.verifierParams;

397:         require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

401:             currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();

403:                 _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],

411:                 verifierParams

416:         require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");

425:             _verifyProof(proofPublicInput, _proof);

428:         _verifyProof(proofPublicInput, _proof);

431:         emit BlocksVerification(s.totalBatchesVerified, currentTotalBatchesVerified);

432:         s.totalBatchesVerified = currentTotalBatchesVerified;

439:         bool successVerifyProof = s.verifier.verify(

444:         require(successVerifyProof, "p"); // Proof verification fail

451:         VerifierParams memory _verifierParams

459:                         _verifierParams.recursionNodeLevelVkHash,

460:                         _verifierParams.recursionLeafLevelVkHash

463:             ) >> PUBLIC_INPUT_SHIFT;

481:             s.totalBatchesVerified = _newLastBatch;

491:         emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);

497:         bytes32 _stateDiffHash,

504:             _batchAuxiliaryOutput(_newBatchData, _stateDiffHash, _blobCommitments, _blobHashes)

526:         bytes32 _stateDiffHash,

537:                 _stateDiffHash,

591:     function _verifyBlobInformation(

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

6: import {VerifierParams} from "../../../state-transition/chain-interfaces/IVerifier.sol";

32:         return address(s.verifier);

82:         return s.totalBatchesVerified;

137:         return s.verifierParams;

239:         return s.totalBatchesVerified;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol

6: import {IVerifier, VerifierParams} from "./IVerifier.sol";

35:     IVerifier verifier;

36:     VerifierParams verifierParams;

52:     IVerifier verifier;

53:     VerifierParams verifierParams;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol

11:     STATE_DIFF_HASH_KEY,

32:     bytes32 stateDiffHash;

192:     event BlocksVerification(uint256 indexed previousLastVerifiedBatch, uint256 indexed currentLastVerifiedBatch);

206:     event BlocksRevert(uint256 totalBatchesCommitted, uint256 totalBatchesVerified, uint256 totalBatchesExecuted);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol

6: import {VerifierParams} from "../chain-interfaces/IVerifier.sol";

18:     function getVerifier() external view returns (address);

42:     function getTotalBatchesVerified() external view returns (uint256);

81:     function getVerifierParams() external view returns (VerifierParams memory);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol

18:     function getTotalBlocksVerified() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IVerifier.sol

19:     function verify(

27:     function verificationKeyHash() external pure returns (bytes32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IVerifier.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransition.sol

9: import {Verifier} from "../Verifier.sol";

10: import {VerifierParams} from "./IVerifier.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransition.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

135:         _saveFacetIfNew(_facet);

165:             _saveFacetIfNew(_facet);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

7: import {VerifierParams} from "../state-transition/chain-interfaces/IVerifier.sol";

8: import {IVerifier} from "../state-transition/chain-interfaces/IVerifier.sol";

33:     address verifier;

34:     VerifierParams verifierParams;

55:     event NewVerifier(address indexed oldVerifier, address indexed newVerifier);

58:     event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);

76:         _upgradeVerifier(_proposedUpgrade.verifier, _proposedUpgrade.verifierParams);

135:         IVerifier oldVerifier = s.verifier;

136:         s.verifier = _newVerifier;

137:         emit NewVerifier(address(oldVerifier), address(_newVerifier));

147:         if (

148:             _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) &&

149:             _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) &&

150:             _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)

155:         VerifierParams memory oldVerifierParams = s.verifierParams;

156:         s.verifierParams = _newVerifierParams;

157:         emit NewVerifierParams(oldVerifierParams, _newVerifierParams);

164:         _setVerifier(IVerifier(_newVerifier));

165:         _setVerifierParams(_verifierParams);

210:         _verifyFactoryDeps(_factoryDeps, _l2ProtocolUpgradeTx.factoryDeps);

244:             "Too big protocol version difference"

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol

7: import "../state-transition/chain-interfaces/IVerifier.sol";

29:             "Too big protocol version difference"

56:         _upgradeVerifier(_proposedUpgrade.verifier, _proposedUpgrade.verifierParams);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

131:         if (

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

10: import {L1_MESSENGER_CONTRACT, INITIAL_WRITE_STARTING_POSITION, COMPRESSED_INITIAL_WRITE_SIZE, STATE_DIFF_ENTRY_SIZE, STATE_DIFF_ENUM_INDEX_OFFSET, STATE_DIFF_FINAL_VALUE_OFFSET, STATE_DIFF_DERIVED_KEY_OFFSET, DERIVED_KEY_LENGTH, VALUE_LENGTH, ENUM_INDEX_LENGTH, KNOWN_CODE_STORAGE_CONTRACT} from "./Constants.sol";

110:     function verifyCompressedStateDiffs(

111:         uint256 _numberOfStateDiffs,

113:         bytes calldata _stateDiffs,

114:         bytes calldata _compressedStateDiffs

121:         uint256 numberOfInitialWrites = uint256(_compressedStateDiffs.readUint16(0));

123:         uint256 stateDiffPtr = 2;

128:             bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];

129:             uint64 enumIndex = stateDiff.readUint64(84);

137:             bytes32 derivedKey = stateDiff.readBytes32(52);

138:             uint256 initValue = stateDiff.readUint256(92);

139:             uint256 finalValue = stateDiff.readUint256(124);

140:             require(derivedKey == _compressedStateDiffs.readBytes32(stateDiffPtr), "iw: initial key mismatch");

141:             stateDiffPtr += 32;

143:             uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));

144:             stateDiffPtr++;

147:             _verifyValueCompression(

151:                 _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]

153:             stateDiffPtr += len;

156:         require(numInitialWritesProcessed == numberOfInitialWrites, "Incorrect number of initial storage diffs");

160:             bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];

161:             uint64 enumIndex = stateDiff.readUint64(84);

166:             uint256 initValue = stateDiff.readUint256(92);

167:             uint256 finalValue = stateDiff.readUint256(124);

169:                 _compressedStateDiffs[stateDiffPtr:stateDiffPtr + _enumerationIndexSize]

172:             stateDiffPtr += _enumerationIndexSize;

174:             uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));

175:             stateDiffPtr += 1;

178:             _verifyValueCompression(

182:                 _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]

184:             stateDiffPtr += len;

187:         require(stateDiffPtr == _compressedStateDiffs.length, "Extra data in _compressedStateDiffs");

189:         stateDiffHash = EfficientCall.keccak(_stateDiffs);

220:     function _verifyValueCompression(

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/Constants.sol

90: uint256 constant STATE_DIFF_ENTRY_SIZE = 272;

99:     STATE_DIFF_HASH_KEY,

131: uint256 constant STATE_DIFF_DERIVED_KEY_OFFSET = 52;

133: uint256 constant STATE_DIFF_ENUM_INDEX_OFFSET = 84;

135: uint256 constant STATE_DIFF_FINAL_VALUE_OFFSET = 124;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Constants.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

47:         if (

372:             require(value == 0, "The value must be zero if we do not call the constructor");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

87:             abi.encodeCall(INonceHolder.incrementMinNonceIfEquals, (_transaction.nonce))

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

5: import {IL1Messenger, L2ToL1Log, L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, L2_TO_L1_LOG_SERIALIZE_SIZE, STATE_DIFF_COMPRESSION_VERSION_NUMBER} from "./interfaces/IL1Messenger.sol";

10: import {SystemLogKey, SYSTEM_CONTEXT_CONTRACT, KNOWN_CODE_STORAGE_CONTRACT, COMPRESSOR_CONTRACT, STATE_DIFF_ENTRY_SIZE, MAX_ALLOWED_PUBDATA_PER_BATCH, L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, PUBDATA_CHUNK_PUBLISHER} from "./Constants.sol";

199:         bytes calldata _totalL2ToL1PubdataAndStateDiffs

204:         uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

212:                 _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + L2_TO_L1_LOG_SERIALIZE_SIZE]

237:         uint32 numberOfMessages = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

241:             uint32 currentMessageLength = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

244:                 _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + currentMessageLength]

255:         uint32 numberOfBytecodes = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

260:                 bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4])

267:                         _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + currentBytecodeLength]

284:             uint256(uint8(bytes1(_totalL2ToL1PubdataAndStateDiffs[calldataPtr]))) ==

285:                 STATE_DIFF_COMPRESSION_VERSION_NUMBER,

286:             "state diff compression version mismatch"

290:         uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]));

293:         uint8 enumerationIndexSize = uint8(bytes1(_totalL2ToL1PubdataAndStateDiffs[calldataPtr]));

296:         bytes calldata compressedStateDiffs = _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr +

297:             compressedStateDiffSize];

298:         calldataPtr += compressedStateDiffSize;

300:         bytes calldata totalL2ToL1Pubdata = _totalL2ToL1PubdataAndStateDiffs[:calldataPtr];

304:         uint32 numberOfStateDiffs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

307:         bytes calldata stateDiffs = _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr +

308:             (numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE)];

309:         calldataPtr += numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE;

311:         bytes32 stateDiffHash = COMPRESSOR_CONTRACT.verifyCompressedStateDiffs(

312:             numberOfStateDiffs,

314:             stateDiffs,

315:             compressedStateDiffs

319:         require(calldataPtr == _totalL2ToL1PubdataAndStateDiffs.length, "Extra data in the totalL2ToL1Pubdata array");

330:         SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.STATE_DIFF_HASH_KEY)), stateDiffHash);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

44:     uint256 public difficulty = 2.5e15;

127:         } else if (

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="NC-6"></a>[NC-6] Critical Changes Should Use Two-step Procedure
The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference: <https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure>

**Recommended Mitigation Steps**

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

*Instances (2)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

23:     function setPendingAdmin(address _newPendingAdmin) external onlyAdmin {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol

17:     function setPendingAdmin(address _newPendingAdmin) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol)

### <a name="NC-7"></a>[NC-7] Default Visibility for constants
Some constants are using the default visibility. For readability, consider explicitly declaring them as `internal`.

*Instances (91)*:
```solidity
File: contracts/ethereum/contracts/common/Config.sol

6: bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;

11: uint256 constant L2_TO_L1_LOG_SERIALIZE_SIZE = 88;

14: uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

19: bytes32 constant L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH = 0x72abee45b59e344af8a6e520241c4744aff26ed411f4c4b00f8af09adada43ba;

22: bytes32 constant DEFAULT_L2_LOGS_TREE_ROOT_HASH = bytes32(0);

25: uint256 constant PRIORITY_OPERATION_L2_TX_TYPE = 255;

28: uint256 constant SYSTEM_UPGRADE_L2_TX_TYPE = 254;

33: uint256 constant MAX_ALLOWED_PROTOCOL_VERSION_DELTA = 100;

37: uint256 constant PRIORITY_EXPIRATION = 0 days;

40: uint256 constant COMMIT_TIMESTAMP_NOT_OLDER = 3 days;

44: uint256 constant COMMIT_TIMESTAMP_APPROXIMATION_DELTA = 1 hours;

50: uint256 constant MAX_GAS_PER_TRANSACTION = $(MAX_GAS_PER_TRANSACTION);

54: uint256 constant L1_GAS_PER_PUBDATA_BYTE = $(L1_GAS_PER_PUBDATA_BYTE);

57: uint256 constant L1_TX_INTRINSIC_L2_GAS = $(L1_TX_INTRINSIC_L2_GAS);

60: uint256 constant L1_TX_INTRINSIC_PUBDATA = $(L1_TX_INTRINSIC_PUBDATA);

63: uint256 constant L1_TX_MIN_L2_GAS_BASE = $(L1_TX_MIN_L2_GAS_BASE);

66: uint256 constant L1_TX_DELTA_544_ENCODING_BYTES = $(L1_TX_DELTA_544_ENCODING_BYTES);

69: uint256 constant L1_TX_DELTA_FACTORY_DEPS_L2_GAS = $(L1_TX_DELTA_FACTORY_DEPS_L2_GAS);

72: uint256 constant L1_TX_DELTA_FACTORY_DEPS_PUBDATA = $(L1_TX_DELTA_FACTORY_DEPS_PUBDATA);

75: uint256 constant MAX_NEW_FACTORY_DEPS = $(MAX_NEW_FACTORY_DEPS);

78: uint256 constant REQUIRED_L2_GAS_PRICE_PER_PUBDATA = $(REQUIRED_L2_GAS_PRICE_PER_PUBDATA);

82: uint256 constant PACKED_L2_BLOCK_TIMESTAMP_MASK = 0xffffffffffffffffffffffffffffffff;

85: address constant POINT_EVALUATION_PRECOMPILE_ADDR = address(0x0A);

92: uint256 constant TX_SLOT_OVERHEAD_L2_GAS = 10000;

99: uint256 constant MEMORY_OVERHEAD_GAS = 10;

101: address constant ETH_TOKEN_ADDRESS = address(1);

104: uint256 constant ERA_CHAIN_ID = $(ERA_CHAIN_ID);

107: address constant ERA_ERC20_BRIDGE_ADDRESS = $(ERA_ERC20_BRIDGE_ADDRESS);

110: address constant ERA_DIAMOND_PROXY = $(ERA_DIAMOND_PROXY);

112: bytes32 constant TWO_BRIDGES_MAGIC_VALUE = bytes32(uint256(keccak256("TWO_BRIDGES_MAGIC_VALUE")) - 1);

115: address constant BRIDGEHUB_MIN_SECOND_BRIDGE_ADDRESS = address(uint160(type(uint16).max));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Config.sol)

```solidity
File: contracts/ethereum/contracts/common/L2ContractAddresses.sol

6: address constant L2_BOOTLOADER_ADDRESS = address(0x8001);

9: address constant L2_KNOWN_CODE_STORAGE_SYSTEM_CONTRACT_ADDR = address(0x8004);

12: address constant L2_DEPLOYER_SYSTEM_CONTRACT_ADDR = address(0x8006);

21: address constant L2_FORCE_DEPLOYER_ADDR = address(0x8007);

24: address constant L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR = address(0x8008);

27: address constant L2_BASE_TOKEN_SYSTEM_CONTRACT_ADDR = address(0x800a);

30: address constant L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR = address(0x800b);

33: address constant L2_PUBDATA_CHUNK_PUBLISHER_ADDR = address(0x8011);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol

40: uint256 constant L2_LOG_ADDRESS_OFFSET = 4;

43: uint256 constant L2_LOG_KEY_OFFSET = 24;

46: uint256 constant L2_LOG_VALUE_OFFSET = 56;

50: uint256 constant BLS_MODULUS = 52435875175126190479447740508185965837690552500527637822603658699938581184513;

54: uint256 constant PUBDATA_COMMITMENT_SIZE = 144;

57: uint256 constant PUBDATA_COMMITMENT_CLAIMED_VALUE_OFFSET = 16;

60: uint256 constant PUBDATA_COMMITMENT_COMMITMENT_OFFSET = 48;

63: uint256 constant MAX_NUMBER_OF_BLOBS = 2;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol)

```solidity
File: contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

22:     uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

23:     bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/Constants.sol

20: uint160 constant SYSTEM_CONTRACTS_OFFSET = {{SYSTEM_CONTRACTS_OFFSET}}; // 2^15

24: uint160 constant MAX_SYSTEM_CONTRACT_ADDRESS = 0xffff; // 2^16 - 1

26: address constant ECRECOVER_SYSTEM_CONTRACT = address(0x01);

27: address constant SHA256_SYSTEM_CONTRACT = address(0x02);

28: address constant ECADD_SYSTEM_CONTRACT = address(0x06);

29: address constant ECMUL_SYSTEM_CONTRACT = address(0x07);

34: uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = 0xff;

36: address payable constant BOOTLOADER_FORMAL_ADDRESS = payable(address(SYSTEM_CONTRACTS_OFFSET + 0x01));

37: IAccountCodeStorage constant ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT = IAccountCodeStorage(

40: INonceHolder constant NONCE_HOLDER_SYSTEM_CONTRACT = INonceHolder(address(SYSTEM_CONTRACTS_OFFSET + 0x03));

41: IKnownCodesStorage constant KNOWN_CODE_STORAGE_CONTRACT = IKnownCodesStorage(address(SYSTEM_CONTRACTS_OFFSET + 0x04));

42: IImmutableSimulator constant IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT = IImmutableSimulator(

45: IContractDeployer constant DEPLOYER_SYSTEM_CONTRACT = IContractDeployer(address(SYSTEM_CONTRACTS_OFFSET + 0x06));

49: address constant FORCE_DEPLOYER = address(SYSTEM_CONTRACTS_OFFSET + 0x07);

50: IL1Messenger constant L1_MESSENGER_CONTRACT = IL1Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

51: address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

53: IBaseToken constant BASE_TOKEN_SYSTEM_CONTRACT = IBaseToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));

58: address constant KECCAK256_SYSTEM_CONTRACT = address(0x8010);

60: ISystemContext constant SYSTEM_CONTEXT_CONTRACT = ISystemContext(payable(address(SYSTEM_CONTRACTS_OFFSET + 0x0b)));

62: IBootloaderUtilities constant BOOTLOADER_UTILITIES = IBootloaderUtilities(address(SYSTEM_CONTRACTS_OFFSET + 0x0c));

65: address constant EVENT_WRITER_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x0d);

67: ICompressor constant COMPRESSOR_CONTRACT = ICompressor(address(SYSTEM_CONTRACTS_OFFSET + 0x0e));

69: IComplexUpgrader constant COMPLEX_UPGRADER_CONTRACT = IComplexUpgrader(address(SYSTEM_CONTRACTS_OFFSET + 0x0f));

71: IPubdataChunkPublisher constant PUBDATA_CHUNK_PUBLISHER = IPubdataChunkPublisher(

77: uint256 constant MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT = 1;

80: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;

84: bytes32 constant CREATE2_PREFIX = 0x2020dba91b30cc0006188af794c2fb30dd8520db7e2c088b7fc7c103c00ca494;

87: bytes32 constant CREATE_PREFIX = 0x63bae3a9951d38e8a3fbb7b70909afc1200610fc5bc55ade242f815974674f23;

90: uint256 constant STATE_DIFF_ENTRY_SIZE = 272;

94: uint256 constant MAX_ALLOWED_PUBDATA_PER_BATCH = 520000;

111: uint256 constant L2_TO_L1_LOGS_MERKLE_TREE_LEAVES = 4096;

114: uint256 constant DERIVED_KEY_LENGTH = 32;

116: uint256 constant ENUM_INDEX_LENGTH = 8;

118: uint256 constant VALUE_LENGTH = 32;

121: uint256 constant COMPRESSED_INITIAL_WRITE_SIZE = DERIVED_KEY_LENGTH + VALUE_LENGTH;

123: uint256 constant COMPRESSED_REPEATED_WRITE_SIZE = ENUM_INDEX_LENGTH + VALUE_LENGTH;

126: uint256 constant INITIAL_WRITE_STARTING_POSITION = 4;

131: uint256 constant STATE_DIFF_DERIVED_KEY_OFFSET = 52;

133: uint256 constant STATE_DIFF_ENUM_INDEX_OFFSET = 84;

135: uint256 constant STATE_DIFF_FINAL_VALUE_OFFSET = 124;

140: uint256 constant BLOB_SIZE_BYTES = 126_976;

143: uint256 constant MAX_NUMBER_OF_BLOBS = 2;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Constants.sol)

### <a name="NC-8"></a>[NC-8] Consider disabling `renounceOwnership()`
If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's `Ownable`'s `renounceOwnership()` function in order to disable it.

*Instances (5)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

30: contract L1SharedBridge is IL1SharedBridge, ReentrancyGuard, Initializable, Ownable2Step {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

16: contract Bridgehub is IBridgehub, ReentrancyGuard, Ownable2Step {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

20: contract Governance is IGovernance, Ownable2Step {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

25: contract StateTransitionManager is IStateTransitionManager, ReentrancyGuard, Ownable2Step {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

22: contract ValidatorTimelock is IExecutor, Ownable2Step {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

### <a name="NC-9"></a>[NC-9] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (352)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

60:     function initialize() external reentrancyGuardInitializer {}

63:     function tranferTokenToSharedBridge(address _token, uint256 _amount) external {

75:     function l2TokenAddress(address _l1Token) external view returns (address) {

160:     function _depositFundsToSharedBridge(address _from, IERC20 _token, uint256 _amount) internal returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

116:     function transferTokenFromERC20Bridge(address _token) external onlyOwner {

137:     function initializeChainGovernance(uint256 _chainId, address _l2BridgeAddress) external onlyOwner {

168:     function _depositFunds(address _from, IERC20 _token, uint256 _amount) internal returns (uint256) {

249:     function _getERC20Getters(address _token) internal view returns (bytes memory) {

369:     function _isEraLegacyWithdrawal(uint256 _chainId, uint256 _l2BatchNumber) internal view returns (bool) {

498:         (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_l2ToL1message, 0);

499:         if (bytes4(functionSignature) == IMailbox.finalizeEthWithdrawal.selector) {

504:         } else if (bytes4(functionSignature) == IL1ERC20Bridge.finalizeWithdrawal.selector) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL1ERC20Bridge.sol

24:     function isWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool);

61:     function l2TokenAddress(address _l1Token) external view returns (address);

63:     function sharedBridge() external view returns (IL1SharedBridge);

65:     function l2TokenBeacon() external view returns (address);

67:     function l2Bridge() external view returns (address);

75:     function tranferTokenToSharedBridge(address _token, uint256 _amount) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL1SharedBridge.sol

114:     function l1WethAddress() external view returns (address);

116:     function bridgehub() external view returns (IBridgehub);

118:     function legacyBridge() external view returns (IL1ERC20Bridge);

120:     function l2BridgeAddress(uint256 _chainId) external view returns (address);

122:     function depositHappened(uint256 _chainId, bytes32 _l2TxHash) external view returns (bytes32);

142:     function bridgehubConfirmL2Transaction(uint256 _chainId, bytes32 _txDataHash, bytes32 _txHash) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

15:     function withdraw(address _l1Receiver, address _l2Token, uint256 _amount) external;

17:     function l1TokenAddress(address _l2Token) external view returns (address);

19:     function l2TokenAddress(address _l1Token) external view returns (address);

21:     function l1Bridge() external view returns (address);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

38:     function initialize(address _owner) external reentrancyGuardInitializer {

50:     function getStateTransition(uint256 _chainId) public view returns (address) {

57:     function setDeployer(address _newDeployer) external onlyOwner {

62:     function addStateTransitionManager(address _stateTransitionManager) external onlyOwner {

72:     function removeStateTransitionManager(address _stateTransitionManager) external onlyOwner {

81:     function addToken(address _token) external onlyOwner {

88:     function setSharedBridge(address _sharedBridge) external onlyOwner {

305:     function _actualRefundRecipient(address _refundRecipient) internal view returns (address _recipient) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/IBridgehub.sol

44:     function stateTransitionManagerIsRegistered(address _stateTransitionManager) external view returns (bool);

46:     function stateTransitionManager(uint256 _chainId) external view returns (address);

48:     function tokenIsRegistered(address _baseToken) external view returns (bool);

50:     function baseToken(uint256 _chainId) external view returns (address);

52:     function sharedBridge() external view returns (IL1SharedBridge);

54:     function getStateTransition(uint256 _chainId) external view returns (address);

110:     function addStateTransitionManager(address _stateTransitionManager) external;

112:     function removeStateTransitionManager(address _stateTransitionManager) external;

116:     function setSharedBridge(address _sharedBridge) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/IBridgehub.sol)

```solidity
File: contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol

25:     function forceDeployOnAddresses(ForceDeployment[] calldata _deployParams) external;

31:     function create2(bytes32 _salt, bytes32 _bytecodeHash, bytes calldata _input) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

21:     function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {

39:     function validateBytecodeHash(bytes32 _bytecodeHash) internal pure {

49:     function _bytecodeLen(bytes32 _bytecodeHash) private pure returns (uint256 codeLengthInWords) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/UncheckedMath.sol

11:     function uncheckedInc(uint256 _number) internal pure returns (uint256) {

17:     function uncheckedAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol

19:     function readUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32 result, uint256 offset) {

26:     function readAddress(bytes memory _bytes, uint256 _start) internal pure returns (address result, uint256 offset) {

33:     function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {

40:     function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

84:     function isOperation(bytes32 _id) public view returns (bool) {

89:     function isOperationPending(bytes32 _id) public view returns (bool) {

95:     function isOperationReady(bytes32 _id) public view returns (bool) {

100:     function isOperationDone(bytes32 _id) public view returns (bool) {

105:     function getOperationState(bytes32 _id) public view returns (OperationState) {

129:     function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {

142:     function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {

167:     function execute(Operation calldata _operation) external payable onlyOwnerOrSecurityCouncil {

186:     function executeInstant(Operation calldata _operation) external payable onlySecurityCouncil {

204:     function hashOperation(Operation calldata _operation) public pure returns (bytes32) {

215:     function _schedule(bytes32 _id, uint256 _delay) internal {

224:     function _execute(Call[] calldata _calls) internal {

239:     function _checkPredecessorDone(bytes32 _predecessorId) internal view {

249:     function updateDelay(uint256 _newDelay) external onlySelf {

256:     function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/governance/IGovernance.sol

41:     function isOperation(bytes32 _id) external view returns (bool);

43:     function isOperationPending(bytes32 _id) external view returns (bool);

45:     function isOperationReady(bytes32 _id) external view returns (bool);

47:     function isOperationDone(bytes32 _id) external view returns (bool);

49:     function getOperationState(bytes32 _id) external view returns (OperationState);

51:     function scheduleTransparent(Operation calldata _operation, uint256 _delay) external;

53:     function scheduleShadow(bytes32 _id, uint256 _delay) external;

57:     function execute(Operation calldata _operation) external payable;

59:     function executeInstant(Operation calldata _operation) external payable;

61:     function hashOperation(Operation calldata _operation) external pure returns (bytes32);

65:     function updateSecurityCouncil(address _newSecurityCouncil) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol

36:     function bridgehub() external view returns (address);

38:     function stateTransition(uint256 _chainId) external view returns (address);

40:     function storedBatchZero() external view returns (bytes32);

42:     function initialCutHash() external view returns (bytes32);

44:     function genesisUpgrade() external view returns (address);

46:     function upgradeCutHash(uint256 _protocolVersion) external view returns (bytes32);

48:     function protocolVersion() external view returns (uint256);

50:     function initialize(StateTransitionManagerInitializeData calldata _initalizeData) external;

52:     function setInitialCutHash(Diamond.DiamondCutData calldata _diamondCut) external;

54:     function setValidatorTimelock(address _validatorTimelock) external;

56:     function getChainAdmin(uint256 _chainId) external view returns (address);

73:     function setUpgradeDiamondCut(Diamond.DiamondCutData calldata _cutData, uint256 _oldProtocolVersion) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

62:     function getChainAdmin(uint256 _chainId) external view override returns (address) {

98:     function setValidatorTimelock(address _validatorTimelock) external onlyOwner {

103:     function setInitialCutHash(Diamond.DiamondCutData calldata _diamondCut) external onlyOwner {

126:     function freezeChain(uint256 _chainId) external onlyOwner {

131:     function revertBatches(uint256 _chainId, uint256 _newLastBatch) external onlyOwner {

138:     function _setChainIdUpgrade(uint256 _chainId, address _chainContract) internal {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

73:     function setStateTransitionManager(IStateTransitionManager _stateTransitionManager) external onlyOwner {

78:     function addValidator(uint256 _chainId, address _newValidator) external onlyChainAdmin(_chainId) {

87:     function removeValidator(uint256 _chainId, address _validator) external onlyChainAdmin(_chainId) {

96:     function setExecutionDelay(uint32 _executionDelay) external onlyOwner {

102:     function getCommittedBatchTimestamp(uint256 _chainId, uint256 _l2BatchNumber) external view returns (uint256) {

146:     function revertBatches(uint256) external onlyValidator(ERA_CHAIN_ID) {

153:     function revertBatchesSharedBridge(uint256 _chainId, uint256) external onlyValidator(_chainId) {

182:     function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator(ERA_CHAIN_ID) {

225:     function _propagateToZkSyncStateTransition(uint256 _chainId) internal {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

24:     function initialize(InitializeData calldata _initializeData) external reentrancyGuardInitializer returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

23:     function setPendingAdmin(address _newPendingAdmin) external onlyAdmin {

45:     function setValidator(address _validator, bool _active) external onlyStateTransitionManager {

51:     function setPorterAvailability(bool _zkPorterIsAvailable) external onlyStateTransitionManager {

58:     function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyStateTransitionManager {

67:     function changeFeeParams(FeeParams calldata _newFeeParams) external onlyAdminOrStateTransitionManager {

79:     function setTokenMultiplier(uint128 _nominator, uint128 _denominator) external onlyAdminOrStateTransitionManager {

117:     function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyStateTransitionManager {

127:     function freezeDiamond() external onlyAdminOrStateTransitionManager {

137:     function unfreezeDiamond() external onlyAdminOrStateTransitionManager {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

248:     function _commitBatchesWithoutSystemContractsUpgrade(

268:     function _commitBatchesWithSystemContractsUpgrade(

303:     function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {

316:     function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal {

340:     function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {

344:     function _executeBatches(StoredBatchInfo[] calldata _batchesData) internal {

435:     function _verifyProof(uint256[] memory proofPublicInput, ProofInput calldata _proof) internal view {

467:     function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidatorOrStateTransitionManager {

472:     function revertBatchesSharedBridge(uint256, uint256 _newLastBatch) external nonReentrant onlyValidator {

476:     function _revertBatches(uint256 _newLastBatch) internal {

510:     function _batchPassThroughData(CommitBatchInfo calldata _batch) internal pure returns (bytes memory) {

520:     function _batchMetaParameters() internal view returns (bytes memory) {

553:     function _hashStoredBatchInfo(StoredBatchInfo memory _storedBatchInfo) internal pure returns (bytes32) {

558:     function _checkBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) {

563:     function _setBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) {

645:     function _getBlobVersionedHash(uint256 _index) internal view returns (bytes32 versionedHash) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

31:     function getVerifier() external view returns (address) {

36:     function getAdmin() external view returns (address) {

41:     function getPendingAdmin() external view returns (address) {

46:     function getBridgehub() external view returns (address) {

51:     function getStateTransitionManager() external view returns (address) {

56:     function getBaseToken() external view returns (address) {

61:     function getBaseTokenBridge() external view returns (address) {

66:     function baseTokenGasPriceMultiplierNominator() external view returns (uint128) {

71:     function baseTokenGasPriceMultiplierDenominator() external view returns (uint128) {

76:     function getTotalBatchesCommitted() external view returns (uint256) {

81:     function getTotalBatchesVerified() external view returns (uint256) {

86:     function getTotalBatchesExecuted() external view returns (uint256) {

91:     function getTotalPriorityTxs() external view returns (uint256) {

96:     function getFirstUnprocessedPriorityTx() external view returns (uint256) {

101:     function getPriorityQueueSize() external view returns (uint256) {

106:     function priorityQueueFrontOperation() external view returns (PriorityOperation memory) {

111:     function isValidator(address _address) external view returns (bool) {

116:     function l2LogsRootHash(uint256 _batchNumber) external view returns (bytes32) {

121:     function storedBatchHash(uint256 _batchNumber) external view returns (bytes32) {

126:     function getL2BootloaderBytecodeHash() external view returns (bytes32) {

131:     function getL2DefaultAccountBytecodeHash() external view returns (bytes32) {

136:     function getVerifierParams() external view returns (VerifierParams memory) {

141:     function getProtocolVersion() external view returns (uint256) {

146:     function getL2SystemContractsUpgradeTxHash() external view returns (bytes32) {

151:     function getL2SystemContractsUpgradeBatchNumber() external view returns (uint256) {

156:     function isDiamondStorageFrozen() external view returns (bool) {

162:     function isFacetFreezable(address _facet) external view returns (bool isFreezable) {

175:     function getPriorityTxMaxGasLimit() external view returns (uint256) {

180:     function isFunctionFreezable(bytes4 _selector) external view returns (bool) {

187:     function isEthWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool) {

196:     function facets() external view returns (Facet[] memory result) {

211:     function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {

217:     function facetAddresses() external view returns (address[] memory) {

223:     function facetAddress(bytes4 _selector) external view returns (address) {

233:     function getTotalBlocksCommitted() external view returns (uint256) {

238:     function getTotalBlocksVerified() external view returns (uint256) {

243:     function getTotalBlocksExecuted() external view returns (uint256) {

248:     function storedBlockHash(uint256 _batchNumber) external view returns (bytes32) {

253:     function getL2SystemContractsUpgradeBlockNumber() external view returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

38:     function transferEthToSharedBridge() external onlyBaseTokenBridge {

135:     function _L2MessageToLog(L2Message memory _message) internal pure returns (L2Log memory) {

161:     function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPerPubdata) internal view returns (uint256) {

365:     function _hashFactoryDeps(bytes[] memory _factoryDeps) internal pure returns (uint256[] memory hashedFactoryDeps) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol

17:     function setPendingAdmin(address _newPendingAdmin) external;

25:     function setValidator(address _validator, bool _active) external;

29:     function setPorterAvailability(bool _zkPorterIsAvailable) external;

33:     function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external;

37:     function changeFeeParams(FeeParams calldata _newFeeParams) external;

40:     function setTokenMultiplier(uint128 _nominator, uint128 _denominator) external;

42:     function upgradeChainFromVersion(uint256 _protocolVersion, Diamond.DiamondCutData calldata _cutData) external;

47:     function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IAdmin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol

62:     function initialize(InitializeData calldata _initData) external returns (bytes32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol

167:     function executeBatches(StoredBatchInfo[] calldata _batchesData) external;

170:     function executeBatchesSharedBridge(uint256 _chainId, StoredBatchInfo[] calldata _batchesData) external;

176:     function revertBatches(uint256 _newLastBatch) external;

179:     function revertBatchesSharedBridge(uint256 _chainId, uint256 _newLastBatch) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol

18:     function getVerifier() external view returns (address);

21:     function getAdmin() external view returns (address);

24:     function getPendingAdmin() external view returns (address);

27:     function getBridgehub() external view returns (address);

30:     function getStateTransitionManager() external view returns (address);

33:     function getBaseToken() external view returns (address);

36:     function getBaseTokenBridge() external view returns (address);

39:     function getTotalBatchesCommitted() external view returns (uint256);

42:     function getTotalBatchesVerified() external view returns (uint256);

45:     function getTotalBatchesExecuted() external view returns (uint256);

48:     function getTotalPriorityTxs() external view returns (uint256);

55:     function getFirstUnprocessedPriorityTx() external view returns (uint256);

58:     function getPriorityQueueSize() external view returns (uint256);

61:     function priorityQueueFrontOperation() external view returns (PriorityOperation memory);

64:     function isValidator(address _address) external view returns (bool);

67:     function l2LogsRootHash(uint256 _batchNumber) external view returns (bytes32 merkleRoot);

72:     function storedBatchHash(uint256 _batchNumber) external view returns (bytes32);

75:     function getL2BootloaderBytecodeHash() external view returns (bytes32);

78:     function getL2DefaultAccountBytecodeHash() external view returns (bytes32);

81:     function getVerifierParams() external view returns (VerifierParams memory);

84:     function isDiamondStorageFrozen() external view returns (bool);

87:     function getProtocolVersion() external view returns (uint256);

90:     function getL2SystemContractsUpgradeTxHash() external view returns (bytes32);

97:     function getL2SystemContractsUpgradeBatchNumber() external view returns (uint256);

100:     function getPriorityTxMaxGasLimit() external view returns (uint256);

105:     function isEthWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool);

108:     function baseTokenGasPriceMultiplierNominator() external view returns (uint128);

111:     function baseTokenGasPriceMultiplierDenominator() external view returns (uint128);

126:     function facets() external view returns (Facet[] memory);

129:     function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory);

132:     function facetAddresses() external view returns (address[] memory facets);

135:     function facetAddress(bytes4 _selector) external view returns (address facet);

138:     function isFunctionFreezable(bytes4 _selector) external view returns (bool);

141:     function isFacetFreezable(address _facet) external view returns (bool isFreezable);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IGetters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol

14:     function getTotalBlocksCommitted() external view returns (uint256);

18:     function getTotalBlocksVerified() external view returns (uint256);

22:     function getTotalBlocksExecuted() external view returns (uint256);

28:     function storedBlockHash(uint256 _batchNumber) external view returns (bytes32);

36:     function getL2SystemContractsUpgradeBlockNumber() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/ILegacyGetters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IVerifier.sol

27:     function verificationKeyHash() external pure returns (bytes32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IVerifier.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransitionBase.sol

9:     function getName() external view returns (string memory);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IZkSyncStateTransitionBase.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/l2-deps/ISystemContext.sol

5:     function setChainId(uint256 _newChainId) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/l2-deps/ISystemContext.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

87:     function getDiamondStorage() internal pure returns (DiamondStorage storage diamondStorage) {

96:     function diamondCut(DiamondCutData memory _diamondCut) internal {

126:     function _addFunctions(address _facet, bytes4[] memory _selectors, bool _isFacetFreezable) private {

149:     function _replaceFunctions(address _facet, bytes4[] memory _selectors, bool _isFacetFreezable) private {

172:     function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

189:     function _saveFacetIfNew(address _facet) private {

205:     function _addOneFunction(address _facet, bytes4 _selector, bool _isSelectorFreezable) private {

228:     function _removeOneFunction(address _facet, bytes4 _selector) private {

278:     function _initializeDiamondCut(address _init, bytes memory _calldata) private {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/LibMap.sol

18:     function get(Uint32Map storage _map, uint256 _index) internal view returns (uint32 result) {

38:     function set(Uint32Map storage _map, uint256 _index, uint32 _value) internal {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/LibMap.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Merkle.sol

40:     function _efficientHash(bytes32 _lhs, bytes32 _rhs) private pure returns (bytes32 result) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol

35:     function getFirstUnprocessedPriorityTx(Queue storage _queue) internal view returns (uint256) {

40:     function getTotalPriorityTxs(Queue storage _queue) internal view returns (uint256) {

45:     function getSize(Queue storage _queue) internal view returns (uint256) {

50:     function isEmpty(Queue storage _queue) internal view returns (bool) {

55:     function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {

64:     function front(Queue storage _queue) internal view returns (PriorityOperation memory) {

72:     function popFront(Queue storage _queue) internal returns (PriorityOperation memory priorityOperation) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

46:     function validateUpgradeTransaction(L2CanonicalTransaction memory _transaction) internal pure {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

67:     function upgrade(ProposedUpgrade calldata _proposedUpgrade) public virtual returns (bytes32) {

92:     function _setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) private {

109:     function _setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) private {

126:     function _setVerifier(IVerifier _newVerifier) private {

142:     function _setVerifierParams(VerifierParams calldata _newVerifierParams) private {

163:     function _upgradeVerifier(address _newVerifier, VerifierParams calldata _verifierParams) internal {

171:     function _setBaseSystemContracts(bytes32 _bootloaderHash, bytes32 _defaultAccountHash) internal {

222:     function _verifyFactoryDeps(bytes[] calldata _factoryDeps, uint256[] calldata _expectedHashes) private pure {

236:     function _setNewProtocolVersion(uint256 _newProtocolVersion) internal virtual {

263:     function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

269:     function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol

20:     function _setNewProtocolVersion(uint256 _newProtocolVersion) internal override {

47:     function upgrade(ProposedUpgrade calldata _proposedUpgrade) public virtual override returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgradeGenesis.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

13:     function upgrade(ProposedUpgrade calldata _proposedUpgrade) public override returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/GenesisUpgrade.sol

14:     function upgrade(ProposedUpgrade calldata _proposedUpgrade) public override returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/GenesisUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/IDefaultUpgrade.sol

8:     function upgrade(ProposedUpgrade calldata _upgrade) external returns (bytes32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/IDefaultUpgrade.sol)

```solidity
File: contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

28:     function applyL1ToL2Alias(address l1Address) internal pure returns (address l2Address) {

38:     function undoL1ToL2Alias(address l2Address) internal pure returns (address l1Address) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

35:     function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

46:     function storeAccountConstructedCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

54:     function markAccountCodeHashAsConstructed(address _address) external override onlyDeployer {

68:     function _storeCodeHash(address _address, bytes32 _hash) internal {

78:     function getRawCodeHash(address _address) public view override returns (bytes32 codeHash) {

89:     function getCodeHash(uint256 _input) external view override returns (bytes32) {

117:     function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

44:     function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) {

139:     function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

229:     function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

21:     function upgrade(address _delegateTo, bytes calldata _calldata) external payable {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

251:     function _sliceToUint256(bytes calldata _calldataSlice) internal pure returns (uint256 number) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

34:     function getAccountInfo(address _address) external view returns (AccountInfo memory info) {

40:     function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {

58:     function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal {

65:     function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {

74:     function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {

217:     function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf {

244:     function forceDeployKeccak256(bytes32 _keccak256BytecodeHash) external payable onlyCallFrom(FORCE_DEPLOYER) {

260:     function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable {

323:     function _ensureBytecodeIsKnown(bytes32 _bytecodeHash) internal view {

331:     function _storeConstructingByteCodeHashOnAddress(address _newAddress, bytes32 _bytecodeHash) internal {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

125:     function executeTransactionFromOutside(Transaction calldata _transaction) external payable override {

131:     function _execute(Transaction calldata _transaction) internal {

159:     function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

27:     function getImmutable(address _dest, uint256 _index) external view override returns (bytes32) {

34:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

27:     function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyCallFromBootloader {

39:     function markBytecodeAsPublished(bytes32 _bytecodeHash) external onlyCompressor {

46:     function _markBytecodeAsPublished(bytes32 _bytecodeHash, bool _shouldSendToL1) internal {

65:     function getMarker(bytes32 _hash) public view override returns (uint256 marker) {

74:     function _validateBytecode(bytes32 _bytecodeHash) internal pure {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

50:     function keccakGasCost(uint256 _length) internal pure returns (uint256) {

61:     function sha256GasCost(uint256 _length) internal pure returns (uint256) {

94:     function _processL2ToL1Log(L2ToL1Log memory _l2ToL1Log) internal returns (uint256 logIdInMerkleTree) {

116:     function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/L2BaseToken.sol

32:     function transferFromTo(address _from, address _to, uint256 _amount) external override {

56:     function balanceOf(uint256 _account) external view override returns (uint256) {

64:     function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {

72:     function withdraw(address _l1Receiver) external payable override {

85:     function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override {

99:     function _burnMsgValue() internal returns (uint256 amount) {

112:     function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {

128:     function name() external pure override returns (string memory) {

134:     function symbol() external pure override returns (string memory) {

140:     function decimals() external pure override returns (uint8) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L2BaseToken.sol)

```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

25:     function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

46:     function getMinNonce(address _address) public view returns (uint256) {

57:     function getRawNonce(address _address) public view returns (uint256) {

65:     function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {

82:     function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {

102:     function getValueUnderNonce(uint256 _key) public view returns (uint256) {

110:     function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {

125:     function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {

135:     function incrementDeploymentNonce(address _address) external returns (uint256 prevDeploymentNonce) {

154:     function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {

166:     function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view {

179:     function _splitRawNonce(uint256 _rawMinNonce) internal pure returns (uint256 deploymentNonce, uint256 minNonce) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

21:     function chunkAndPublishPubdata(bytes calldata _pubdata) external onlyCallFrom(address(L1_MESSENGER_CONTRACT)) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

84:     function setChainId(uint256 _newChainId) external onlyCallFromForceDeployer {

93:     function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {

99:     function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {

109:     function getBlockHashEVM(uint256 _block) external view returns (bytes32 hash) {

143:     function getBatchHash(uint256 _batchNumber) external view returns (bytes32 hash) {

149:     function getBatchNumberAndTimestamp() public view returns (uint128 batchNumber, uint128 batchTimestamp) {

157:     function getL2BlockNumberAndTimestamp() public view returns (uint128 blockNumber, uint128 blockTimestamp) {

167:     function getBlockNumber() public view returns (uint128) {

175:     function getBlockTimestamp() public view returns (uint128) {

182:     function _getLatest257L2blockHash(uint256 _block) internal view returns (bytes32) {

189:     function _setL2BlockHash(uint256 _block, bytes32 _hash) internal {

210:     function _calculateLegacyL2BlockHash(uint128 _blockNumber) internal pure returns (bytes32) {

218:     function _upgradeL2Blocks(uint128 _l2BlockNumber, bytes32 _expectedPrevL2BlockHash, bool _isFirstInBatch) internal {

291:     function _setNewL2BlockData(uint128 _l2BlockNumber, uint128 _l2BlockTimestamp, bytes32 _prevL2BlockHash) internal {

379:     function appendTransactionToCurrentL2Block(bytes32 _txHash) external onlyCallFromBootloader {

385:     function publishTimestampDataToL1() external onlyCallFromBootloader {

404:     function _ensureBatchConsistentWithL2Block(uint128 _newTimestamp) internal view {

459:     function incrementTxNumberInBatch() external onlyCallFromBootloader {

463:     function resetTxNumberInBatch() external onlyCallFromBootloader {

473:     function currentBlockInfo() external view returns (uint256 blockInfo) {

480:     function getBlockNumberAndTimestamp() external view returns (uint256 blockNumber, uint256 blockTimestamp) {

486:     function blockHash(uint256 _blockNumber) external view returns (bytes32 hash) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="NC-10"></a>[NC-10] Change int to int256
Throughout the code base, some variables are declared as `int`. To favor explicitness, consider changing all instances of `int` to `int256`

*Instances (18)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

582:         require(success, "failed to call point evaluation precompile");

609:             bytes32 openingPoint = bytes32(

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

296:         _params.valueToMint = _mintValue;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

81:             uint256 rInt = uint256(bytes32(_transaction.signature[0:32]));

86:             uint256 sInt = uint256(bytes32(_transaction.signature[32:64]));

91:             uint256 vInt = uint256(uint8(_transaction.signature[64]));

92:             require(vInt == 27 || vInt == 28, "Invalid v value");

97:                 vInt += 8 + block.chainid * 2;

180:             uint256 rInt = uint256(bytes32(_transaction.signature[0:32]));

185:             uint256 sInt = uint256(bytes32(_transaction.signature[32:64]));

190:             uint256 vInt = uint256(uint8(_transaction.signature[64]));

191:             require(vInt == 27 || vInt == 28, "Invalid v value");

193:             vEncoded = RLPEncoder.encodeUint256(vInt - 27);

275:             uint256 rInt = uint256(bytes32(_transaction.signature[0:32]));

280:             uint256 sInt = uint256(bytes32(_transaction.signature[32:64]));

285:             uint256 vInt = uint256(uint8(_transaction.signature[64]));

286:             require(vInt == 27 || vInt == 28, "Invalid v value");

288:             vEncoded = RLPEncoder.encodeUint256(vInt - 27);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

### <a name="NC-11"></a>[NC-11] Change uint to uint256
Throughout the code base, some variables are declared as `uint`. To favor explicitness, consider changing all instances of `uint` to `uint256`

*Instances (1)*:
```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

27:         uint256 addressAsUint = SystemContractHelper.getExtraAbiData(1);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)

### <a name="NC-12"></a>[NC-12] Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*Instances (8)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

155:             uint256 amount = _depositFunds(_prevMsgSender, IERC20(_l1Token), _amount); // note if _prevMsgSender is this contract, this will return 0. This does not happen.

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

6: import {COMMIT_TIMESTAMP_NOT_OLDER, COMMIT_TIMESTAMP_APPROXIMATION_DELTA, EMPTY_STRING_KECCAK, L2_TO_L1_LOG_SERIALIZE_SIZE, MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, PACKED_L2_BLOCK_TIMESTAMP_MASK, PUBLIC_INPUT_SHIFT, POINT_EVALUATION_PRECOMPILE_ADDR} from "../../../common/Config.sol";

7: import {IExecutor, L2_LOG_ADDRESS_OFFSET, L2_LOG_KEY_OFFSET, L2_LOG_VALUE_OFFSET, SystemLogKey, LogProcessingOutput, PubdataSource, BLS_MODULUS, PUBDATA_COMMITMENT_SIZE, PUBDATA_COMMITMENT_CLAIMED_VALUE_OFFSET, PUBDATA_COMMITMENT_COMMITMENT_OFFSET, MAX_NUMBER_OF_BLOBS} from "../../chain-interfaces/IExecutor.sol";

12: import {L2_BOOTLOADER_ADDRESS, L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, L2_PUBDATA_CHUNK_PUBLISHER_ADDR} from "../../../common/L2ContractAddresses.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

10: import {L1_MESSENGER_CONTRACT, INITIAL_WRITE_STARTING_POSITION, COMPRESSED_INITIAL_WRITE_SIZE, STATE_DIFF_ENTRY_SIZE, STATE_DIFF_ENUM_INDEX_OFFSET, STATE_DIFF_FINAL_VALUE_OFFSET, STATE_DIFF_DERIVED_KEY_OFFSET, DERIVED_KEY_LENGTH, VALUE_LENGTH, ENUM_INDEX_LENGTH, KNOWN_CODE_STORAGE_CONTRACT} from "./Constants.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

7: import {CREATE2_PREFIX, CREATE_PREFIX, NONCE_HOLDER_SYSTEM_CONTRACT, ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT, FORCE_DEPLOYER, MAX_SYSTEM_CONTRACT_ADDRESS, KNOWN_CODE_STORAGE_CONTRACT, BASE_TOKEN_SYSTEM_CONTRACT, IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT, COMPLEX_UPGRADER_CONTRACT, KECCAK256_SYSTEM_CONTRACT} from "./Constants.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

5: import {IL1Messenger, L2ToL1Log, L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, L2_TO_L1_LOG_SERIALIZE_SIZE, STATE_DIFF_COMPRESSION_VERSION_NUMBER} from "./interfaces/IL1Messenger.sol";

10: import {SystemLogKey, SYSTEM_CONTEXT_CONTRACT, KNOWN_CODE_STORAGE_CONTRACT, COMPRESSOR_CONTRACT, STATE_DIFF_ENTRY_SIZE, MAX_ALLOWED_PUBDATA_PER_BATCH, L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, PUBDATA_CHUNK_PUBLISHER} from "./Constants.sol";

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

### <a name="NC-13"></a>[NC-13] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (24)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

64:         require(msg.sender == address(sharedBridge), "Not shared bridge");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

69:         require(msg.sender == address(bridgehub), "ShB not BH");

84:         require(msg.sender == address(legacyBridge), "ShB not legacy bridge");

132:         require(msg.sender == ERA_DIAMOND_PROXY, "ShB: not era diamond proxy");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

43:         require(msg.sender == deployer || msg.sender == owner(), "Bridgehub: not owner or deployer");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

59:         require(msg.sender == address(this), "Only governance contract itself is allowed to call this function");

65:         require(msg.sender == securityCouncil, "Only security council is allowed to call this function");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

58:         require(msg.sender == bridgehub, "StateTransition: only bridgehub");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

62:         require(msg.sender == stateTransitionManager.getChainAdmin(_chainId), "ValidatorTimelock: only chain admin");

68:         require(validators[_chainId][msg.sender] == true, "ValidatorTimelock: only validator");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

34:         require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol

16:         require(msg.sender == s.admin, "StateTransition Chain: not admin");

22:         require(s.validators[msg.sender], "StateTransition Chain: not validator");

27:         require(msg.sender == s.stateTransitionManager, "StateTransition Chain: not state transition manager");

32:         require(msg.sender == s.bridgehub, "StateTransition Chain: not bridgehub");

53:         require(msg.sender == s.baseTokenBridge, "Only shared bridge can call this function");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/ZkSyncStateTransitionBase.sol)

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

26:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

22:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

29:         require(msg.sender == address(this), "Callable only by self");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

29:         if (msg.sender != BOOTLOADER_FORMAL_ADDRESS) {

222:         assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

35:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

20:         require(msg.sender == address(COMPRESSOR_CONTRACT), "Callable only by the compressor");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

89:             require(isNonceUsed(msg.sender, _key - 1), "Previous nonce has not been used");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

### <a name="NC-14"></a>[NC-14] Consider using named mappings
Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/questions/51629/how-to-name-the-arguments-in-mapping/145555#145555) to make it easier to understand the purpose of each mapping

*Instances (8)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

45:     mapping(address => uint256) private __DEPRECATED_lastWithdrawalLimitReset;

48:     mapping(address => uint256) private __DEPRECATED_withdrawnAmountInWindow;

51:     mapping(address => mapping(address => uint256)) private __DEPRECATED_totalDepositedAmountPerUser;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

30:     mapping(uint256 => address) public stateTransition;

48:     mapping(uint256 => bytes32) public upgradeCutHash;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

47:     mapping(uint256 => LibMap.Uint32Map) internal committedBatchTimestamp;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol

120:     mapping(address => uint256) __DEPRECATED_totalDepositedAmountPerUser;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/ZkSyncStateTransitionStorage.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

26:     mapping(address => AccountInfo) internal accountInfo;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

### <a name="NC-15"></a>[NC-15] `address`s shouldn't be hard-coded
It is often better to declare `address`es as `immutable`, and assign them via constructor arguments. This allows the code to remain the same across deployments on different networks, and avoids recompilation when addresses need to change.

*Instances (1)*:
```solidity
File: contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

22:     uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol)

### <a name="NC-16"></a>[NC-16] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (1)*:
```solidity
File: system-contracts/contracts/DefaultAccount.sol

151:             EfficientCall.propagateRevert();

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

### <a name="NC-17"></a>[NC-17] TODO Left in the code
TODOs may signal that a feature is missing or not ready for audit, consider resolving the issue and removing the TODO comment

*Instances (1)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

350:                 layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

### <a name="NC-18"></a>[NC-18] Some require descriptions are not clear
1. It does not comply with the general require error description model of the project (Either all of them should be debugged in this way, or all of them should be explained with a string not exceeding 32 bytes.)
2. For debug dapps like Tenderly, these debug messages are important, this allows the user to see the reasons for revert practically.

*Instances (111)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

141:         require(_amount != 0, "0T"); // empty deposit

143:         require(amount == _amount, "3T"); // The token has non-standard transfer logic

186:         require(amount != 0, "2T"); // empty deposit

215:         require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

156:             require(amount == _amount, "3T"); // The token has non-standard transfer logic

201:             require(withdrawAmount == _depositAmount, "5T"); // The token has non-standard transfer logic

203:         require(amount != 0, "6T"); // empty deposit amount

320:             require(proofValid, "yn");

322:         require(_amount > 0, "y1");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/common/ReentrancyGuard.sol

58:         require(lockSlotOldValue == 0, "1B");

75:         require(_status == _NOT_ENTERED, "r1");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol)

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

23:         require(_bytecode.length % 32 == 0, "pq");

26:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

27:         require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

41:         require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

43:         require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

195:                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed

217:                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

25:         require(address(_initializeData.verifier) != address(0), "vt");

26:         require(_initializeData.admin != address(0), "vy");

27:         require(_initializeData.validatorTimelock != address(0), "hc");

28:         require(_initializeData.priorityTxMaxGasLimit <= MAX_GAS_PER_TRANSACTION, "vu");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol

14:         require(_chainId == block.chainid, "pr");

25:         require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

30:         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

31:         require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondProxy.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol

34:         require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

59:         require(_newPriorityTxMaxGasLimit <= MAX_GAS_PER_TRANSACTION, "n5");

70:         require(_newFeeParams.maxPubdataPerBatch >= _newFeeParams.priorityTxMaxPubdata, "n6");

130:         require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

140:         require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Admin.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

36:         require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

39:         require(pubdataSource == uint8(PubdataSource.Calldata) || pubdataSource == uint8(PubdataSource.Blob), "us");

69:         require(_previousBatch.batchHash == logOutput.previousBatchHash, "l");

71:         require(logOutput.chainedPriorityTxsHash == _newBatch.priorityOperationsHash, "t");

73:         require(logOutput.numberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");

105:         require(batchTimestamp == _expectedBatchTimestamp, "tb");

109:         require(_previousBatchTimestamp < batchTimestamp, "h3");

117:         require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small

118:         require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

144:             require(!_checkBit(processedLogs, uint8(logKey)), "kp");

149:                 require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");

152:                 require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln");

155:                 require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb");

158:                 require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc");

161:                 require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv");

164:                 require(logSender == L2_BOOTLOADER_ADDRESS, "bl");

167:                 require(logSender == L2_BOOTLOADER_ADDRESS, "bk");

170:                 require(logSender == L2_PUBDATA_CHUNK_PUBLISHER_ADDR, "pc");

173:                 require(logSender == L2_PUBDATA_CHUNK_PUBLISHER_ADDR, "pd");

176:                 require(logSender == L2_BOOTLOADER_ADDRESS, "bu");

177:                 require(_expectedSystemContractUpgradeTxHash == logValue, "ut");

187:             require(processedLogs == 511, "b7");

189:             require(processedLogs == 1023, "b8");

226:         require(_newBatchesData.length == 1, "e4");

228:         require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data

279:         require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");

318:         require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order

325:         require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x"); // priority operations hash does not match to expected

353:         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

397:         require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

416:         require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");

437:         require(proofPublicInput.length == 1, "t4");

444:         require(successVerifyProof, "p"); // Proof verification fail

477:         require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch

478:         require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

530:         require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu");

597:         require(_pubdataCommitments.length > 0, "pl");

598:         require(_pubdataCommitments.length <= PUBDATA_COMMITMENT_SIZE * MAX_NUMBER_OF_BLOBS, "bd");

599:         require(_pubdataCommitments.length % PUBDATA_COMMITMENT_SIZE == 0, "bs");

605:             require(blobVersionedHash != bytes32(0), "vh");

629:         require(versionedHash == bytes32(0), "lh");

647:         require(success, "vc");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

182:         require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

115:         require(_batchNumber <= s.totalBatchesExecuted, "xx");

122:         require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

248:         require(_request.l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

276:         require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");

284:         require(_mintValue >= baseCost + _params.l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

107:             require(selectors.length > 0, "B"); // no functions for diamond cut

132:         require(_facet.code.length > 0, "G");

141:             require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

155:         require(_facet.code.length > 0, "K");

161:             require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

175:         require(_facet == address(0), "a1"); // facet address must be zero

181:             require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

215:             require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");

280:             require(_calldata.length == 0, "H"); // Non-empty calldata for zero address

297:             require(data.length == 32, "lp");

298:             require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Merkle.sol

24:         require(pathLength > 0, "xc");

25:         require(pathLength < 256, "bt");

26:         require(_index < (1 << pathLength), "px");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Merkle.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol

65:         require(!_queue.isEmpty(), "D"); // priority queue is empty

73:         require(!_queue.isEmpty(), "s"); // priority queue is empty

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/PriorityQueue.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

28:         require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");

30:         require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= _priorityTxMaxPubdata, "uk");

48:         require(_transaction.from <= type(uint16).max, "ua");

49:         require(_transaction.to <= type(uint160).max, "ub");

50:         require(_transaction.paymaster == 0, "uc");

51:         require(_transaction.value == 0, "ud");

52:         require(_transaction.maxFeePerGas == 0, "uq");

53:         require(_transaction.maxPriorityFeePerGas == 0, "ux");

54:         require(_transaction.reserved[0] == 0, "ue");

55:         require(_transaction.reserved[1] <= type(uint160).max, "uf");

56:         require(_transaction.reserved[2] == 0, "ug");

57:         require(_transaction.reserved[3] == 0, "uo");

58:         require(_transaction.signature.length == 0, "uh");

59:         require(_transaction.paymasterInput.length == 0, "ul1");

60:         require(_transaction.reservedDynamic.length == 0, "um");

115:         require(_totalGasLimit >= overhead, "my"); // provided gas limit doesn't cover transaction overhead

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

### <a name="NC-19"></a>[NC-19] Use Underscores for Number Literals (add an underscore every 3 digits)

*Instances (5)*:
```solidity
File: contracts/ethereum/contracts/common/Config.sol

92: uint256 constant TX_SLOT_OVERHEAD_L2_GAS = 10000;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/Config.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

189:             require(processedLogs == 1023, "b8");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol

50: uint256 constant BLS_MODULUS = 52435875175126190479447740508185965837690552500527637822603658699938581184513;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IExecutor.sol)

```solidity
File: system-contracts/contracts/Constants.sol

94: uint256 constant MAX_ALLOWED_PUBDATA_PER_BATCH = 520000;

111: uint256 constant L2_TO_L1_LOGS_MERKLE_TREE_LEAVES = 4096;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Constants.sol)

### <a name="NC-20"></a>[NC-20] Constants should be defined rather than using magic numbers

*Instances (11)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

253:             bytes memory decimals = abi.encode(uint8(18));

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

129:             uint64 enumIndex = stateDiff.readUint64(84);

137:             bytes32 derivedKey = stateDiff.readBytes32(52);

138:             uint256 initValue = stateDiff.readUint256(92);

139:             uint256 finalValue = stateDiff.readUint256(124);

161:             uint64 enumIndex = stateDiff.readUint64(84);

166:             uint256 initValue = stateDiff.readUint256(92);

167:             uint256 finalValue = stateDiff.readUint256(124);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

89:         uint256 gasToPay = keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) + 2 * keccakGasCost(64);

155:             keccakGasCost(64) +

182:         uint256 gasToPay = pubdataLen * gasPerPubdataBytes + sha256GasCost(bytecodeLen) + keccakGasCost(64);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

### <a name="NC-21"></a>[NC-21] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (37)*:
```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

225:         for (uint256 i = 0; i < _calls.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

116:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

135:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

185:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

209:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

137:         for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

252:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

284:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

306:         for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

346:         for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

400:         for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

595:         uint256 versionedHashIndex = 0;

602:         for (uint256 i = 0; i < _pubdataCommitments.length; i += PUBDATA_COMMITMENT_SIZE) {

633:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol

202:         for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Getters.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

368:         for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

101:         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

158:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

178:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

92:         uint256 costForPubdata = 0;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

226:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)

```solidity
File: system-contracts/contracts/Compressor.sol

61:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

124:         uint256 numInitialWritesProcessed = 0;

127:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

159:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

269:         uint256 sumOfValues = 0;

270:         for (uint256 i = 0; i < deploymentsLength; ++i) {

275:         for (uint256 i = 0; i < deploymentsLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

38:             for (uint256 i = 0; i < immutablesLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol)

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

30:             for (uint256 i = 0; i < hashesLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

201:         uint256 calldataPtr = 0;

210:         for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {

228:             for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

240:         for (uint256 i = 0; i < numberOfMessages; ++i) {

258:         for (uint256 i = 0; i < numberOfBytecodes; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/PubdataChunkPublisher.sol

36:         for (uint256 i = 0; i < MAX_NUMBER_OF_BLOBS; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/PubdataChunkPublisher.sol)

### <a name="NC-22"></a>[NC-22] No need to check that `v == 27` or `v == 28` with `ecrecover`
This [Tweeter thread from Alex Beregszaszi aka Axic](https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg) explains it well and the [Yellow Paper has been changed](https://pbs.twimg.com/media/FUu--C-XEAELc2A?format=jpg&name=large) in direct consequence to it.

> The Yellow Paper makes it look like nothing is checked.

> Digging into the clients, it turns out the precompile actually checks if the value is 27 or 28. No need to do this on the caller side!

> The change has been merged into the Yellow Paper

*Instances (4)*:
```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

92:             require(vInt == 27 || vInt == 28, "Invalid v value");

191:             require(vInt == 27 || vInt == 28, "Invalid v value");

286:             require(vInt == 27 || vInt == 28, "Invalid v value");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol)

```solidity
File: system-contracts/contracts/DefaultAccount.sol

173:         require(v == 27 || v == 28, "v is neither 27 nor 28");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Use of `tx.origin` is unsafe in almost every context | 3 |
| [L-2](#L-2) | Use of `ecrecover` is susceptible to signature malleability | 1 |
| [L-3](#L-3) | Use of `tx.origin` is unsafe in almost every context | 3 |
| [L-4](#L-4) | Division by zero not prevented | 3 |
| [L-5](#L-5) | External call recipient may consume all transaction gas | 5 |
| [L-6](#L-6) | Fallback lacking `payable` | 1 |
| [L-7](#L-7) | Initializers could be front-run | 8 |
| [L-8](#L-8) | Signature use at deadlines should be allowed | 2 |
| [L-9](#L-9) | Loss of precision | 4 |
| [L-10](#L-10) | Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership` | 5 |
| [L-11](#L-11) | Upgradeable contract not initialized | 57 |
| [L-12](#L-12) | Use of ecrecover is susceptible to signature malleability | 1 |
### <a name="L-1"></a>[L-1] Use of `tx.origin` is unsafe in almost every context
According to [Vitalik Buterin](https://ethereum.stackexchange.com/questions/196/how-do-i-make-my-dapp-serenity-proof), contracts should _not_ `assume that tx.origin will continue to be usable or meaningful`. An example of this is [EIP-3074](https://eips.ethereum.org/EIPS/eip-3074#allowing-txorigin-as-signer-1) which explicitly mentions the intention to change its semantics when it's used with new op codes. There have also been calls to [remove](https://github.com/ethereum/solidity/issues/683) `tx.origin`, and there are [security issues](solidity.readthedocs.io/en/v0.4.24/security-considerations.html#tx-origin) associated with using it for authorization. For these reasons, it's best to completely avoid the feature.

*Instances (3)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

574:                 refundRecipient = _prevMsgSender != tx.origin

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

308:             _recipient = msg.sender == tx.origin ? msg.sender : AddressAliasHelper.applyL1ToL2Alias(msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

239:         if (l2Sender != tx.origin) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

### <a name="L-2"></a>[L-2] Use of `ecrecover` is susceptible to signature malleability
The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References:  <https://swcregistry.io/docs/SWC-117>,  <https://swcregistry.io/docs/SWC-121>, and  <https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57>.
While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

*Instances (1)*:
```solidity
File: system-contracts/contracts/DefaultAccount.sol

186:         address recoveredAddress = ecrecover(_hash, v, r, s);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)

### <a name="L-3"></a>[L-3] Use of `tx.origin` is unsafe in almost every context
According to [Vitalik Buterin](https://ethereum.stackexchange.com/questions/196/how-do-i-make-my-dapp-serenity-proof), contracts should _not_ `assume that tx.origin will continue to be usable or meaningful`. An example of this is [EIP-3074](https://eips.ethereum.org/EIPS/eip-3074#allowing-txorigin-as-signer-1) which explicitly mentions the intention to change its semantics when it's used with new op codes. There have also been calls to [remove](https://github.com/ethereum/solidity/issues/683) `tx.origin`, and there are [security issues](solidity.readthedocs.io/en/v0.4.24/security-considerations.html#tx-origin) associated with using it for authorization. For these reasons, it's best to completely avoid the feature.

*Instances (3)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

574:                 refundRecipient = _prevMsgSender != tx.origin

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

308:             _recipient = msg.sender == tx.origin ? msg.sender : AddressAliasHelper.applyL1ToL2Alias(msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

239:         if (l2Sender != tx.origin) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

### <a name="L-4"></a>[L-4] Division by zero not prevented
The divisions below take an input parameter which does not have any zero-value checks, which may lead to the functions reverting when zero is passed.

*Instances (3)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

176:         uint256 l2GasPrice = feeParams.minimalL2GasPrice + batchOverheadBaseToken / uint256(feeParams.maxL2GasPerBatch);

177:         uint256 minL2GasPriceBaseToken = (fullPubdataPriceBaseToken + _gasPerPubdata - 1) / _gasPerPubdata;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol

30:         require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= _priorityTxMaxPubdata, "uk");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/TransactionValidator.sol)

### <a name="L-5"></a>[L-5] External call recipient may consume all transaction gas
There is no limit specified on the amount of gas used, so the recipient can use up all of the transaction's gas, causing it to revert. Use `addr.call{gas: <amount>}("")` or [this](https://github.com/nomad-xyz/ExcessivelySafeCall) library instead.

*Instances (5)*:
```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

174:         _execute(_operation.calls);

193:         _execute(_operation.calls);

226:             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: system-contracts/contracts/ContractDeployer.sol

235:             _deployment.callConstructor

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)

```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

46:             (bool success, ) = address(BASE_TOKEN_SYSTEM_CONTRACT).call(

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)

### <a name="L-6"></a>[L-6] Fallback lacking `payable`

*Instances (1)*:
```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

39:     fallback(bytes calldata _data) external onlySystemCall returns (bytes memory) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)

### <a name="L-7"></a>[L-7] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (8)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

60:     function initialize() external reentrancyGuardInitializer {}

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

104:     function initialize(

107:     ) external reentrancyGuardInitializer initializer {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

38:     function initialize(address _owner) external reentrancyGuardInitializer {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol

50:     function initialize(StateTransitionManagerInitializeData calldata _initalizeData) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

67:     function initialize(

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

24:     function initialize(InitializeData calldata _initializeData) external reentrancyGuardInitializer returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol

62:     function initialize(InitializeData calldata _initData) external returns (bytes32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol)

### <a name="L-8"></a>[L-8] Signature use at deadlines should be allowed
According to [EIP-2612](https://github.com/ethereum/EIPs/blob/71dc97318013bf2ac572ab63fab530ac9ef419ca/EIPS/eip-2612.md?plain=1#L58), signatures used on exactly the deadline timestamp are supposed to be allowed. While the signature may or may not be used for the exact EIP-2612 use case (transfer approvals), for consistency's sake, all deadlines should follow this semantic. If the timestamp is an expiration rather than a deadline, consider whether it makes more sense to include the expiration timestamp as a valid timestamp, as is done for deadlines.

*Instances (2)*:
```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

111:         } else if (timestamp > block.timestamp) {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol

118:         require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Executor.sol)

### <a name="L-9"></a>[L-9] Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*Instances (4)*:
```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol

176:         uint256 l2GasPrice = feeParams.minimalL2GasPrice + batchOverheadBaseToken / uint256(feeParams.maxL2GasPerBatch);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/facets/Mailbox.sol)

```solidity
File: system-contracts/contracts/L1Messenger.sol

51:         return KECCAK_ROUND_GAS_COST * (_length / KECCAK_ROUND_NUMBER_OF_BYTES + 1);

62:         return SHA256_ROUND_GAS_COST * ((_length + 8) / SHA256_ROUND_NUMBER_OF_BYTES + 1);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)

```solidity
File: system-contracts/contracts/NonceHolder.sol

180:         deploymentNonce = _rawMinNonce / DEPLOY_NONCE_MULTIPLIER;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)

### <a name="L-10"></a>[L-10] Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership`
Use [Ownable2Step.transferOwnership](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) which is safer. Use it as it is more secure due to 2-stage ownership transfer.

**Recommended Mitigation Steps**

Use <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol">Ownable2Step.sol</a>
  
  ```solidity
      function acceptOwnership() external {
          address sender = _msgSender();
          require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
          _transferOwnership(sender);
      }
```

*Instances (5)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

109:         _transferOwnership(_owner);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

39:         _transferOwnership(_owner);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

44:         _transferOwnership(_admin);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

71:         _transferOwnership(_initializeData.governor);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

56:         _transferOwnership(_initialOwner);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)

### <a name="L-11"></a>[L-11] Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*Instances (57)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

55:     constructor(IL1SharedBridge _sharedBridge) reentrancyGuardInitializer {

60:     function initialize() external reentrancyGuardInitializer {}

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)

```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

94:     ) reentrancyGuardInitializer {

95:         _disableInitializers();

104:     function initialize(

107:     ) external reentrancyGuardInitializer initializer {

137:     function initializeChainGovernance(uint256 _chainId, address _l2BridgeAddress) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

35:     constructor() reentrancyGuardInitializer {}

38:     function initialize(address _owner) external reentrancyGuardInitializer {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/common/ReentrancyGuard.sol

41:     modifier reentrancyGuardInitializer() {

42:         _initializeReentrancyGuard();

46:     function _initializeReentrancyGuard() private {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol

15: struct StateTransitionManagerInitializeData {

50:     function initialize(StateTransitionManagerInitializeData calldata _initalizeData) external;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/IStateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

52:     constructor(address _bridgehub) reentrancyGuardInitializer {

67:     function initialize(

68:         StateTransitionManagerInitializeData calldata _initializeData

69:     ) external reentrancyGuardInitializer {

70:         require(_initializeData.governor != address(0), "StateTransition: governor zero");

71:         _transferOwnership(_initializeData.governor);

73:         genesisUpgrade = _initializeData.genesisUpgrade;

74:         protocolVersion = _initializeData.protocolVersion;

75:         validatorTimelock = _initializeData.validatorTimelock;

80:             _initializeData.genesisBatchHash,

81:             _initializeData.genesisIndexRepeatedStorageChanges,

86:             _initializeData.genesisBatchCommitment

90:         initialCutHash = keccak256(abi.encode(_initializeData.diamondCut));

210:             IDiamondInit.initialize.selector,

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol

9: import {InitializeData, IDiamondInit} from "../chain-interfaces/IDiamondInit.sol";

19:     constructor() reentrancyGuardInitializer {}

24:     function initialize(InitializeData calldata _initializeData) external reentrancyGuardInitializer returns (bytes32) {

25:         require(address(_initializeData.verifier) != address(0), "vt");

26:         require(_initializeData.admin != address(0), "vy");

27:         require(_initializeData.validatorTimelock != address(0), "hc");

28:         require(_initializeData.priorityTxMaxGasLimit <= MAX_GAS_PER_TRANSACTION, "vu");

30:         s.chainId = _initializeData.chainId;

31:         s.bridgehub = _initializeData.bridgehub;

32:         s.stateTransitionManager = _initializeData.stateTransitionManager;

33:         s.baseToken = _initializeData.baseToken;

34:         s.baseTokenBridge = _initializeData.baseTokenBridge;

35:         s.protocolVersion = _initializeData.protocolVersion;

37:         s.verifier = _initializeData.verifier;

38:         s.admin = _initializeData.admin;

39:         s.validators[_initializeData.validatorTimelock] = true;

41:         s.storedBatchHashes[0] = _initializeData.storedBatchZero;

42:         s.verifierParams = _initializeData.verifierParams;

43:         s.l2BootloaderBytecodeHash = _initializeData.l2BootloaderBytecodeHash;

44:         s.l2DefaultAccountBytecodeHash = _initializeData.l2DefaultAccountBytecodeHash;

45:         s.priorityTxMaxGasLimit = _initializeData.priorityTxMaxGasLimit;

46:         s.feeParams = _initializeData.feeParams;

47:         s.blobVersionedHashRetriever = _initializeData.blobVersionedHashRetriever;

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-deps/DiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol

25: struct InitializeData {

51: struct InitializeDataNewChain {

62:     function initialize(InitializeData calldata _initData) external returns (bytes32);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/chain-interfaces/IDiamondInit.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/libraries/Diamond.sol

120:         _initializeDiamondCut(initAddress, initCalldata);

278:     function _initializeDiamondCut(address _init, bytes memory _calldata) private {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/libraries/Diamond.sol)

```solidity
File: system-contracts/contracts/SystemContext.sol

264:             require(_maxVirtualBlocksToCreate > 0, "Can't initialize the first virtual block");

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)

### <a name="L-12"></a>[L-12] Use of ecrecover is susceptible to signature malleability
The built-in EVM precompile ecrecover is susceptible to signature malleability, which could lead to replay attacks.Consider using OpenZeppelin’s ECDSA library instead of the built-in function.

*Instances (1)*:
```solidity
File: system-contracts/contracts/DefaultAccount.sol

186:         address recoveredAddress = ecrecover(_hash, v, r, s);

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Centralization Risk for trusted owners | 23 |
### <a name="M-1"></a>[M-1] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (23)*:
```solidity
File: contracts/ethereum/contracts/bridge/L1SharedBridge.sol

30: contract L1SharedBridge is IL1SharedBridge, ReentrancyGuard, Initializable, Ownable2Step {

116:     function transferTokenFromERC20Bridge(address _token) external onlyOwner {

137:     function initializeChainGovernance(uint256 _chainId, address _l2BridgeAddress) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1SharedBridge.sol)

```solidity
File: contracts/ethereum/contracts/bridgehub/Bridgehub.sol

16: contract Bridgehub is IBridgehub, ReentrancyGuard, Ownable2Step {

57:     function setDeployer(address _newDeployer) external onlyOwner {

62:     function addStateTransitionManager(address _stateTransitionManager) external onlyOwner {

72:     function removeStateTransitionManager(address _stateTransitionManager) external onlyOwner {

81:     function addToken(address _token) external onlyOwner {

88:     function setSharedBridge(address _sharedBridge) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/bridgehub/Bridgehub.sol)

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

20: contract Governance is IGovernance, Ownable2Step {

129:     function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {

142:     function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {

154:     function cancel(bytes32 _id) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/StateTransitionManager.sol

25: contract StateTransitionManager is IStateTransitionManager, ReentrancyGuard, Ownable2Step {

98:     function setValidatorTimelock(address _validatorTimelock) external onlyOwner {

103:     function setInitialCutHash(Diamond.DiamondCutData calldata _diamondCut) external onlyOwner {

112:     ) external onlyOwner {

121:     ) external onlyOwner {

126:     function freezeChain(uint256 _chainId) external onlyOwner {

131:     function revertBatches(uint256 _chainId, uint256 _newLastBatch) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/StateTransitionManager.sol)

```solidity
File: contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol

22: contract ValidatorTimelock is IExecutor, Ownable2Step {

73:     function setStateTransitionManager(IStateTransitionManager _stateTransitionManager) external onlyOwner {

96:     function setExecutionDelay(uint32 _executionDelay) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-03-zksync/blob/main/code/contracts/ethereum/contracts/state-transition/ValidatorTimelock.sol)
