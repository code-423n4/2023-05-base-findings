## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Using bools for storage incurs overhead | 9 |
| [GAS-2](#GAS-2) | Cache array length outside of loop | 3 |
| [GAS-3](#GAS-3) | For Operations that will not overflow, you could use unchecked | 324 |
| [GAS-4](#GAS-4) | Use Custom Errors | 23 |
| [GAS-5](#GAS-5) | Don't initialize variables with default value | 6 |
| [GAS-6](#GAS-6) | Long revert strings | 11 |
| [GAS-7](#GAS-7) | Functions guaranteed to revert when called by normal users can be marked `payable` | 15 |
| [GAS-8](#GAS-8) | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 5 |
| [GAS-9](#GAS-9) | Using `private` rather than `public` for constants, saves gas | 3 |
| [GAS-10](#GAS-10) | Use != 0 instead of > 0 for unsigned integer comparison | 12 |
### <a name="GAS-1"></a>[GAS-1] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (9)*:
```solidity
File: contracts/L1/deployment/ChugSplashDictator.sol

18:     bool public isUpgrading = true;

```

```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

56:     mapping(bytes32 => bool) public blockedMessages;

57:     mapping(bytes32 => bool) public relayedMessages;

58:     mapping(bytes32 => bool) public successfulMessages;

```

```solidity
File: contracts/L2/messaging/L2CrossDomainMessenger.sol

25:     mapping(bytes32 => bool) public relayedMessages;

26:     mapping(bytes32 => bool) public successfulMessages;

27:     mapping(bytes32 => bool) public sentMessages;

```

```solidity
File: contracts/L2/predeploys/OVM_DeployerWhitelist.sol

26:     mapping(address => bool) public whitelist;

```

```solidity
File: contracts/L2/predeploys/OVM_L2ToL1MessagePasser.sol

19:     mapping(bytes32 => bool) public sentMessages;

```

### <a name="GAS-2"></a>[GAS-2] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (3)*:
```solidity
File: contracts/L1/deployment/AddressDictator.sol

52:         for (uint256 i = 0; i < _names.length; i++) {

67:         for (uint256 i = 0; i < namedAddresses.length; i++) {

```

```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

152:         for (uint256 i = 0; i < _data.length; i++) {

```

### <a name="GAS-3"></a>[GAS-3] For Operations that will not overflow, you could use unchecked

*Instances (324)*:
```solidity
File: contracts/L1/deployment/AddressDictator.sol

4: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

4: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

4: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

4: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

52:         for (uint256 i = 0; i < _names.length; i++) {

52:         for (uint256 i = 0; i < _names.length; i++) {

67:         for (uint256 i = 0; i < namedAddresses.length; i++) {

67:         for (uint256 i = 0; i < namedAddresses.length; i++) {

```

```solidity
File: contracts/L1/deployment/ChugSplashDictator.sol

4: import { L1ChugSplashProxy } from "../../chugsplash/L1ChugSplashProxy.sol";

4: import { L1ChugSplashProxy } from "../../chugsplash/L1ChugSplashProxy.sol";

4: import { L1ChugSplashProxy } from "../../chugsplash/L1ChugSplashProxy.sol";

5: import { iL1ChugSplashDeployer } from "../../chugsplash/interfaces/iL1ChugSplashDeployer.sol";

5: import { iL1ChugSplashDeployer } from "../../chugsplash/interfaces/iL1ChugSplashDeployer.sol";

5: import { iL1ChugSplashDeployer } from "../../chugsplash/interfaces/iL1ChugSplashDeployer.sol";

5: import { iL1ChugSplashDeployer } from "../../chugsplash/interfaces/iL1ChugSplashDeployer.sol";

```

```solidity
File: contracts/L1/messaging/IL1CrossDomainMessenger.sol

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

8: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

8: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

8: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

8: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

```

```solidity
File: contracts/L1/messaging/IL1StandardBridge.sol

4: import "./IL1ERC20Bridge.sol";

```

```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

7: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

7: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

7: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

7: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

8: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

8: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

8: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

8: import { Lib_AddressManager } from "../../libraries/resolver/Lib_AddressManager.sol";

9: import { Lib_SecureMerkleTrie } from "../../libraries/trie/Lib_SecureMerkleTrie.sol";

9: import { Lib_SecureMerkleTrie } from "../../libraries/trie/Lib_SecureMerkleTrie.sol";

9: import { Lib_SecureMerkleTrie } from "../../libraries/trie/Lib_SecureMerkleTrie.sol";

9: import { Lib_SecureMerkleTrie } from "../../libraries/trie/Lib_SecureMerkleTrie.sol";

10: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

10: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

10: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

10: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

11: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

11: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

11: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

11: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

12: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

12: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

12: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

15: import { IL1CrossDomainMessenger } from "./IL1CrossDomainMessenger.sol";

16: import { ICanonicalTransactionChain } from "../rollup/ICanonicalTransactionChain.sol";

16: import { ICanonicalTransactionChain } from "../rollup/ICanonicalTransactionChain.sol";

17: import { IStateCommitmentChain } from "../rollup/IStateCommitmentChain.sol";

17: import { IStateCommitmentChain } from "../rollup/IStateCommitmentChain.sol";

22: } from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

22: } from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

22: } from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

22: } from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

25: } from "@openzeppelin/contracts-upgradeable/security/PausableUpgradeable.sol";

25: } from "@openzeppelin/contracts-upgradeable/security/PausableUpgradeable.sol";

25: } from "@openzeppelin/contracts-upgradeable/security/PausableUpgradeable.sol";

25: } from "@openzeppelin/contracts-upgradeable/security/PausableUpgradeable.sol";

28: } from "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

28: } from "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

28: } from "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

28: } from "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

90:         __Context_init_unchained(); // Context is a dependency for both Ownable and Pausable

90:         __Context_init_unchained(); // Context is a dependency for both Ownable and Pausable

198:             "Cannot send L2->L1 messages to L1 system contracts."

```

```solidity
File: contracts/L1/messaging/L1StandardBridge.sol

5: import { IL1StandardBridge } from "./IL1StandardBridge.sol";

6: import { IL1ERC20Bridge } from "./IL1ERC20Bridge.sol";

7: import { IL2ERC20Bridge } from "../../L2/messaging/IL2ERC20Bridge.sol";

7: import { IL2ERC20Bridge } from "../../L2/messaging/IL2ERC20Bridge.sol";

7: import { IL2ERC20Bridge } from "../../L2/messaging/IL2ERC20Bridge.sol";

7: import { IL2ERC20Bridge } from "../../L2/messaging/IL2ERC20Bridge.sol";

8: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

8: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

8: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

8: import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

13: import { Address } from "@openzeppelin/contracts/utils/Address.sol";

13: import { Address } from "@openzeppelin/contracts/utils/Address.sol";

13: import { Address } from "@openzeppelin/contracts/utils/Address.sol";

14: import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

14: import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

14: import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

14: import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

14: import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

205:         deposits[_l1Token][_l2Token] = deposits[_l1Token][_l2Token] + _amount;

243:         deposits[_l1Token][_l2Token] = deposits[_l1Token][_l2Token] - _amount;

```

```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

6: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

6: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

6: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

6: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

7: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

7: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

7: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

7: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

10: import { ICanonicalTransactionChain } from "./ICanonicalTransactionChain.sol";

11: import { IChainStorageContainer } from "./IChainStorageContainer.sol";

60:     uint40 private _nextQueueIndex; // index of the first queue element not yet included

60:     uint40 private _nextQueueIndex; // index of the first queue element not yet included

76:         enqueueL2GasPrepaid = _l2GasDiscountDivisor * _enqueueGasCost;

109:         enqueueL2GasPrepaid = _l2GasDiscountDivisor * _enqueueGasCost;

123:         return IChainStorageContainer(resolve("ChainStorageContainer-CTC-batches"));

123:         return IChainStorageContainer(resolve("ChainStorageContainer-CTC-batches"));

193:         return uint40(queueElements.length) - _nextQueueIndex;

242:             uint256 gasToConsume = (_gasLimit - enqueueL2GasPrepaid) / l2GasDiscountDivisor;

242:             uint256 gasToConsume = (_gasLimit - enqueueL2GasPrepaid) / l2GasDiscountDivisor;

250:             while (startingGas - gasleft() < gasToConsume) {

251:                 i++;

251:                 i++;

276:         uint256 queueIndex = queueElements.length - 1;

309:             BATCH_CONTEXT_START_POS + BATCH_CONTEXT_SIZE * numContexts

309:             BATCH_CONTEXT_START_POS + BATCH_CONTEXT_SIZE * numContexts

323:         for (uint32 i = 0; i < numContexts; i++) {

323:         for (uint32 i = 0; i < numContexts; i++) {

330:             numSequencerTransactions += uint32(curContext.numSequencedTransactions);

333:             nextQueueIndex += uint40(curContext.numSubsequentQueueTransactions);

342:         uint40 numQueuedTransactions = totalElementsToAppend - numSequencerTransactions;

356:             Lib_OVMCodec.QueueElement memory lastElement = queueElements[nextQueueIndex - 1];

365:             blockhash(block.number - 1),

374:             nextQueueIndex - numQueuedTransactions,

394:         uint256 contextPtr = 15 + _index * BATCH_CONTEXT_SIZE;

394:         uint256 contextPtr = 15 + _index * BATCH_CONTEXT_SIZE;

528:             totalElements + uint40(header.batchSize),

529:             nextQueueIndex + uint40(_numQueuedTransactions),

```

```solidity
File: contracts/L1/rollup/ChainStorageContainer.sol

5: import { Lib_Buffer } from "../../libraries/utils/Lib_Buffer.sol";

5: import { Lib_Buffer } from "../../libraries/utils/Lib_Buffer.sol";

5: import { Lib_Buffer } from "../../libraries/utils/Lib_Buffer.sol";

5: import { Lib_Buffer } from "../../libraries/utils/Lib_Buffer.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

9: import { IChainStorageContainer } from "./IChainStorageContainer.sol";

```

```solidity
File: contracts/L1/rollup/ICanonicalTransactionChain.sol

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

8: import { IChainStorageContainer } from "./IChainStorageContainer.sol";

```

```solidity
File: contracts/L1/rollup/IStateCommitmentChain.sol

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

```

```solidity
File: contracts/L1/rollup/StateCommitmentChain.sol

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

5: import { Lib_OVMCodec } from "../../libraries/codec/Lib_OVMCodec.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

6: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

7: import { Lib_MerkleTree } from "../../libraries/utils/Lib_MerkleTree.sol";

7: import { Lib_MerkleTree } from "../../libraries/utils/Lib_MerkleTree.sol";

7: import { Lib_MerkleTree } from "../../libraries/utils/Lib_MerkleTree.sol";

7: import { Lib_MerkleTree } from "../../libraries/utils/Lib_MerkleTree.sol";

10: import { IStateCommitmentChain } from "./IStateCommitmentChain.sol";

11: import { ICanonicalTransactionChain } from "./ICanonicalTransactionChain.sol";

12: import { IBondManager } from "../verification/IBondManager.sol";

12: import { IBondManager } from "../verification/IBondManager.sol";

13: import { IChainStorageContainer } from "./IChainStorageContainer.sol";

59:         return IChainStorageContainer(resolve("ChainStorageContainer-SCC-batches"));

59:         return IChainStorageContainer(resolve("ChainStorageContainer-SCC-batches"));

107:             getTotalElements() + _batch.length <=

173:         return (timestamp + FRAUD_PROOF_WINDOW) > block.timestamp;

245:                 lastSequencerTimestamp + SEQUENCER_PUBLISH_WINDOW < block.timestamp,

272:                 uint40(batchHeader.prevTotalElements + batchHeader.batchSize),

```

```solidity
File: contracts/L1/verification/BondManager.sol

5: import { IBondManager } from "./IBondManager.sol";

8: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

8: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

8: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

8: import { Lib_AddressResolver } from "../../libraries/resolver/Lib_AddressResolver.sol";

```

```solidity
File: contracts/L2/messaging/IL2CrossDomainMessenger.sol

5: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

5: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

5: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

5: import { ICrossDomainMessenger } from "../../libraries/bridge/ICrossDomainMessenger.sol";

```

```solidity
File: contracts/L2/messaging/L2CrossDomainMessenger.sol

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

5: import { AddressAliasHelper } from "../../standards/AddressAliasHelper.sol";

6: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

6: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

6: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

6: import { Lib_CrossDomainUtils } from "../../libraries/bridge/Lib_CrossDomainUtils.sol";

7: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

7: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

7: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

7: import { Lib_DefaultValues } from "../../libraries/constants/Lib_DefaultValues.sol";

8: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

8: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

8: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

8: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

11: import { IL2CrossDomainMessenger } from "./IL2CrossDomainMessenger.sol";

12: import { iOVM_L2ToL1MessagePasser } from "../predeploys/iOVM_L2ToL1MessagePasser.sol";

12: import { iOVM_L2ToL1MessagePasser } from "../predeploys/iOVM_L2ToL1MessagePasser.sol";

87:         messageNonce += 1;

```

```solidity
File: contracts/L2/messaging/L2StandardBridge.sol

5: import { IL1StandardBridge } from "../../L1/messaging/IL1StandardBridge.sol";

5: import { IL1StandardBridge } from "../../L1/messaging/IL1StandardBridge.sol";

5: import { IL1StandardBridge } from "../../L1/messaging/IL1StandardBridge.sol";

5: import { IL1StandardBridge } from "../../L1/messaging/IL1StandardBridge.sol";

6: import { IL1ERC20Bridge } from "../../L1/messaging/IL1ERC20Bridge.sol";

6: import { IL1ERC20Bridge } from "../../L1/messaging/IL1ERC20Bridge.sol";

6: import { IL1ERC20Bridge } from "../../L1/messaging/IL1ERC20Bridge.sol";

6: import { IL1ERC20Bridge } from "../../L1/messaging/IL1ERC20Bridge.sol";

7: import { IL2ERC20Bridge } from "./IL2ERC20Bridge.sol";

10: import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

10: import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

10: import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

10: import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

11: import { CrossDomainEnabled } from "../../libraries/bridge/CrossDomainEnabled.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

12: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

15: import { IL2StandardERC20 } from "../../standards/IL2StandardERC20.sol";

15: import { IL2StandardERC20 } from "../../standards/IL2StandardERC20.sol";

15: import { IL2StandardERC20 } from "../../standards/IL2StandardERC20.sol";

175:                 _to, // switched the _to and _from here to bounce back the deposit to the sender

175:                 _to, // switched the _to and _from here to bounce back the deposit to the sender

```

```solidity
File: contracts/L2/messaging/L2StandardTokenFactory.sol

5: import { L2StandardERC20 } from "../../standards/L2StandardERC20.sol";

5: import { L2StandardERC20 } from "../../standards/L2StandardERC20.sol";

5: import { L2StandardERC20 } from "../../standards/L2StandardERC20.sol";

6: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

6: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

6: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

6: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

```

```solidity
File: contracts/L2/predeploys/OVM_ETH.sol

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

8: import { L2StandardERC20 } from "../../standards/L2StandardERC20.sol";

8: import { L2StandardERC20 } from "../../standards/L2StandardERC20.sol";

8: import { L2StandardERC20 } from "../../standards/L2StandardERC20.sol";

```

```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

5: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

5: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

5: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

119:         uint256 l1Fee = l1GasUsed * l1BaseFee;

120:         uint256 divisor = 10**decimals;

120:         uint256 divisor = 10**decimals;

121:         uint256 unscaled = l1Fee * scalar;

122:         uint256 scaled = unscaled / divisor;

152:         for (uint256 i = 0; i < _data.length; i++) {

152:         for (uint256 i = 0; i < _data.length; i++) {

154:                 total += 4;

156:                 total += 16;

159:         uint256 unsigned = total + overhead;

160:         return unsigned + (68 * 16);

160:         return unsigned + (68 * 16);

```

```solidity
File: contracts/L2/predeploys/OVM_L2ToL1MessagePasser.sol

5: import { iOVM_L2ToL1MessagePasser } from "./iOVM_L2ToL1MessagePasser.sol";

```

```solidity
File: contracts/L2/predeploys/OVM_SequencerFeeVault.sol

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

5: import { Lib_PredeployAddresses } from "../../libraries/constants/Lib_PredeployAddresses.sol";

8: import { L2StandardBridge } from "../messaging/L2StandardBridge.sol";

8: import { L2StandardBridge } from "../messaging/L2StandardBridge.sol";

```

```solidity
File: contracts/L2/predeploys/WETH9.sol

35:         balanceOf[msg.sender] += msg.value;

40:         balanceOf[msg.sender] -= wad;

65:         if (src != msg.sender && allowance[src][msg.sender] != uint(-1)) {

67:             allowance[src][msg.sender] -= wad;

70:         balanceOf[src] -= wad;

71:         balanceOf[dst] += wad;

84:  Copyright (C) 2007 Free Software Foundation, Inc. <http://fsf.org/>

84:  Copyright (C) 2007 Free Software Foundation, Inc. <http://fsf.org/>

84:  Copyright (C) 2007 Free Software Foundation, Inc. <http://fsf.org/>

96: share and change all versions of a program--to make sure it remains free

96: share and change all versions of a program--to make sure it remains free

122: giving you legal permission to copy, distribute and/or modify it.

143: software on general-purpose computers, but in those that do, we wish to

146: patents cannot be used to render the program non-free.

157:   "Copyright" also means copyright-like laws that apply to other kinds of

195: for making modifications to it.  "Object code" means any non-source

218: System Libraries, or general-purpose tools or generally available free

259:   3. Protecting Users' Legal Rights From Anti-Circumvention Law.

281: non-permissive terms added in accord with section 7 apply to the code;

325:   6. Conveying Non-Source Forms.

329: machine-readable Corresponding Source under the terms of this License,

368:     e) Convey the object code using peer-to-peer transmission, provided

368:     e) Convey the object code using peer-to-peer transmission, provided

387: commercial, industrial or non-consumer uses, unless such uses represent

468:   All other non-permissive additional terms are considered "further

483:   Additional terms, permissive or non-permissive, may be stated in the

519: occurring solely as a consequence of using peer-to-peer transmission

519: occurring solely as a consequence of using peer-to-peer transmission

547: (including a cross-claim or counterclaim in a lawsuit) alleging that

567:   Each contributor grants you a non-exclusive, worldwide, royalty-free

567:   Each contributor grants you a non-exclusive, worldwide, royalty-free

603: conditioned on the non-exercise of one or more of the rights that are

645:   The Free Software Foundation may publish revised and/or new versions of

673: HOLDERS AND/OR OTHER PARTIES PROVIDE THE PROGRAM "AS IS" WITHOUT WARRANTY

683: WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MODIFIES AND/OR CONVEYS

717:     This program is free software: you can redistribute it and/or modify

728:     along with this program.  If not, see <http://www.gnu.org/licenses/>.

728:     along with this program.  If not, see <http://www.gnu.org/licenses/>.

728:     along with this program.  If not, see <http://www.gnu.org/licenses/>.

728:     along with this program.  If not, see <http://www.gnu.org/licenses/>.

747: <http://www.gnu.org/licenses/>.

747: <http://www.gnu.org/licenses/>.

747: <http://www.gnu.org/licenses/>.

747: <http://www.gnu.org/licenses/>.

754: <http://www.gnu.org/philosophy/why-not-lgpl.html>.

754: <http://www.gnu.org/philosophy/why-not-lgpl.html>.

754: <http://www.gnu.org/philosophy/why-not-lgpl.html>.

754: <http://www.gnu.org/philosophy/why-not-lgpl.html>.

754: <http://www.gnu.org/philosophy/why-not-lgpl.html>.

754: <http://www.gnu.org/philosophy/why-not-lgpl.html>.

```

### <a name="GAS-4"></a>[GAS-4] Use Custom Errors
[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (23)*:
```solidity
File: contracts/L1/deployment/AddressDictator.sol

81:         require(msg.sender == finalOwner, "AddressDictator: only callable by finalOwner");

```

```solidity
File: contracts/L1/deployment/ChugSplashDictator.sol

54:         require(keccak256(_code) == codeHash, "ChugSplashDictator: Incorrect code hash.");

69:         require(msg.sender == finalOwner, "ChugSplashDictator: only callable by finalOwner");

```

```solidity
File: contracts/L1/messaging/L1StandardBridge.sol

52:         require(messenger == address(0), "Contract has already been initialized.");

66:         require(!Address.isContract(msg.sender), "Account not EOA");

226:         require(success, "TransferHelper::safeTransferETH: ETH transfer failed");

```

```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

88:         require(msg.sender == libAddressManager.owner(), "Only callable by the Burn Admin.");

227:         require(_gasLimit >= MIN_ROLLUP_TX_GAS, "Transaction gas limit too low to enqueue.");

247:             require(startingGas > gasToConsume, "Insufficient gas for L2 rate limiting burn.");

312:         require(msg.data.length >= nextTransactionPtr, "Not enough BatchContexts provided.");

```

```solidity
File: contracts/L1/rollup/StateCommitmentChain.sol

104:         require(_batch.length > 0, "Cannot submit an empty state batch.");

127:         require(_isValidBatchHeader(_batchHeader), "Invalid batch header.");

146:         require(_isValidBatchHeader(_batchHeader), "Invalid batch header.");

172:         require(timestamp != 0, "Batch header timestamp cannot be zero");

283:         require(_batchHeader.batchIndex < batches().length(), "Invalid batch index.");

285:         require(_isValidBatchHeader(_batchHeader), "Invalid batch header.");

```

```solidity
File: contracts/L2/messaging/L2StandardTokenFactory.sol

27:         require(_l1Token != address(0), "Must provide L1 token address");

```

```solidity
File: contracts/L2/predeploys/OVM_DeployerWhitelist.sol

36:         require(msg.sender == owner, "Function can only be called by the owner of this contract.");

```

```solidity
File: contracts/L2/predeploys/OVM_ETH.sol

28:         revert("OVM_ETH: transfer is disabled pending further community discussion.");

32:         revert("OVM_ETH: approve is disabled pending further community discussion.");

40:         revert("OVM_ETH: transferFrom is disabled pending further community discussion.");

49:         revert("OVM_ETH: increaseAllowance is disabled pending further community discussion.");

58:         revert("OVM_ETH: decreaseAllowance is disabled pending further community discussion.");

```

### <a name="GAS-5"></a>[GAS-5] Don't initialize variables with default value

*Instances (6)*:
```solidity
File: contracts/L1/deployment/AddressDictator.sol

52:         for (uint256 i = 0; i < _names.length; i++) {

67:         for (uint256 i = 0; i < namedAddresses.length; i++) {

```

```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

315:         uint32 numSequencerTransactions = 0;

323:         for (uint32 i = 0; i < numContexts; i++) {

```

```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

151:         uint256 total = 0;

152:         for (uint256 i = 0; i < _data.length; i++) {

```

### <a name="GAS-6"></a>[GAS-6] Long revert strings

*Instances (11)*:
```solidity
File: contracts/L1/deployment/AddressDictator.sol

81:         require(msg.sender == finalOwner, "AddressDictator: only callable by finalOwner");

```

```solidity
File: contracts/L1/deployment/ChugSplashDictator.sol

54:         require(keccak256(_code) == codeHash, "ChugSplashDictator: Incorrect code hash.");

69:         require(msg.sender == finalOwner, "ChugSplashDictator: only callable by finalOwner");

```

```solidity
File: contracts/L1/messaging/L1StandardBridge.sol

52:         require(messenger == address(0), "Contract has already been initialized.");

226:         require(success, "TransferHelper::safeTransferETH: ETH transfer failed");

```

```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

227:         require(_gasLimit >= MIN_ROLLUP_TX_GAS, "Transaction gas limit too low to enqueue.");

247:             require(startingGas > gasToConsume, "Insufficient gas for L2 rate limiting burn.");

312:         require(msg.data.length >= nextTransactionPtr, "Not enough BatchContexts provided.");

```

```solidity
File: contracts/L1/rollup/StateCommitmentChain.sol

104:         require(_batch.length > 0, "Cannot submit an empty state batch.");

172:         require(timestamp != 0, "Batch header timestamp cannot be zero");

```

```solidity
File: contracts/L2/predeploys/OVM_DeployerWhitelist.sol

36:         require(msg.sender == owner, "Function can only be called by the owner of this contract.");

```

### <a name="GAS-7"></a>[GAS-7] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (15)*:
```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

99:     function pause() external onlyOwner {

107:     function blockMessage(bytes32 _xDomainCalldataHash) external onlyOwner {

116:     function allowMessage(bytes32 _xDomainCalldataHash) external onlyOwner {

```

```solidity
File: contracts/L1/rollup/ChainStorageContainer.sol

71:     function setGlobalMetadata(bytes27 _globalMetadata) public onlyOwner {

95:     function push(bytes32 _object) public onlyOwner {

103:     function push(bytes32 _object, bytes27 _globalMetadata) public onlyOwner {

119:     function deleteElementsAfterInclusive(uint256 _index) public onlyOwner {

```

```solidity
File: contracts/L2/predeploys/OVM_DeployerWhitelist.sol

49:     function setWhitelistedDeployer(address _deployer, bool _isWhitelisted) external onlyOwner {

59:     function setOwner(address _owner) public onlyOwner {

75:     function enableArbitraryContractDeployment() external onlyOwner {

```

```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

64:     function setGasPrice(uint256 _gasPrice) public onlyOwner {

74:     function setL1BaseFee(uint256 _baseFee) public onlyOwner {

84:     function setOverhead(uint256 _overhead) public onlyOwner {

94:     function setScalar(uint256 _scalar) public onlyOwner {

104:     function setDecimals(uint256 _decimals) public onlyOwner {

```

### <a name="GAS-8"></a>[GAS-8] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
*Saves 5 gas per loop*

*Instances (5)*:
```solidity
File: contracts/L1/deployment/AddressDictator.sol

52:         for (uint256 i = 0; i < _names.length; i++) {

67:         for (uint256 i = 0; i < namedAddresses.length; i++) {

```

```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

251:                 i++;

323:         for (uint32 i = 0; i < numContexts; i++) {

```

```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

152:         for (uint256 i = 0; i < _data.length; i++) {

```

### <a name="GAS-9"></a>[GAS-9] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (3)*:
```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

28:     uint256 public constant MIN_ROLLUP_TX_GAS = 100000;

29:     uint256 public constant MAX_ROLLUP_TX_SIZE = 50000;

```

```solidity
File: contracts/L2/predeploys/OVM_SequencerFeeVault.sol

21:     uint256 public constant MIN_WITHDRAWAL_AMOUNT = 15 ether;

```

### <a name="GAS-10"></a>[GAS-10] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (12)*:
```solidity
File: contracts/L1/messaging/IL1ERC20Bridge.sol

2: pragma solidity >0.5.0 <0.9.0;

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/messaging/IL1StandardBridge.sol

2: pragma solidity >0.5.0 <0.9.0;

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/ICanonicalTransactionChain.sol

2: pragma solidity >0.5.0 <0.9.0;

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/IChainStorageContainer.sol

2: pragma solidity >0.5.0 <0.9.0;

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/IStateCommitmentChain.sol

2: pragma solidity >0.5.0 <0.9.0;

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/StateCommitmentChain.sol

104:         require(_batch.length > 0, "Cannot submit an empty state batch.");

```

```solidity
File: contracts/L2/predeploys/WETH9.sol

16: pragma solidity >=0.4.22 <0.6;

```
