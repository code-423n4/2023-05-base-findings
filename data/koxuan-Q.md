# Report


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Constants should be defined rather than using magic numbers | 16 |
| [NC-2](#NC-2) | Constants in comparisons should appear on the left side | 2 |
### <a name="NC-1"></a>[NC-1] Constants should be defined rather than using magic numbers

*Instances (16)*:
```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

293:             shouldStartAtElement := shr(216, calldataload(4))

294:             totalElementsToAppend := shr(232, calldataload(9))

295:             numContexts := shr(232, calldataload(12))

402:             numSequencedTransactions := shr(232, calldataload(contextPtr))

403:             numSubsequentQueueTransactions := shr(232, calldataload(add(contextPtr, 3)))

404:             ctxTimestamp := shr(216, calldataload(add(contextPtr, 6)))

405:             ctxBlockNumber := shr(216, calldataload(add(contextPtr, 11)))

443:             extraData := shr(40, extraData)

483:             extraData := or(extraData, shl(40, _nextQueueIdx))

484:             extraData := or(extraData, shl(80, _timestamp))

485:             extraData := or(extraData, shl(120, _blockNumber))

486:             extraData := shl(40, extraData)

```

```solidity
File: contracts/L1/rollup/StateCommitmentChain.sol

192:             extraData := shr(40, extraData)

221:             extraData := or(extraData, shl(40, _lastSequencerTimestamp))

222:             extraData := shl(40, extraData)

```

```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

160:         return unsigned + (68 * 16);

```

### [NC&#x2011;2] Constants in comparisons should appear on the left side

Constants should appear on the left side of comparisons, to avoid accidental assignment

*Instances (2)*:
```solidity
File: contracts/L1/rollup/CanonicalTransactionChain.sol

345:        if (curContext.numSubsequentQueueTransactions == 0) {

```


```solidity
File: contracts/L2/predeploys/OVM_GasPriceOracle.sol

153:            if (_data[i] == 0) {

```

## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) |  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 3 |
| [L-2](#L-2) | Empty Function Body - Consider commenting why | 6 |
| [L-3](#L-3) | Initializers could be front-run | 3 |
| [L-4](#L-4) | Unsafe ERC20 operation(s) | 1 |
| [L-5](#L-5) | Unspecific compiler version pragma | 6 |
### <a name="L-1"></a>[L-1]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*Instances (3)*:
```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

221:         bytes32 relayId = keccak256(abi.encodePacked(xDomainCalldata, msg.sender, block.number));

```

```solidity
File: contracts/L2/messaging/L2CrossDomainMessenger.sol

152:         bytes32 relayId = keccak256(abi.encodePacked(xDomainCalldata, msg.sender, block.number));

```

```solidity
File: contracts/L2/predeploys/OVM_L2ToL1MessagePasser.sol

33:         sentMessages[keccak256(abi.encodePacked(_message, msg.sender))] = true;

```

### <a name="L-2"></a>[L-2] Empty Function Body - Consider commenting why

*Instances (6)*:
```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

71:     constructor() Lib_AddressResolver(address(0)) {}

```

```solidity
File: contracts/L1/messaging/L1StandardBridge.sol

40:     constructor() CrossDomainEnabled(address(0)) {}

263:     function donateETH() external payable {}

```

```solidity
File: contracts/L1/verification/BondManager.sol

20:     constructor(address _libAddressManager) Lib_AddressResolver(_libAddressManager) {}

```

```solidity
File: contracts/L2/predeploys/OVM_ETH.sol

22:     {}

```

```solidity
File: contracts/L2/predeploys/OVM_SequencerFeeVault.sol

48:     receive() external payable {}

```

### <a name="L-3"></a>[L-3] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (3)*:
```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

81:     function initialize(address _libAddressManager) public initializer {

81:     function initialize(address _libAddressManager) public initializer {

```

```solidity
File: contracts/L1/messaging/L1StandardBridge.sol

51:     function initialize(address _l1messenger, address _l2TokenBridge) public {

```

### <a name="L-4"></a>[L-4] Unsafe ERC20 operation(s)

*Instances (1)*:
```solidity
File: contracts/L2/predeploys/WETH9.sol

41:         msg.sender.transfer(wad);

```

### <a name="L-5"></a>[L-5] Unspecific compiler version pragma

*Instances (6)*:
```solidity
File: contracts/L1/messaging/IL1ERC20Bridge.sol

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/messaging/IL1StandardBridge.sol

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/ICanonicalTransactionChain.sol

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/IChainStorageContainer.sol

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L1/rollup/IStateCommitmentChain.sol

2: pragma solidity >0.5.0 <0.9.0;

```

```solidity
File: contracts/L2/predeploys/WETH9.sol

16: pragma solidity >=0.4.22 <0.6;

```


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Centralization Risk for trusted owners | 19 |
### <a name="M-1"></a>[M-1] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (19)*:
```solidity
File: contracts/L1/messaging/L1CrossDomainMessenger.sol

90:         __Context_init_unchained(); // Context is a dependency for both Ownable and Pausable

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

5: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

18: contract OVM_GasPriceOracle is Ownable {

41:     constructor(address _owner) Ownable() {

64:     function setGasPrice(uint256 _gasPrice) public onlyOwner {

74:     function setL1BaseFee(uint256 _baseFee) public onlyOwner {

84:     function setOverhead(uint256 _overhead) public onlyOwner {

94:     function setScalar(uint256 _scalar) public onlyOwner {

104:     function setDecimals(uint256 _decimals) public onlyOwner {

```

