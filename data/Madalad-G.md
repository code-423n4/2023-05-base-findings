# Summary

| |Issue|Instances|
|:-:|:-|:-:|
|[[G-01]](#g-01-functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable)|Functions guaranteed to revert when called by normal users can be marked `payable`|12|
|[[G-02]](#g-02-use-assembly-to-check-for-address0)|Use assembly to check for `address(0)`|5|
|[[G-03]](#g-03-use-assembly-to-calculate-hashes)|Use assembly to calculate hashes|1|
|[[G-04]](#g-04-using-assemblys-selfbalance-is-cheaper-than-addressthisbalance)|Using assembly's `selfbalance()` is cheaper than `address(this).balance`|1|
|[[G-05]](#g-05-using-bool-for-storage-incurs-overhead)|Using `bool` for storage incurs overhead|5|
|[[G-06]](#g-06-do-not-compare-boolean-expressions-to-boolean-literals)|Do not compare boolean expressions to boolean literals|4|
|[[G-07]](#g-07-cache-storage-variables-read-from-more-than-once)|Cache storage variables read from more than once|29|
|[[G-08]](#g-08-divisionmultiplication-by-2-should-use-bit-shifting)|Division/Multiplication by 2 should use bit shifting|1|
|[[G-09]](#g-09-initializing-variable-to-default-value-costs-unnecessary-gas)|Initializing variable to default value costs unnecessary gas|2|
|[[G-10]](#g-10-requirerevert-strings-longer-than-32-bytes-cost-extra-gas)|`require`/`revert` strings longer than 32 bytes cost extra gas|10|
|[[G-11]](#g-11-inline-internal-functions-that-are-only-called-once)|Inline `internal` functions that are only called once|2|
|[[G-12]](#g-12-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant)|Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`|2|
|[[G-13]](#g-13-using-storage-instead-of-memory-for-structsarrays-saves-gas)|Using `storage` instead of `memory` for structs/arrays saves gas|4|
|[[G-14]](#g-14-combine-multiple-mappings-with-the-same-key-type-where-appropriate)|Combine multiple mappings with the same key type where appropriate|1|
|[[G-15]](#g-15-use-unchecked-for-operations-that-cannot-overflowunderflow)|Use `unchecked` for operations that cannot overflow/underflow|1|
|[[G-16]](#g-16-pack-state-variables-into-fewer-storage-slots)|Pack state variables into fewer storage slots|2|
|[[G-17]](#g-17-use-payable-for-constructor)|Use `payable` for constructor|13|
|[[G-18]](#g-18-pre-compute-hashes)|Pre-compute hashes|1|
|[[G-19]](#g-19-use-private-rather-than-public-for-constants)|Use `private` rather than `public` for constants|5|
|[[G-20]](#g-20-use-solidity-version-0819-for-a-gas-boost)|Use Solidity version `0.8.19` for a gas boost|15|
|[[G-21]](#g-21-use--0-instead-of--0-for-uints)|Use `!= 0` instead of `> 0` for uints|2|
|[[G-22]](#g-22-usage-of-uint-smaller-than-32-bytes-256-bits-incurs-overhead)|Usage of `uint` smaller than 32 bytes (256 bits) incurs overhead|8|
|[[G-23]](#g-23-avoid-updating-storage-when-the-value-hasnt-changed)|Avoid updating storage when the value hasn't changed|22|
|[[G-24]](#g-24-use-custom-errors)|Use custom errors|13|
|[[G-25]](#g-25-i-costs-less-gas-than-i)|`++i` costs less gas than `i++`|1|

Total issues: 25

Total instances: 162

&nbsp;

# Gas Optimizations
## [G-01] Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost (2400 per instance).

<details>
<summary>Instances: 12</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

180:     function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {

192:     function setBatcherHash(bytes32 _batcherHash) external onlyOwner {

205:     function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {

218:     function setGasLimit(uint64 _gasLimit) external onlyOwner {

256:     function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {

```
[L180](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L180), [L192](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L192), [L205](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L205), [L218](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L218), [L256](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L256)

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

46:     function finalizeBridgeERC721(
47:         address _localToken,
48:         address _remoteToken,
49:         address _from,
50:         address _to,
51:         uint256 _tokenId,
52:         bytes calldata _extraData
53:     ) external onlyOtherBridge {

```
[L46](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L46)

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

179:     function __ResourceMetering_init() internal onlyInitializing {

```
[L179](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L179)

```solidity
File: ../contracts/../contracts/L1/L1StandardBridge.sol

119:     function depositETH(uint32 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {

157:     function depositERC20(
158:         address _l1Token,
159:         address _l2Token,
160:         uint256 _amount,
161:         uint32 _minGasLimit,
162:         bytes calldata _extraData
163:     ) external virtual onlyEOA {

```
[L119](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1StandardBridge.sol#L119), [L157](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1StandardBridge.sol#L157)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

46:     function finalizeBridgeERC721(
47:         address _localToken,
48:         address _remoteToken,
49:         address _from,
50:         address _to,
51:         uint256 _tokenId,
52:         bytes calldata _extraData
53:     ) external onlyOtherBridge {

```
[L46](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L46)

```solidity
File: ../contracts/../contracts/L2/L2StandardBridge.sol

96:     function withdraw(
97:         address _l2Token,
98:         uint256 _amount,
99:         uint32 _minGasLimit,
100:         bytes calldata _extraData
101:     ) external payable virtual onlyEOA {

```
[L96](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2StandardBridge.sol#L96)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

37:     function transferOwnership(address _owner, bool _isLocal) external onlyOwner {

```
[L37](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L37)

</details>

&nbsp;
## [G-02] Use assembly to check for `address(0)`

Saves 16000 deployment gas per instance and 6 runtime gas per instance.

```solidity
assembly {
  if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
  }
}
```

<details>
<summary>Instances: 5</summary>

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

86:         require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");

```
[L86](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L86)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

445:                 _to == address(0),

```
[L445](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L445)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

88:         require(_remoteToken != address(0), "L2ERC721Bridge: remote token cannot be address(0)");

```
[L88](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L88)

```solidity
File: ../contracts/../contracts/L2/L2StandardBridge.sol

151:         if (_l1Token == address(0) && _l2Token == Predeploys.LEGACY_ERC20_ETH) {

```
[L151](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2StandardBridge.sol#L151)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

38:         require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");

```
[L38](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L38)

</details>

&nbsp;
## [G-03] Use assembly to calculate hashes

Saves 5000 deployment gas per instance and 80 runtime gas per instance.

### Unoptimized
```solidity
function solidityHash(uint256 a, uint256 b) public view {
	//unoptimized
	keccak256(abi.encodePacked(a, b));
}
```

### Optimized
```solidity
function assemblyHash(uint256 a, uint256 b) public view {
	//optimized
	assembly {
		mstore(0x00, a)
		mstore(0x20, b)
		let hashedVal := keccak256(0x00, 0x40)
	}
}
```

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

286:         bytes32 storageKey = keccak256(

```
[L286](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L286)

</details>

&nbsp;
## [G-04] Using assembly's `selfbalance()` is cheaper than `address(this).balance`

Saves 159 gas per instance.

```solidity
assembly {
 	mstore(0x00, selfbalance())
 	return(0x00, 0x20)
}
```

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

86:         uint256 balance = address(this).balance;

```
[L86](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L86)

</details>

&nbsp;
## [G-05] Using `bool` for storage incurs overhead

Booleans are more expensive than `uint256` or any type that takes up a full
word because each write operation emits an extra `SLOAD` to first read the
slot's contents, replace the bits taken up by the boolean, and then write
back. This is the compiler's defense against contract upgrades and pointer
aliasing, and it cannot be disabled.

Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess
(100 gas) for the extra `SLOAD`, and to avoid Gsset (20000 gas) when
changing from false to true, after having been true in the past (see
[OpenZeppelin's `ReentrancyGuard`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)
for reference).

<details>
<summary>Instances: 5</summary>

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

20:     mapping(address => mapping(address => mapping(uint256 => bool))) public deposits;

```
[L20](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L20)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

72:     mapping(bytes32 => bool) public finalizedWithdrawals;

83:     bool public paused;

```
[L72](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L72), [L83](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L83)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

20:     bool public isLocal = true;

```
[L20](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L20)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

32:     mapping(bytes32 => bool) public sentMessages;

```
[L32](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L32)

</details>

&nbsp;
## [G-06] Do not compare boolean expressions to boolean literals

`<x> == true` <=> `<x>`, also `<x> == false` <=> `!<x>`

<details>
<summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

57:         require(
58:             deposits[_localToken][_remoteToken][_tokenId] == true,
59:             "L1ERC721Bridge: Token ID is not escrowed in the L1 Bridge"
60:         );

```
[L57](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L57)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

138:         require(paused == false, "OptimismPortal: paused");

387:         require(
388:             finalizedWithdrawals[withdrawalHash] == false,
389:             "OptimismPortal: withdrawal has already been finalized"
390:         );

417:         if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {

```
[L138](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L138), [L387](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L387), [L417](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L417)

</details>

&nbsp;
## [G-07] Cache storage variables read from more than once

If the variable is only accessed once, it's cheaper to use the state
variable directly that one time, and save the 3 gas the extra stack
assignment would spend.

<details>
<summary>Instances: 29</summary>

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

94:         uint256 blockDiff = block.number - params.prevBlockNum;

104:             int256 gasUsedDelta = int256(uint256(params.prevBoughtGas)) - targetResourceLimit;

105:             int256 baseFeeDelta = (int256(uint256(params.prevBaseFee)) * gasUsedDelta) /

106:                 (targetResourceLimit * int256(uint256(config.baseFeeMaxChangeDenominator)));

110:             int256 newBaseFee = Arithmetic.clamp({

111:                 _value: int256(uint256(params.prevBaseFee)) + baseFeeDelta,

112:                 _min: int256(uint256(config.minimumBaseFee)),

113:                 _max: int256(uint256(config.maximumBaseFee))

114:             });

142:         require(

143:             int256(uint256(params.prevBoughtGas)) <= int256(uint256(config.maxResourceLimit)),

144:             "ResourceMetering: cannot buy more gas than available gas limit"

145:         );

148:         uint256 resourceCost = uint256(_amount) * uint256(params.prevBaseFee);

```
[L94](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L94), [L104](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L104), [L105](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L105), [L106](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L106), [L110](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L110), [L111](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L111), [L112](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L112), [L113](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L113), [L114](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L114), [L142](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L142), [L143](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L143), [L144](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L144), [L145](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L145), [L148](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L148)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

148:         require(

149:             _l2OutputIndex < l2Outputs.length,

150:             "L2OutputOracle: cannot delete outputs after the latest output index"

151:         );

154:         require(

155:             block.timestamp - l2Outputs[_l2OutputIndex].timestamp < FINALIZATION_PERIOD_SECONDS,

156:             "L2OutputOracle: cannot delete outputs that have already been finalized"

157:         );

163:             sstore(l2Outputs.slot, _l2OutputIndex)

263:         require(

264:             l2Outputs.length > 0,

265:             "L2OutputOracle: cannot get output as no outputs have been proposed yet"

266:         );

270:         uint256 hi = l2Outputs.length;

273:             if (l2Outputs[mid].l2BlockNumber < _l2BlockNumber) {

```
[L148](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L148), [L149](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L149), [L150](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L150), [L151](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L151), [L154](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L154), [L155](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L155), [L156](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L156), [L157](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L157), [L163](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L163), [L263](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L263), [L264](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L264), [L265](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L265), [L266](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L266), [L270](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L270), [L273](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L273)

</details>

&nbsp;
## [G-08] Division/Multiplication by 2 should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas.

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

272:             uint256 mid = (lo + hi) / 2;

```
[L272](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L272)

</details>

&nbsp;
## [G-09] Initializing variable to default value costs unnecessary gas

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

269:         uint256 lo = 0;

```
[L269](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L269)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

118:         uint256 total = 0;

```
[L118](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/GasPriceOracle.sol#L118)

</details>

&nbsp;
## [G-10] `require`/`revert` strings longer than 32 bytes cost extra gas

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met. If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

<details>
<summary>Instances: 10</summary>

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

54:         require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");

86:         require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");

```
[L54](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L54), [L86](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L86)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

175:         require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can pause");

184:         require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can unpause");

418:             revert("OptimismPortal: withdrawal failed");

```
[L175](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L175), [L184](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L184), [L418](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L418)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

99:         require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");

```
[L99](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L99)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

54:         require(_localToken != address(this), "L2ERC721Bridge: local token cannot be self");

88:         require(_remoteToken != address(0), "L2ERC721Bridge: remote token cannot be address(0)");

```
[L54](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L54), [L88](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L88)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

38:         require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");

54:             require(owner() == msg.sender, "CrossDomainOwnable3: caller is not the owner");

```
[L38](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L38), [L54](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L54)

</details>

&nbsp;
## [G-11] Inline `internal` functions that are only called once

Saves 20-40 gas per instance. See https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/ for more details.

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

83:         _metered(_amount, initialGas);

96:         ResourceConfig memory config = _resourceConfig();

```
[L83](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L83), [L96](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L96)

</details>

&nbsp;
## [G-12] Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`

When left as `constant`, the value is re-calculated each time it is used instead of being converted to a constant at compile time. This costs an extra ~100 gas for each access.

Using `immutable` only incurs the gas costs for the computation at deploy time.

See [here](https://github.com/ethereum/solidity/issues/9232) for a detailed description of the issue.

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

43:     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

43:     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
[L43](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L43), [L43](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L43)

</details>

&nbsp;
## [G-13] Using `storage` instead of `memory` for structs/arrays saves gas


When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read.

Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

<details>
<summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

96:         ResourceConfig memory config = _resourceConfig();

```
[L96](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/ResourceMetering.sol#L96)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

268:         ProvenWithdrawal memory provenWithdrawal = provenWithdrawals[withdrawalHash];

339:         ProvenWithdrawal memory provenWithdrawal = provenWithdrawals[withdrawalHash];

368:         Types.OutputProposal memory proposal = L2_ORACLE.getL2Output(
369:             provenWithdrawal.l2OutputIndex
370:         );

```
[L268](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L268), [L339](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L339), [L368](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L368)

</details>

&nbsp;
## [G-14] Combine multiple mappings with the same key type where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and
sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads
and subsequent writes can also be cheaper when a function requires both
values and they both fit in the same storage slot. Finally, if both fields
are accessed in the same function, can save ~42 gas per access due to
[not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0)
(Gkeccak256 - 30 gas) and that calculation's associated stack operations.

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

72:     mapping(bytes32 => bool) public finalizedWithdrawals;

77:     mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;

```
[L72](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L72), [L77](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L77)

</details>

&nbsp;
## [G-15] Use `unchecked` for operations that cannot overflow/underflow

By bypassing Solidity's built in overflow/underflow checks using `unchecked`, we can save gas. This is especially beneficial for the index variable within for loops (saves 120 gas per iteration).

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

120:         for (uint256 i = 0; i < length; i++) {

```
[L120](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/GasPriceOracle.sol#L120)

</details>

&nbsp;
## [G-16] Pack state variables into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper.

For more information about variable packing, see [here](https://blog.techfund.jp/p/solidity-variable-packing-saving-deployment-costs/#:~:text=Solidity%20variable%20packing%20is%20a,bytes%20equal%20to%20256%20bits.).

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

23: contract OptimismPortal is Initializable, ResourceMetering, Semver {

```
[L23](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L23)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

15: contract L1Block is Semver {

```
[L15](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L15)

</details>

&nbsp;
## [G-17] Use `payable` for constructor

Payable functions cost less gas to execute, since the compiler does not have
to add extra checks to ensure that a payment wasn't provided. A constructor
can safely be marked as payable, since only the deployer would be able to
pass funds, and the project itself would not pass any funds.

<details>
<summary>Instances: 13</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

93:     constructor(
94:         address _owner,
95:         uint256 _overhead,
96:         uint256 _scalar,
97:         bytes32 _batcherHash,
98:         uint64 _gasLimit,
99:         address _unsafeBlockSigner,
100:         ResourceMetering.ResourceConfig memory _config
101:     ) Semver(1, 3, 0) {

```
[L93](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L93)

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

27:     constructor(OptimismPortal _portal)
28:         Semver(1, 4, 0)
29:         CrossDomainMessenger(Predeploys.L2_CROSS_DOMAIN_MESSENGER)
30:     {

```
[L27](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L27)

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

28:     constructor(address _messenger, address _otherBridge)
29:         Semver(1, 1, 1)
30:         ERC721Bridge(_messenger, _otherBridge)
31:     {}

```
[L28](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L28)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

150:     constructor(
151:         L2OutputOracle _l2Oracle,
152:         address _guardian,
153:         bool _paused,
154:         SystemConfig _config
155:     ) Semver(1, 6, 0) {

```
[L150](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L150)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

90:     constructor(
91:         uint256 _submissionInterval,
92:         uint256 _l2BlockTime,
93:         uint256 _startingBlockNumber,
94:         uint256 _startingTimestamp,
95:         address _proposer,
96:         address _challenger,
97:         uint256 _finalizationPeriodSeconds
98:     ) Semver(1, 3, 0) {

```
[L90](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L90)

```solidity
File: ../contracts/../contracts/L2/L2CrossDomainMessenger.sol

24:     constructor(address _l1CrossDomainMessenger)
25:         Semver(1, 4, 0)
26:         CrossDomainMessenger(_l1CrossDomainMessenger)
27:     {

```
[L24](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L24)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

33:     constructor() Semver(1, 0, 0) {}

```
[L33](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/GasPriceOracle.sol#L33)

```solidity
File: ../contracts/../contracts/L2/SequencerFeeVault.sol

20:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[L20](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/SequencerFeeVault.sol#L20)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

28:     constructor(address _messenger, address _otherBridge)
29:         Semver(1, 1, 0)
30:         ERC721Bridge(_messenger, _otherBridge)
31:     {}

```
[L28](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L28)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

65:     constructor() Semver(1, 0, 0) {}

```
[L65](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L65)

```solidity
File: ../contracts/../contracts/L2/L1FeeVault.sol

19:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[L19](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1FeeVault.sol#L19)

```solidity
File: ../contracts/../contracts/L2/BaseFeeVault.sol

19:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[L19](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/BaseFeeVault.sol#L19)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

70:     constructor() Semver(1, 0, 0) {}

```
[L70](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L70)

</details>

&nbsp;
## [G-18] Pre-compute hashes

When calculating a deterministic `keccak256` hash, compute the value beforehand
and hard code it instead of computing at runtime/compile time to save gas.

Calculating a `keccak256` hash costs 30 gas + 6 gas for each 256 bits of data
being hashed.

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

43:     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
[L43](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L43)

</details>

&nbsp;
## [G-19] Use `private` rather than `public` for constants

Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table. If needed to be viewed externally, the values can be read from the verified contract source code.

<details>
<summary>Instances: 5</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

36:     uint256 public constant VERSION = 0;

43:     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
[L36](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L36), [L43](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L43)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

28:     uint256 public constant DECIMALS = 6;

```
[L28](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/GasPriceOracle.sol#L28)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

19:     address public constant DEPOSITOR_ACCOUNT = 0xDeaDDEaDDeAdDeAdDEAdDEaddeAddEAdDEAd0001;

```
[L19](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L19)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

27:     uint16 public constant MESSAGE_VERSION = 1;

```
[L27](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L27)

</details>

&nbsp;
## [G-20] Use Solidity version `0.8.19` for a gas boost

Upgrade to the latest solidity version 0.8.19 to get additional gas savings.
See [here](https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/)
for reference.

<details>
<summary>Instances: 15</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L2)

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L2)

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L2)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L2)

```solidity
File: ../contracts/../contracts/L1/L1StandardBridge.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1StandardBridge.sol#L2)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L2)

```solidity
File: ../contracts/../contracts/L2/L2CrossDomainMessenger.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L2)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/GasPriceOracle.sol#L2)

```solidity
File: ../contracts/../contracts/L2/SequencerFeeVault.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/SequencerFeeVault.sol#L2)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L2)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L2)

```solidity
File: ../contracts/../contracts/L2/L2StandardBridge.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2StandardBridge.sol#L2)

```solidity
File: ../contracts/../contracts/L2/L1FeeVault.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1FeeVault.sol#L2)

```solidity
File: ../contracts/../contracts/L2/BaseFeeVault.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/BaseFeeVault.sol#L2)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

2: pragma solidity 0.8.15;

```
[L2](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L2)

</details>

&nbsp;
## [G-21] Use `!= 0` instead of `> 0` for uints

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

99:         require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");

101:             _submissionInterval > 0,

```
[L99](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L99), [L101](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L101)

</details>

&nbsp;
## [G-22] Usage of `uint` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Consider using a larger size then downcasting where needed.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

<details>
<summary>Instances: 8</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

64:     uint64 public gasLimit;

98:         uint64 _gasLimit,

```
[L64](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L64), [L98](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L98)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

45:     uint64 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

```
[L45](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L45)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

24:     uint64 public number;

29:     uint64 public timestamp;

44:     uint64 public sequenceNumber;

```
[L24](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L24), [L29](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L29), [L44](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L44)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

27:     uint16 public constant MESSAGE_VERSION = 1;

37:     uint240 internal msgNonce;

```
[L27](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L27), [L37](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L37)

</details>

&nbsp;
## [G-23] Avoid updating storage when the value hasn't changed

If the old value is equal to the new value, not re-storing the value will
avoid a Gsreset (2900 gas), potentially at the expense of a Gcoldsload
(2100 gas) or a Gwarmaccess (100 gas).

<details>
<summary>Instances: 22</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

125:     function initialize(
126:         address _owner,
127:         uint256 _overhead,
128:         uint256 _scalar,
129:         bytes32 _batcherHash,
130:         uint64 _gasLimit,
131:         address _unsafeBlockSigner,
132:         ResourceMetering.ResourceConfig memory _config
133:     ) public initializer {

136:         overhead = _overhead;
137:         scalar = _scalar;
138:         batcherHash = _batcherHash;
139:         gasLimit = _gasLimit;

192:     function setBatcherHash(bytes32 _batcherHash) external onlyOwner {
193:         batcherHash = _batcherHash;

205:     function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {
206:         overhead = _overhead;
207:         scalar = _scalar;

218:     function setGasLimit(uint64 _gasLimit) external onlyOwner {

220:         gasLimit = _gasLimit;

266:     function _setResourceConfig(ResourceMetering.ResourceConfig memory _config) internal {

295:         _resourceConfig = _config;

```
[L125](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L125), [L136](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L136), [L192](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L192), [L205](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L205), [L218](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L218), [L220](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L220), [L266](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L266), [L295](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L295)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

165:     function initialize(bool _paused) public initializer {

167:         paused = _paused;

325:     function finalizeWithdrawalTransaction(Types.WithdrawalTransaction memory _tx)
326:         external
327:         whenNotPaused
328:     {

396:         l2Sender = _tx.sender;

```
[L165](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L165), [L167](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L167), [L325](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L325), [L396](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L396)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

120:     function initialize(uint256 _startingBlockNumber, uint256 _startingTimestamp)
121:         public
122:         initializer
123:     {

129:         startingTimestamp = _startingTimestamp;
130:         startingBlockNumber = _startingBlockNumber;

```
[L120](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L120), [L129](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L129)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

79:     function setL1BlockValues(
80:         uint64 _number,
81:         uint64 _timestamp,
82:         uint256 _basefee,
83:         bytes32 _hash,
84:         uint64 _sequenceNumber,
85:         bytes32 _batcherHash,
86:         uint256 _l1FeeOverhead,
87:         uint256 _l1FeeScalar
88:     ) external {

94:         number = _number;
95:         timestamp = _timestamp;
96:         basefee = _basefee;
97:         hash = _hash;
98:         sequenceNumber = _sequenceNumber;
99:         batcherHash = _batcherHash;
100:         l1FeeOverhead = _l1FeeOverhead;
101:         l1FeeScalar = _l1FeeScalar;

```
[L79](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L79), [L94](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L1Block.sol#L94)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

37:     function transferOwnership(address _owner, bool _isLocal) external onlyOwner {

42:         isLocal = _isLocal;

```
[L37](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L37), [L42](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L42)

</details>

&nbsp;
## [G-24] Use custom errors

Use of custom errors reduces both deployment and runtime gas costs, and allows
passing of dynamic information.

<details>
<summary>Instances: 13</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

142:         require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

219:         require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

```
[L142](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L142), [L219](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/SystemConfig.sol#L219)

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

54:         require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");

86:         require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");

```
[L54](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L54), [L86](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L86)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

138:         require(paused == false, "OptimismPortal: paused");

175:         require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can pause");

184:         require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can unpause");

461:         require(_data.length <= 120_000, "OptimismPortal: data too large");

```
[L138](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L138), [L175](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L175), [L184](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L184), [L461](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/OptimismPortal.sol#L461)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

99:         require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");

```
[L99](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L1/L2OutputOracle.sol#L99)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

54:         require(_localToken != address(this), "L2ERC721Bridge: local token cannot be self");

88:         require(_remoteToken != address(0), "L2ERC721Bridge: remote token cannot be address(0)");

```
[L54](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L54), [L88](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L88)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

38:         require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");

54:             require(owner() == msg.sender, "CrossDomainOwnable3: caller is not the owner");

```
[L38](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L38), [L54](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L54)

</details>

&nbsp;
## [G-25] `++i` costs less gas than `i++`

True for `--i`/`i--` as well, and is especially important in for loops. Saves 5 gas per iteration.

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

120:         for (uint256 i = 0; i < length; i++) {

```
[L120](https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/blob/main/../contracts/../contracts/L2/GasPriceOracle.sol#L120)

</details>

&nbsp;
