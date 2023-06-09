## Low Issues

| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) |  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 |
| [L-2](#L-2) | Empty Function Body - Consider commenting why | 4 |
| [L-3](#L-3) | Initializers could be front-run | 13 |

### [L-1]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*Instances (1)*:
```solidity
File: example/SystemDictator.sol

361:                 keccak256(abi.encodePacked(reason)) ==

```

### [L-2] Empty Function Body - Consider commenting why

*Instances (4)*:
```solidity
File: example/L1Block.sol

64:     constructor() Semver(1, 0, 0) {}

```

```solidity
File: example/L1StandardBridge.sol

96:     {}

```

```solidity
File: example/L2ToL1MessagePasser.sol

64:     constructor() Semver(1, 0, 0) {}

```

```solidity
File: example/SequencerFeeVault.sol

16:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```

### [L-3] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (13)*:
```solidity
File: example/OptimismPortal.sol

147:         initialize(_paused);

153:     function initialize(bool _paused) public initializer {

153:     function initialize(bool _paused) public initializer {

156:         __ResourceMetering_init();

```

```solidity
File: example/SystemConfig.sol

96:         initialize({

119:     function initialize(

127:     ) public initializer {

128:         __Ownable_init();

```

```solidity
File: example/SystemDictator.sol

153:         initialize(

175:     function initialize(DeployConfig memory _config) public initializer {

175:     function initialize(DeployConfig memory _config) public initializer {

178:         __Ownable_init();

353:                 .initialize()

```

