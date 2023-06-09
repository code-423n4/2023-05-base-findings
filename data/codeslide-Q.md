## <a id="summary"></a> Summary

### <a id="summary-low-risk-issues"></a> Low Risk Issues
There are 27 instances over 3 issues.
|      ID       | Description                              | Count |
| :-----------: | :--------------------------------------- | ----: |
| [L-01](#l-01) | Initializers could be front-run          |    23 |
| [L-02](#l-02) | Loss of precision                        |     1 |
| [L-03](#l-03) | Unsafe downcasting of `uint256`/`int256` |     3 |

### <a id="summary-non-critical-issues"></a> Non-Critical Issues
There are 49 instances over 10 issues.
|       ID        | Description                                                 | Count |
| :-------------: | :---------------------------------------------------------- | ----: |
| [NC-01](#nc-01) | Empty function body                                         |     6 |
| [NC-02](#nc-02) | Don't initialize variables with default value               |     3 |
| [NC-03](#nc-03) | Constants should be defined rather than using magic numbers |     5 |
| [NC-04](#nc-04) | Consider using named mappings                               |     3 |
| [NC-05](#nc-05) | Events are missing sender information                       |    12 |
| [NC-06](#nc-06) | Un-indexed event parameters                                 |     9 |
| [NC-07](#nc-07) | Refactor duplicated `require()`/`revert()` checks           |     2 |
| [NC-08](#nc-08) | Use of `assert()`                                           |     2 |
| [NC-09](#nc-09) | Boolean comparisons                                         |     6 |
| [NC-10](#nc-10) | Expressions for `constant` values                           |     1 |

### <a id="details-low-risk-issues"></a> Low Risk Issues
#### <a id="l-01"></a> \[L-01\] Initializers could be front-run
##### Description:
Initializers could be front-run, allowing an attacker to either set their own values or take ownership of the contract. This issue may require a re-deployment to correct.

##### Instances:
There are 23 instances of this issue.
<details><summary>View 23 Instances</summary>

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol

32:         initialize();

32:         initialize();

38:     function initialize() public initializer {

38:     function initialize() public initializer {

38:     function initialize() public initializer {

38:     function initialize() public initializer {

39:         __CrossDomainMessenger_init();

39:         __CrossDomainMessenger_init();
```
[L1/L1CrossDomainMessenger.sol - Line 32](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L32)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

111:         initialize(_startingBlockNumber, _startingTimestamp);

120:     function initialize(uint256 _startingBlockNumber, uint256 _startingTimestamp)

122:         initializer
```
[L1/L2OutputOracle.sol - Line 111](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L111)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

159:         initialize(_paused);

165:     function initialize(bool _paused) public initializer {

165:     function initialize(bool _paused) public initializer {

168:         __ResourceMetering_init();
```
[L1/OptimismPortal.sol - Line 159](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L159)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

102:         initialize({

125:     function initialize(

133:     ) public initializer {

134:         __Ownable_init();
```
[L1/SystemConfig.sol - Line 102](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L102)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol

28:         initialize();

34:     function initialize() public initializer {

34:     function initialize() public initializer {

35:         __CrossDomainMessenger_init();
```
[L2/L2CrossDomainMessenger.sol - Line 28](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L28)

</details>

#### <a id="l-02"></a> \[L-02\] Loss of precision
##### Description:
Because Solidity does not support fractions, division by large numbers may result in the quotient being zero.

##### Recommendation:
Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.

##### Instances:
There is 1 instance of this issue.
```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

48:         uint256 scaled = unscaled / divisor;
```
[L2/GasPriceOracle.sol - Line 48](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48)

#### <a id="l-03"></a> \[L-03\] Unsafe downcasting of `uint256`/`int256`
##### Description:
Downcasting from `uint256`/`int256` in Solidity does not revert on overflow. This can result in undesired exploitation or bugs since developers usually assume that overflows raise errors.

##### Recommendation:
Use [OpenZeppelin's SafeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) so that transactions revert when such an operation overflows. Using this library instead of the unchecked operations eliminates an entire class of bugs, so it is recommended to always use it.

##### Instances:
There are 3 instances of this issue.
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

225:                 timestamp: uint128(block.timestamp),
```
[L1/L2OutputOracle.sol - Line 225](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L225)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

312:             timestamp: uint128(block.timestamp),
313:             l2OutputIndex: uint128(_l2OutputIndex)
```
[L1/OptimismPortal.sol - Line 312](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L312)


### <a id="details-non-critical-issues"></a> Non-Critical Issues
#### <a id="nc-01"></a> \[NC-01\] Empty function body
##### Description:
If a function body is empty, consider commenting why it is empty. If applicable, consider emitting an event.

##### Instances:
There are 6 instances of this issue.
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol

101:     {}
```
[L1/L1StandardBridge.sol - Line 101](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L101)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

33:     constructor() Semver(1, 0, 0) {}
```
[L2/GasPriceOracle.sol - Line 33](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L33)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/L1Block.sol

65:     constructor() Semver(1, 0, 0) {}
```
[L2/L1Block.sol - Line 65](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L65)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol

69:     {}
```
[L2/L2StandardBridge.sol - Line 69](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L69)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

70:     constructor() Semver(1, 0, 0) {}
```
[L2/L2ToL1MessagePasser.sol - Line 70](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L70)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol

20:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}
```
[L2/SequencerFeeVault.sol - Line 20](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L20)

#### <a id="nc-02"></a> \[NC-02\] Don't initialize variables with default value
##### Description:
The default value for integer variables is zero, so initializing them to this value is unnecessary. The default value for Boolean variables is `false`, so initializing them to this value is unnecessary.

##### Instances:
There are 3 instances of this issue.
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

269:         uint256 lo = 0;
```
[L1/L2OutputOracle.sol - Line 269](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L269)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

118:         uint256 total = 0;

120:         for (uint256 i = 0; i < length; i++) {
```
[L2/GasPriceOracle.sol - Line 118](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L118)

#### <a id="nc-03"></a> \[NC-03\] Constants should be defined rather than using magic numbers
##### Description:
The term "magic number" refers to the anti-pattern of using numbers directly in source code. This has been referred to as breaking one of the oldest rules of programming. The use of unnamed magic numbers in code obscures the developer's intent in choosing that number, increases opportunities for subtle errors, and makes it more difficult for the program to be adapted and extended in the future.

##### Recommendation:
Replacing all significant magic numbers with named constants makes programs easier to read, understand, and maintain.

##### Instances:
There are 5 instances of this issue.
```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

128:         return unsigned + (68 * 16);

122:                 total += 4;

124:                 total += 16;
```
[L2/GasPriceOracle.sol - Line 128](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L128)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

197:         return _byteCount * 16 + 21000;

461:         require(_data.length <= 120_000, "OptimismPortal: data too large");
```
[L1/OptimismPortal.sol - Line 197](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197)

#### <a id="nc-04"></a> \[NC-04\] Consider using named mappings
##### Description:
To help clarify the purpose of a mapping and make maintenance easier, consider using named mappings. Named mappings were [introduced in Solidity 0.8.18](https://blog.soliditylang.org/2023/02/01/solidity-0.8.18-release-announcement/).

Example:
```solidity
// Before:
mapping(address => uint256) private mappingName;
// After:
mapping(address contractAddress => uint256 functionCount) private mappingName;
```
##### Instances:
There are 3 instances of this issue.
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

72:     mapping(bytes32 => bool) public finalizedWithdrawals;

77:     mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;
```
[L1/OptimismPortal.sol - Line 72](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

32:     mapping(bytes32 => bool) public sentMessages;
```
[L2/L2ToL1MessagePasser.sol - Line 32](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L32)

#### <a id="nc-05"></a> \[NC-05\] Events are missing sender information
##### Description:
When an event is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing more difficult. Including `msg.sender` as an event parameter for these types of event emissions will make the events more useful to end users, particularly when `msg.sender` is not the same as `tx.origin`.

##### Instances:
There are 12 instances of this issue.

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

166:         emit OutputsDeleted(prevNextL2OutputIndex, _l2OutputIndex);

220:         emit OutputProposed(_outputRoot, nextOutputIndex(), _l2BlockNumber, block.timestamp);

317:         emit WithdrawalProven(withdrawalHash, _tx.sender, _tx.target);

412:         emit WithdrawalFinalized(withdrawalHash, success);
```
[L1/L2OutputOracle.sol - Line 166](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L166)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

184:         emit ConfigUpdate(VERSION, UpdateType.UNSAFE_BLOCK_SIGNER, data);

196:         emit ConfigUpdate(VERSION, UpdateType.BATCHER, data);

223:         emit ConfigUpdate(VERSION, UpdateType.GAS_LIMIT, data);
```
[L1/SystemConfig.sol - Line 184](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L184)

```solidity
File: optimism/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

88:         emit WithdrawerBalanceBurnt(balance);
```
[L2/L2ToL1MessagePasser.sol - Line 88](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L88)
```solidity
File: optimism/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol

379:             emit FailedRelayedMessage(versionedHash);

399:             emit RelayedMessage(versionedHash);

402:             emit FailedRelayedMessage(versionedHash);
```
[universal/CrossDomainMessenger.sol - Line 379](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L379)
```solidity
File: optimism/packages/contracts-bedrock/contracts/universal/OptimismMintableERC20Factory.sol

102:         emit StandardL2TokenCreated(_remoteToken, localToken);
```
[universal/OptimismMintableERC20Factory - Line 102](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/OptimismMintableERC20Factory.sol#L102)

#### <a id="nc-06"></a> \[NC-06\] Un-indexed event parameters
##### Description:
Indexing an event parameter makes the parameter more quickly accessible to off-chain tools that parse events. Indexed parameters appear in the structure of topics, not the data portion of the EVM log. Parameters that do not have the `indexed` attribute are ABI-encoded into the data portion of the log.
##### Recommendation:
Since there is a limit of three `indexed` parameters per event, a best practice is:
* If there are <= 3 parameters, all of the parameters should have the `indexed` attribute.
* If there are > 3 parameters, add the `indexed` attribute to the three most important/useful parameters.
##### Instances:
There are 9 instances of this issue.

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol

30:     event ETHDepositInitiated(
31:         address indexed from,
32:         address indexed to,
33:         uint256 amount,
34:         bytes extraData
35:     );

46:     event ETHWithdrawalFinalized(
47:         address indexed from,
48:         address indexed to,
49:         uint256 amount,
50:         bytes extraData
51:     );
```
[L1/L1StandardBridge.sol - Line 30](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L30)
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

118:     event WithdrawalFinalized(bytes32 indexed withdrawalHash, bool success);

125:     event Paused(address account);

132:     event Unpaused(address account);
```
[L1/OptimismPortal.sol - Line 118](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L118)
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

80:     event ConfigUpdate(uint256 indexed version, UpdateType indexed updateType, bytes data);
```
[L1/SystemConfig.sol - Line 80](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L80)
```solidity
File: optimism/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol

211:     event SentMessage(
212:         address indexed target,
213:         address sender,
214:         bytes message,
215:         uint256 messageNonce,
216:         uint256 gasLimit
217:     );

226:     event SentMessageExtension1(address indexed sender, uint256 value);
```
[universal/CrossDomainMessenger.sol - Line 211](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L211)
```solidity
File: optimism/packages/contracts-bedrock/contracts/universal/OptimismMintableERC20Factory.sol

40:     event OptimismMintableERC20Created(
41:         address indexed localToken,
42:         address indexed remoteToken,
43:         address deployer
44:     );
```
[universal/OptimismMintableERC20Factory - Line 40](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/OptimismMintableERC20Factory.sol#L40)

#### <a id="nc-07"></a> \[NC-07\] Refactor duplicated `require()`/`revert()` checks
##### Description:
Duplicated `require()` and `revert()` checks should be refactored to a modifier or a function. The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions.

##### Instances:
There are 2 instances of this issue.

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

184:         require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can unpause");
```
[L1/OptimismPortal.sol - Line 184](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L184)
```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

219:         require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");
```
[L1/SystemConfig.sol - Line 219](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L219)

#### <a id="nc-08"></a> \[NC-08\] Use of `assert()`
##### Description:
Hitting an `assert()` creates an error of type `Panic(uint256)` and consumes the remainder of the transaction\'s available gas rather than returning it, as `require()`/`revert()` do. Per the [Solidity documentation](https://docs.soliditylang.org/en/latest/control-structures.html#panic-via-assert-and-error-via-require), "_Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input._".

##### Recommendation:
Use `require()` rather than `assert()`.

##### Instances:
There are 2 instances of this issue.

```solidity
File: optimism/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol

341:             assert(msg.value == _value);
342:             assert(!failedMessages[versionedHash]);
```
[universal/CrossDomainMessenger.sol - Line 341](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L341)

#### <a id="nc-09"></a> \[NC-09\] Boolean comparisons
##### Description:
In this code base, there are instances where Boolean variables are checked by comparing against the constant `false`, and there are instances where the implicit value of the Boolean variables are used. Consistency in coding conventions is key to readability and maintainability.

##### Recommendation:
Choose one method of Boolean comparisons and use it consistently throughout the code base. When checking the value of a Boolean variable, it is a best practice to omit using the constant `true` or `false` and simply use the implicit Boolean variable value.

##### Counter-Examples:
```solidity
File: optimism/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol

154:         require(!finalized, "SystemDictator: already finalized");
155:         require(!exited, "SystemDictator: already exited");
```

##### Instances:
There are 6 instances of this issue.

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

138:         require(paused == false, "OptimismPortal: paused");

388:             finalizedWithdrawals[withdrawalHash] == false,

417:         if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {
```
[L1/OptimismPortal.sol - Line 138](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L138)
```solidity
File: optimism/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol

322:                 successfulMessages[oldHash] == false,

356:             _isUnsafeTarget(_target) == false,

361:             successfulMessages[versionedHash] == false,
```
[universal/CrossDomainMessenger.sol - Line 322](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L322)

#### <a id="nc-10"></a> \[NC-10\] Expressions for constant values
##### Description:
It is a best practice to use `constant` for literal values and `immutable` for expressions or values calculated or passed into the constructor. Although the compiler will accept it, using an expression such as `keccak256()` to set the value of a `constant` is an anti-pattern that should be avoided.

##### Recommendation:
When using an expression like `keccak256()` to set a value, use `immutable` rather than `constant`.

##### Instances:
There is 1 instance of this issue.

```solidity
File: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

43:     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");
```
[L1/SystemConfig.sol - Line 43](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43)