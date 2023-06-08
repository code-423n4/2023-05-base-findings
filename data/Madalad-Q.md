# Summary

## Low Risk
| |Issue|Instances|
|:-:|:-|:-:|
|[[L-01]](#l-01-missing-address0-checks-when-assigning-to-address-state-variables)|Missing `address(0)` checks when assigning to `address` state variables|6|
|[[L-02]](#l-02-division-before-multiplications-leads-to-precision-loss)|Division before multiplications leads to precision loss|1|
|[[L-03]](#l-03-initialize-can-be-frontrun)|`initialize` can be frontrun|5|
|[[L-04]](#l-04-potential-division-by-zero)|Potential division by zero|4|
|[[L-05]](#l-05-possible-loss-of-precision)|Possible loss of precision|4|
|[[L-06]](#l-06-setter-functions-lacking-input-sanitization)|Setter functions lacking input sanitization|1|
|[[L-07]](#l-07-solidity-version-0820-may-not-work-on-other-chains-due-to-push0)|Solidity version 0.8.20 may not work on other chains due to `PUSH0`|3|
|[[L-08]](#l-08-use-two-step-ownership-transfers)|Use two-step ownership transfers|1|
|[[L-09]](#l-09-upgradeable-contracts-should-have-a-storage-gap-__gapn)|Upgradeable contracts should have a storage gap `__gap[n]`|1|
|[[L-10]](#l-10-missing-address0-checks-in-constructorinitialize)|Missing `address(0)` checks in constructor/initialize|13|

Total issues: 10

Total instances: 39

&nbsp;
## Non-Critical
| |Issue|Instances|
|:-:|:-|:-:|
|[[N-01]](#n-01-comparisons-should-place-constants-on-the-left-hand-side)|Comparisons should place constants on the left hand side|4|
|[[N-02]](#n-02-duplicated-requirerevert-checks-should-be-refactored-to-a-modifier)|Duplicated `require`/`revert` checks should be refactored to a modifier|1|
|[[N-03]](#n-03-event-missing-msgsender-parameter)|Event missing `msg.sender` parameter|2|
|[[N-04]](#n-04-use-indexed-for-event-parameters)|Use `indexed` for event parameters|7|
|[[N-05]](#n-05-function-names-should-use-mixedcase)|Function names should use mixedCase|1|
|[[N-06]](#n-06-function-order-doesnt-follow-solidity-style-guide)|Function order doesn't follow Solidity style guide|3|
|[[N-07]](#n-07-remove-internal-functions-that-are-not-called)|Remove `internal` functions that are not called|9|
|[[N-08]](#n-08-use-constants-rather-than-magic-numbers)|Use constants rather than magic numbers|6|
|[[N-09]](#n-09-use-named-parameters-for-mappings)|Use named parameters for mappings|4|
|[[N-10]](#n-10-natspec-param-missing)|NatSpec `@param` missing|2|
|[[N-11]](#n-11-natspec-return-missing)|NatSpec `@return` missing|1|
|[[N-12]](#n-12-non-external-variable-names-should-begin-with-an-underscore)|Non-external variable names should begin with an underscore|4|
|[[N-13]](#n-13-public-functions-not-called-internally-should-be-declared-external)|`public` functions not called internally should be declared `external`|5|
|[[N-14]](#n-14-use-a-more-recent-version-of-solidity)|Use a more recent version of Solidity|3|
|[[N-15]](#n-15-implementing-renounceownership-is-dangerous)|Implementing `renounceOwnership` is dangerous|1|
|[[N-16]](#n-16-use-single-file-for-all-system-wide-constants)|Use single file for all system-wide constants|8|
|[[N-17]](#n-17-large-numeric-literals-should-use-underscores)|Large numeric literals should use underscores|1|
|[[N-18]](#n-18-use-delete-rather-than-assigning-to-0)|Use `delete` rather than assigning to `0`|1|
|[[N-19]](#n-19-non-constant-state-variables-should-be-named-using-mixedcase)|Non `constant` state variables should be named using mixedCase|12|
|[[N-20]](#n-20-upper-case-variable-names-should-be-reserved-for-constant-variables)|Upper case variable names should be reserved for `constant` variables|9|

Total issues: 20

Total instances: 84

&nbsp;

# Low Risk Issues

## [L-01] Missing `address(0)` checks when assigning to `address` state variables

<details><summary>Instances: 6</summary>

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

31:         PORTAL = _portal;

```
[L31](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L31)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

156:         L2_ORACLE = _l2Oracle;

157:         GUARDIAN = _guardian;

158:         SYSTEM_CONFIG = _config;

```
[L156](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L156), [L157](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L157), [L158](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L158)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

107:         PROPOSER = _proposer;

108:         CHALLENGER = _challenger;

```
[L107](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L107), [L108](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L108)

</details>

&nbsp;
## [L-02] Division before multiplications leads to precision loss

Since Solidity cannot deal with decimal values, make sure to use multiplication before division to avoid precision loss due to rounding errors.

<details><summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

105:             int256 baseFeeDelta = (int256(uint256(params.prevBaseFee)) * gasUsedDelta) /
106:                 (targetResourceLimit * int256(uint256(config.baseFeeMaxChangeDenominator)));

```
[L105](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L105)

</details>

&nbsp;
## [L-03] `initialize` can be frontrun

Lack of access control on `initialize` function means that it can be frontrun,
allowing a malicious user to steal ownership of the contract and necessitating
an expensive re-deployment.

<details><summary>Instances: 5</summary>

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

```
[L125](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L125)

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

38:     function initialize() public initializer {

```
[L38](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L38)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

165:     function initialize(bool _paused) public initializer {

```
[L165](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L165)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

120:     function initialize(uint256 _startingBlockNumber, uint256 _startingTimestamp)
121:         public
122:         initializer
123:     {

```
[L120](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L120)

```solidity
File: ../contracts/../contracts/L2/L2CrossDomainMessenger.sol

34:     function initialize() public initializer {

```
[L34](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L34)

</details>

&nbsp;
## [L-04] Potential division by zero

When dividing by a value that may be zero, add checks so that execution does
not unexpectedly revert.

<details><summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

289:         require(
290:             ((_config.maxResourceLimit / _config.elasticityMultiplier) *
291:                 _config.elasticityMultiplier) == _config.maxResourceLimit,
292:             "SystemConfig: precision loss with target resource limit"
293:         );

```
[L289](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L289)

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

97:         int256 targetResourceLimit = int256(uint256(config.maxResourceLimit)) /
98:             int256(uint256(config.elasticityMultiplier));

155:         uint256 gasCost = resourceCost / Math.max(block.basefee, 1 gwei);

```
[L97](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L97), [L155](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L155)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

48:         uint256 scaled = unscaled / divisor;

```
[L48](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L48)

</details>

&nbsp;
## [L-05] Possible loss of precision

Division by large numbers may result in precision loss due to rounding down,
or even the result being erroneously equal to zero. Consider adding checks on
the numerator to ensure precision loss is handled appropriately.

<details><summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

289:         require(
290:             ((_config.maxResourceLimit / _config.elasticityMultiplier) *
291:                 _config.elasticityMultiplier) == _config.maxResourceLimit,
292:             "SystemConfig: precision loss with target resource limit"
293:         );

```
[L289](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L289)

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

97:         int256 targetResourceLimit = int256(uint256(config.maxResourceLimit)) /
98:             int256(uint256(config.elasticityMultiplier));

155:         uint256 gasCost = resourceCost / Math.max(block.basefee, 1 gwei);

```
[L97](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L97), [L155](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L155)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

48:         uint256 scaled = unscaled / divisor;

```
[L48](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L48)

</details>

&nbsp;
## [L-06] Setter functions lacking input sanitization

Setter functions should have input sanitization. A malicious/compromised
owner, or one that erroneously does a "fat finger", can change key variables
to extreme values that result in DoS, or even loss of funds.

<details><summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

192:     function setBatcherHash(bytes32 _batcherHash) external onlyOwner {

```
[L192](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L192)

</details>

&nbsp;
## [L-07] Solidity version 0.8.20 may not work on other chains due to `PUSH0`

The compiler for Solidity 0.8.20 switches the default target EVM version to
[Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note),
which includes the new PUSH0 op code. This op code may not yet be implemented
on all L2s, so deployment on these chains will fail. To work around this
issue, use an earlier
[EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target)
[version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version).

<details><summary>Instances: 3</summary>

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable2.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable2.sol#L2)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable.sol#L2)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L2)

</details>

&nbsp;
## [L-08] Use two-step ownership transfers

The current ownership transfer process involves the current owner calling `transferOwnership()`. This function checks the new owner is not the zero address and proceeds to write the new owner's address into the owner's state variable.

If the nominated EOA account is not a valid account, it is entirely possible the owner may accidentally transfer ownership to an uncontrolled account, breaking all functions with the onlyOwner() modifier.

Consider implementing a two step process where the owner nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed.

This can be achieved using OpenZeppelin's `Ownable2Step` or `Ownable2StepUpgradeable`.

<details><summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

16: contract SystemConfig is OwnableUpgradeable, Semver {

```
[L16](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L16)

</details>

&nbsp;
## [L-09] Upgradeable contracts should have a storage gap `__gap[n]`

A storage gap is an empty fixed length array conventionally added at the end of
an upgradeable contracts storage, to reserve space for state variables to be
added in the future without compromising storage compatibility. See
[here](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)
for more information.

<details><summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

16: contract SystemConfig is OwnableUpgradeable, Semver {

```
[L16](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L16)

</details>

&nbsp;
## [L-10] Missing `address(0)` checks in constructor/initialize

Failing to check for invalid parameters on deployment may result in an erroneous input and require an expensive redeployment.

<details><summary>Instances: 13</summary>

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

125:     function initialize(
126:         address _owner,
127:         uint256 _overhead,
128:         uint256 _scalar,
129:         bytes32 _batcherHash,
130:         uint64 _gasLimit,
131:         address _unsafeBlockSigner,
132:         ResourceMetering.ResourceConfig memory _config
133:     ) public initializer {

```
[L93](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L93), [L125](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L125)

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

27:     constructor(OptimismPortal _portal)
28:         Semver(1, 4, 0)
29:         CrossDomainMessenger(Predeploys.L2_CROSS_DOMAIN_MESSENGER)
30:     {

```
[L27](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L27)

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

28:     constructor(address _messenger, address _otherBridge)
29:         Semver(1, 1, 1)
30:         ERC721Bridge(_messenger, _otherBridge)
31:     {}

```
[L28](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L28)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

150:     constructor(
151:         L2OutputOracle _l2Oracle,
152:         address _guardian,
153:         bool _paused,
154:         SystemConfig _config
155:     ) Semver(1, 6, 0) {

```
[L150](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L150)

```solidity
File: ../contracts/../contracts/L1/L1StandardBridge.sol

98:     constructor(address payable _messenger)
99:         Semver(1, 1, 0)
100:         StandardBridge(_messenger, payable(Predeploys.L2_STANDARD_BRIDGE))
101:     {}

```
[L98](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1StandardBridge.sol#L98)

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
[L90](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L90)

```solidity
File: ../contracts/../contracts/L2/L2CrossDomainMessenger.sol

24:     constructor(address _l1CrossDomainMessenger)
25:         Semver(1, 4, 0)
26:         CrossDomainMessenger(_l1CrossDomainMessenger)
27:     {

```
[L24](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L24)

```solidity
File: ../contracts/../contracts/L2/SequencerFeeVault.sol

20:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/SequencerFeeVault.sol#L20)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

28:     constructor(address _messenger, address _otherBridge)
29:         Semver(1, 1, 0)
30:         ERC721Bridge(_messenger, _otherBridge)
31:     {}

```
[L28](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L28)

```solidity
File: ../contracts/../contracts/L2/L2StandardBridge.sol

66:     constructor(address payable _otherBridge)
67:         Semver(1, 1, 0)
68:         StandardBridge(payable(Predeploys.L2_CROSS_DOMAIN_MESSENGER), _otherBridge)
69:     {}

```
[L66](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2StandardBridge.sol#L66)

```solidity
File: ../contracts/../contracts/L2/L1FeeVault.sol

19:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L1FeeVault.sol#L19)

```solidity
File: ../contracts/../contracts/L2/BaseFeeVault.sol

19:     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/BaseFeeVault.sol#L19)

</details>

&nbsp;

# Non-Critical Issues
## [N-01] Comparisons should place constants on the left hand side

This practise avoids
[typo errors](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html).

<details>
<summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

277:             provenWithdrawal.timestamp == 0 ||

345:             provenWithdrawal.timestamp != 0,

461:         require(_data.length <= 120_000, "OptimismPortal: data too large");

```
[L277](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L277), [L345](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L345), [L461](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L461)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

326:             l2Outputs.length == 0

```
[L326](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L326)

</details>

&nbsp;
## [N-02] Duplicated `require`/`revert` checks should be refactored to a modifier
or function

The compiler will inline the function, which will avoid `JUMP` instructions
usually associated with functions.

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

142:         require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

219:         require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

```
[L142](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L142), [L219](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L219)

</details>

&nbsp;
## [N-03] Event missing `msg.sender` parameter

When an action is triggered based on a user's action, not being able to
filter based on who triggered the action makes event processing a lot
more cumbersome. Including the `msg.sender` the events of these types of
action will make events much more useful to end users, especially when
`msg.sender` is not `tx.origin`.

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

166:         emit OutputsDeleted(prevNextL2OutputIndex, _l2OutputIndex);

220:         emit OutputProposed(_outputRoot, nextOutputIndex(), _l2BlockNumber, block.timestamp);

```
[L166](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L166), [L220](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L220)

</details>

&nbsp;
## [N-04] Use `indexed` for event parameters

Index event fields make the field more quickly accessible to 
[off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing)
that parse events. This is especially useful when it comes to filtering based
on an address. However, note that each index field costs extra gas during
emission, so it's not necessarily best to index the maximum allowed per event
(three fields).

Where applicable, each event should use three `indexed` fields if there are three
or more fields, and gas usage is not particularly of concern for the events in
question. If there are fewer than three applicable fields, all of the applicable
fields should be indexed.

<details>
<summary>Instances: 7</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

80:     event ConfigUpdate(uint256 indexed version, UpdateType indexed updateType, bytes data);

```
[L80](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L80)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

118:     event WithdrawalFinalized(bytes32 indexed withdrawalHash, bool success);

125:     event Paused(address account);

132:     event Unpaused(address account);

```
[L118](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L118), [L125](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L125), [L132](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L132)

```solidity
File: ../contracts/../contracts/L1/L1StandardBridge.sol

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
[L30](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1StandardBridge.sol#L30), [L46](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1StandardBridge.sol#L46)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

26:     event OwnershipTransferred(
27:         address indexed previousOwner,
28:         address indexed newOwner,
29:         bool isLocal
30:     );

```
[L26](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L26)

</details>

&nbsp;
## [N-05] Function names should use mixedCase

See the
[Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#local-and-state-variable-names)
for more info.

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

179:     function __ResourceMetering_init() internal onlyInitializing {

```
[L179](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L179)

</details>

&nbsp;
## [N-06] Function order doesn't follow Solidity style guide

The [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions)
states that functions should be laid out in the following order: `constructor`,
`receive`, `fallback`, `external`, `public`, `internal`, `private`. In the
following contracts, this is not the case.

<details>
<summary>Instances: 3</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

16: contract SystemConfig is OwnableUpgradeable, Semver {

```
[L16](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L16)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

23: contract OptimismPortal is Initializable, ResourceMetering, Semver {

```
[L23](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L23)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

15: contract L2OutputOracle is Initializable, Semver {

```
[L15](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L15)

</details>

&nbsp;
## [N-07] Remove `internal` functions that are not called

<details>
<summary>Instances: 9</summary>

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

45:     function _sendMessage(

57:     function _isOtherMessenger() internal view override returns (bool) {

64:     function _isUnsafeTarget(address _target) internal view override returns (bool) {

```
[L45](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L45), [L57](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L57), [L64](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L64)

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

77:     function _initiateBridgeERC721(

```
[L77](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L77)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

225:     function _resourceConfig()

```
[L225](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L225)

```solidity
File: ../contracts/../contracts/L2/L2CrossDomainMessenger.sol

51:     function _sendMessage(

65:     function _isOtherMessenger() internal view override returns (bool) {

72:     function _isUnsafeTarget(address _target) internal view override returns (bool) {

```
[L51](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L51), [L65](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L65), [L72](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L72)

```solidity
File: ../contracts/../contracts/L2/L2ERC721Bridge.sol

79:     function _initiateBridgeERC721(

```
[L79](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ERC721Bridge.sol#L79)

</details>

&nbsp;
## [N-08] Use constants rather than magic numbers

Improves code readability and reduces margin for error.

<details>
<summary>Instances: 6</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

197:         return _byteCount * 16 + 21000;

461:         require(_data.length <= 120_000, "OptimismPortal: data too large");

```
[L197](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L197), [L461](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L461)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

46:         uint256 divisor = 10**DECIMALS;

122:                 total += 4;

124:                 total += 16;

128:         return unsigned + (68 * 16);

```
[L46](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L46), [L122](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L122), [L124](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L124), [L128](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L128)

</details>

&nbsp;
## [N-09] Use named parameters for mappings

Consider using named parameters in mappings (e.g. `mapping(address account => uint256 balance)`) to improve readability. This feature is present since Solidity 0.8.18.

<details>
<summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/L1ERC721Bridge.sol

20:     mapping(address => mapping(address => mapping(uint256 => bool))) public deposits;

```
[L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1ERC721Bridge.sol#L20)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

72:     mapping(bytes32 => bool) public finalizedWithdrawals;

77:     mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;

```
[L72](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L72), [L77](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L77)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

32:     mapping(bytes32 => bool) public sentMessages;

```
[L32](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L32)

</details>

&nbsp;
## [N-10] NatSpec `@param` missing

<details>
<summary>Instances: 2</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

162:     /**
163:      * @notice Initializer.
164:      */
165:     function initialize(bool _paused) public initializer {

189:     /**
190:      * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
191:      *         linearly increases based on the size of the calldata. This is to prevent
192:      *         users from creating L2 resource usage without paying for it. This function
193:      *         can be used when interacting with the portal to ensure forwards compatibility.
194:      *
195:      */
196:     function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {

```
[L162](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L162), [L189](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L189)

</details>

&nbsp;
## [N-11] NatSpec `@return` missing

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

189:     /**
190:      * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
191:      *         linearly increases based on the size of the calldata. This is to prevent
192:      *         users from creating L2 resource usage without paying for it. This function
193:      *         can be used when interacting with the portal to ensure forwards compatibility.
194:      *
195:      */
196:     function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {

```
[L189](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L189)

</details>

&nbsp;
## [N-12] Non-external variable names should begin with an underscore

According to the
[Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables),
Non-external variable and function names should begin with an underscore.

<details>
<summary>Instances: 4</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

40:     uint256 internal constant DEPOSIT_VERSION = 0;

45:     uint64 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

```
[L40](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L40), [L45](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L45)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

22:     uint256 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

37:     uint240 internal msgNonce;

```
[L22](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L22), [L37](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L37)

</details>

&nbsp;
## [N-13] `public` functions not called internally should be declared `external`

Contracts
[are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding)
to override their parents' functions and change the visibility from external to
public.

<details>
<summary>Instances: 5</summary>

```solidity
File: ../contracts/../contracts/L2/L2CrossDomainMessenger.sol

44:     function l1CrossDomainMessenger() public view returns (address) {

```
[L44](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2CrossDomainMessenger.sol#L44)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

57:     function gasPrice() public view returns (uint256) {

66:     function baseFee() public view returns (uint256) {

103:     function decimals() public pure returns (uint256) {

```
[L57](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L57), [L66](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L66), [L103](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L103)

```solidity
File: ../contracts/../contracts/L2/SequencerFeeVault.sol

28:     function l1FeeWallet() public view returns (address) {

```
[L28](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/SequencerFeeVault.sol#L28)

</details>

&nbsp;
## [N-14] Use a more recent version of Solidity

Use a Solidity version of at least 0.8.2 to get simple compiler automatic inlining.

Use a Solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.

Use a Solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()`/`require()` strings.

Use a Solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

Use a Solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>).

Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions.

<details>
<summary>Instances: 3</summary>

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable2.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable2.sol#L2)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable.sol#L2)

```solidity
File: ../contracts/../contracts/L2/CrossDomainOwnable3.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/CrossDomainOwnable3.sol#L2)

</details>

&nbsp;
## [N-15] Implementing `renounceOwnership` is dangerous

Typically, the contract's owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The OpenZeppelin's Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design.

Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

It is recommended to either reimplement the function to disable it, or to clearly specify if it is part of the contract design.


<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

16: contract SystemConfig is OwnableUpgradeable, Semver {

```
[L16](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L16)

</details>

&nbsp;
## [N-16] Use single file for all system-wide constants

<details>
<summary>Instances: 8</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

36:     uint256 public constant VERSION = 0;

43:     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
[L36](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L36), [L43](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L43)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

40:     uint256 internal constant DEPOSIT_VERSION = 0;

45:     uint64 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

```
[L40](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L40), [L45](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L45)

```solidity
File: ../contracts/../contracts/L2/GasPriceOracle.sol

28:     uint256 public constant DECIMALS = 6;

```
[L28](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/GasPriceOracle.sol#L28)

```solidity
File: ../contracts/../contracts/L2/L1Block.sol

19:     address public constant DEPOSITOR_ACCOUNT = 0xDeaDDEaDDeAdDeAdDEAdDEaddeAddEAdDEAd0001;

```
[L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L1Block.sol#L19)

```solidity
File: ../contracts/../contracts/L2/L2ToL1MessagePasser.sol

22:     uint256 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

27:     uint16 public constant MESSAGE_VERSION = 1;

```
[L22](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L22), [L27](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L2/L2ToL1MessagePasser.sol#L27)

</details>

&nbsp;
## [N-17] Large numeric literals should use underscores

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

197:         return _byteCount * 16 + 21000;

```
[L197](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L197)

</details>

&nbsp;
## [N-18] Use `delete` rather than assigning to `0`

Using `delete` more closely aligns with the intention of the action, and draws more
attention towards the changing of state, which may lead to a more thorough audit of
its associated logic"

<details>
<summary>Instances: 1</summary>

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

136:             params.prevBoughtGas = 0;

```
[L136](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L136)

</details>

&nbsp;
## [N-19] Non `constant` state variables should be named using mixedCase

See the
[Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#local-and-state-variable-names)
for more info.

<details>
<summary>Instances: 12</summary>

```solidity
File: ../contracts/../contracts/L1/SystemConfig.sol

27:         BATCHER,
28:         GAS_CONFIG,
29:         GAS_LIMIT,
30:         UNSAFE_BLOCK_SIGNER
31:     }

71:     ResourceMetering.ResourceConfig internal _resourceConfig;

```
[L27](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L27), [L71](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/SystemConfig.sol#L71)

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

20:     OptimismPortal public immutable PORTAL;

```
[L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L20)

```solidity
File: ../contracts/../contracts/L1/ResourceMetering.sol

68:     uint256[48] private __gap;

```
[L68](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/ResourceMetering.sol#L68)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

50:     L2OutputOracle public immutable L2_ORACLE;

55:     SystemConfig public immutable SYSTEM_CONFIG;

60:     address public immutable GUARDIAN;

```
[L50](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L50), [L55](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L55), [L60](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L60)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

20:     uint256 public immutable SUBMISSION_INTERVAL;

25:     uint256 public immutable L2_BLOCK_TIME;

30:     address public immutable CHALLENGER;

35:     address public immutable PROPOSER;

40:     uint256 public immutable FINALIZATION_PERIOD_SECONDS;

```
[L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L20), [L25](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L25), [L30](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L30), [L35](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L35), [L40](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L40)

</details>

&nbsp;
## [N-20] Upper case variable names should be reserved for `constant` variables

See the
[Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#local-and-state-variable-names)
for more info.

<details>
<summary>Instances: 9</summary>

```solidity
File: ../contracts/../contracts/L1/L1CrossDomainMessenger.sol

20:     OptimismPortal public immutable PORTAL;

```
[L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L1CrossDomainMessenger.sol#L20)

```solidity
File: ../contracts/../contracts/L1/OptimismPortal.sol

50:     L2OutputOracle public immutable L2_ORACLE;

55:     SystemConfig public immutable SYSTEM_CONFIG;

60:     address public immutable GUARDIAN;

```
[L50](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L50), [L55](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L55), [L60](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/OptimismPortal.sol#L60)

```solidity
File: ../contracts/../contracts/L1/L2OutputOracle.sol

20:     uint256 public immutable SUBMISSION_INTERVAL;

25:     uint256 public immutable L2_BLOCK_TIME;

30:     address public immutable CHALLENGER;

35:     address public immutable PROPOSER;

40:     uint256 public immutable FINALIZATION_PERIOD_SECONDS;

```
[L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L20), [L25](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L25), [L30](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L30), [L35](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L35), [L40](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/main/../contracts/../contracts/L1/L2OutputOracle.sol#L40)

</details>

&nbsp;