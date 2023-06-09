
## Low Risk Issues
|       |Issue  |Instances|
|:-----:|:-----:|:-------:|
|L-01|Upgradeable contract is missing a ```__gap[50]``` storage variable to allow for new storage variables in later versions|1|
|L-02|Loss of precision|3|
|L-03|Setters should have initial value check|6|
|L-04|Use of floating pragma|3|

## Not Critical Issues
|       |Issue  |Instances|
|:-----:|:-----:|:-------:|
|NC-01|Expressions for constant values such as a call to ```keccak256()```, should use ```immutable``` rather than ```constant```|1|
|NC-02|Typos|6|
|NC-03|Imports could be organized more systematically|2|
|NC-04|Large numeric literals should use underscores for readability|1|
|NC-05|Constants in comparisons should appear on the left side|4|
|NC-06|```public``` functions not called by the contract should be declared ```external``` instead|5|
|NC-07|```constants``` should be defined rather than using magic numbers|6|
|NC-08|Consider using ```delete``` rather than assigning zero/false to clear values|1|
|NC-09|NatSpec ```@param``` is missing|3|
|NC-10|NatSpec ```@return``` argument is missing|1|
|NC-11|Empty Function Body. Considering commenting why.|11|
|NC-12|Function ordering does not follow the Solidity style guide|2|

# Low Risk Issues
## L-01
### Upgradeable contract is missing a ```__gap[50]``` storage variable to allow for new storage variables in later versions
#### Description
See [this link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.


```
File: SystemConfig.sol

  
  16 contract SystemConfig is OwnableUpgradeable, Semver {
  17     /**
  18      * @notice Enum representing different types of updates.
  19      *
  20      * @custom:value BATCHER              Represents an update to the batcher hash.
  21      * @custom:value GAS_CONFIG           Represents an update to txn fee config on L2.
  22      * @custom:value GAS_LIMIT            Represents an update to gas limit on L2.
  23      * @custom:value UNSAFE_BLOCK_SIGNER  Represents an update to the signer key for unsafe
  24      *                                    block distrubution.
  25      */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16-L25](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16-L25)
## L-02
### Loss of precision
#### Description
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator


```
File: GasPriceOracle.sol

  
  48         uint256 scaled = unscaled / divisor;

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48)

```
File: ResourceMetering.sol

  
  105             int256 baseFeeDelta = (int256(uint256(params.prevBaseFee)) * gasUsedDelta) /
  106                 (targetResourceLimit * int256(uint256(config.baseFeeMaxChangeDenominator)));

  
  155         uint256 gasCost = resourceCost / Math.max(block.basefee, 1 gwei);

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L105-L106](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L105-L106)
## L-03
### Setters should have initial value check
#### Description
Setters should have initial value check to prevent assigning wrong value to the variable. Assginment of wrong value can lead to unexpected behavior of the contract.


<details>
<summary>There are 6 instances of this issue:</summary>

```
File: L1Block.sol

  @audit: unchecked values _number, _timestamp, _basefee, _hash, _sequenceNumber, _batcherHash, _l1FeeOverhead, _l1FeeScalar
  79     function setL1BlockValues(
  80         uint64 _number,
  81         uint64 _timestamp,
  82         uint256 _basefee,
  83         bytes32 _hash,
  84         uint64 _sequenceNumber,
  85         bytes32 _batcherHash,
  86         uint256 _l1FeeOverhead,
  87         uint256 _l1FeeScalar
  88     ) external {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79-L88](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79-L88)

```
File: SystemConfig.sol

  @audit: unchecked values _unsafeBlockSigner
  180     function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {

  @audit: unchecked values _batcherHash
  192     function setBatcherHash(bytes32 _batcherHash) external onlyOwner {

  @audit: unchecked values _overhead, _scalar
  205     function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {

  @audit: unchecked values _gasLimit
  218     function setGasLimit(uint64 _gasLimit) external onlyOwner {

  @audit: unchecked values _config
  256     function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180)

            
</details>

## L-04
### Use of floating pragma
#### Description
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.[(https://swcregistry.io/docs/SWC-103)](https://swcregistry.io/docs/SWC-103)


```
File: CrossDomainOwnable.sol

  
  2 pragma solidity ^0.8.0;

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2)

```
File: CrossDomainOwnable2.sol

  
  2 pragma solidity ^0.8.0;

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2)

```
File: CrossDomainOwnable3.sol

  
  2 pragma solidity ^0.8.0;

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2)


# Not Critical Issues
## NC-01
### Expressions for constant values such as a call to ```keccak256()```, should use ```immutable``` rather than ```constant```
#### Description
While it **doesn't save any gas** because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between ```constant``` variables and ```immutable``` variables, and they should each be used in their appropriate contexts. ```constants``` should be used for literal values written into the code, and ```immutable``` variables should be used for expressions, or values calculated in, or passed into the constructor.


```
File: SystemConfig.sol

  
  43     bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43)
## NC-02
### Typos
#### Description



<details>
<summary>There are 6 instances of this issue:</summary>

```
File: L1StandardBridge.sol

  @audit: transfering/transferring
  8 /**
  9  * @custom:proxied
  10  * @title L1StandardBridge
  11  * @notice The L1StandardBridge is responsible for transfering ETH and ERC20 tokens between L1 and
  12  *         L2. In the case that an ERC20 token is native to L1, it will be escrowed within this
  13  *         contract. If the ERC20 token is native to L2, it will be burnt. Before Bedrock, ETH was
  14  *         stored within this contract. After Bedrock, ETH is instead stored inside the
  15  *         OptimismPortal contract.
  16  *         NOTE: this contract is not intended to support all variations of ERC20 tokens. Examples
  17  *         of some token types that may not be properly supported by this contract include, but are
  18  *         not limited to: tokens with transfer fees, rebasing tokens, and tokens with blocklists.
  19  */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L8-L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L8-L19)

```
File: L2StandardBridge.sol

  @audit: transfering/transferring
  9 /**
  10  * @custom:proxied
  11  * @custom:predeploy 0x4200000000000000000000000000000000000010
  12  * @title L2StandardBridge
  13  * @notice The L2StandardBridge is responsible for transfering ETH and ERC20 tokens between L1 and
  14  *         L2. In the case that an ERC20 token is native to L2, it will be escrowed within this
  15  *         contract. If the ERC20 token is native to L1, it will be burnt.
  16  *         NOTE: this contract is not intended to support all variations of ERC20 tokens. Examples
  17  *         of some token types that may not be properly supported by this contract include, but are
  18  *         not limited to: tokens with transfer fees, rebasing tokens, and tokens with blocklists.
  19  */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L9-L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L9-L19)

```
File: OptimismPortal.sol

  @audit: gossipped/gossiped
  460         // transactions are not gossipped over the p2p network.

  @audit: whcih/which
  24     /**
  25      * @notice Represents a proven withdrawal.
  26      *
  27      * @custom:field outputRoot    Root of the L2 output this was proven against.
  28      * @custom:field timestamp     Timestamp at whcih the withdrawal was proven.
  29      * @custom:field l2OutputIndex Index of the output this was proven against.
  30      */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L460](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L460)

```
File: SystemConfig.sol

  @audit: derviation/deviation
  10 /**
  11  * @title SystemConfig
  12  * @notice The SystemConfig contract is used to manage configuration of an Optimism network. All
  13  *         configuration is stored on L1 and picked up by L2 as part of the derviation of the L2
  14  *         chain.
  15  */

  @audit: distrubution/distribution
  17     /**
  18      * @notice Enum representing different types of updates.
  19      *
  20      * @custom:value BATCHER              Represents an update to the batcher hash.
  21      * @custom:value GAS_CONFIG           Represents an update to txn fee config on L2.
  22      * @custom:value GAS_LIMIT            Represents an update to gas limit on L2.
  23      * @custom:value UNSAFE_BLOCK_SIGNER  Represents an update to the signer key for unsafe
  24      *                                    block distrubution.
  25      */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L10-L15](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L10-L15)

            
</details>

## NC-03
### Imports could be organized more systematically
#### Description
The contract's interface should be imported first, followed by each of the interfaces it uses, followed by all other files. We report only interface reports whose should be imported first


```
File: L1ERC721Bridge.sol

  
  5 import { IERC721 } from "@openzeppelin/contracts/token/ERC721/IERC721.sol";

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L5](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L5)

```
File: L2ERC721Bridge.sol

  
  7 import { IOptimismMintableERC721 } from "../universal/IOptimismMintableERC721.sol";

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L7](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L7)
## NC-04
### Large numeric literals should use underscores for readability
#### Description



```
File: OptimismPortal.sol

  
  197         return _byteCount * 16 + 21000;

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197)
## NC-05
### Constants in comparisons should appear on the left side
#### Description
Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)


```
File: GasPriceOracle.sol

  
  121             if (_data[i] == 0) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L121](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L121)

```
File: L2OutputOracle.sol

  
  326             l2Outputs.length == 0

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L326](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L326)

```
File: OptimismPortal.sol

  
  277             provenWithdrawal.timestamp == 0 ||

  
  345             provenWithdrawal.timestamp != 0,

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L277](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L277)
## NC-06
### ```public``` functions not called by the contract should be declared ```external``` instead
#### Description
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from ```external``` to ```public```.


```
File: GasPriceOracle.sol

  
  57     function gasPrice() public view returns (uint256) {

  
  66     function baseFee() public view returns (uint256) {

  
  103     function decimals() public pure returns (uint256) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L57](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L57)

```
File: L2CrossDomainMessenger.sol

  
  44     function l1CrossDomainMessenger() public view returns (address) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L44](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L44)

```
File: SequencerFeeVault.sol

  
  28     function l1FeeWallet() public view returns (address) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L28](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L28)
## NC-07
### ```constants``` should be defined rather than using magic numbers
#### Description
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals


<details>
<summary>There are 6 instances of this issue:</summary>

```
File: GasPriceOracle.sol

  
  46         uint256 divisor = 10**DECIMALS;

  
  124                 total += 16;

  
  128         return unsigned + (68 * 16);

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L46](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L46)

```
File: OptimismPortal.sol

  
  197         return _byteCount * 16 + 21000;

  
  197         return _byteCount * 16 + 21000;

  
  461         require(_data.length <= 120_000, "OptimismPortal: data too large");

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197)

            
</details>

## NC-08
### Consider using ```delete``` rather than assigning zero/false to clear values
#### Description
The ```delete``` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic


```
File: ResourceMetering.sol

  
  136             params.prevBoughtGas = 0;

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L136](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L136)
## NC-09
### NatSpec ```@param``` is missing
#### Description



```
File: L2OutputOracle.sol

  @audit: _finalizationPeriodSeconds
  80     /**
  81      * @custom:semver 1.3.0
  82      *
  83      * @param _submissionInterval  Interval in blocks at which checkpoints must be submitted.
  84      * @param _l2BlockTime         The time per L2 block, in seconds.
  85      * @param _startingBlockNumber The number of the first L2 block.
  86      * @param _startingTimestamp   The timestamp of the first L2 block.
  87      * @param _proposer            The address of the proposer.
  88      * @param _challenger          The address of the challenger.
  89      */
  90     constructor(
  91         uint256 _submissionInterval,
  92         uint256 _l2BlockTime,
  93         uint256 _startingBlockNumber,
  94         uint256 _startingTimestamp,
  95         address _proposer,
  96         address _challenger,
  97         uint256 _finalizationPeriodSeconds
  98     ) Semver(1, 3, 0) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L80-L98](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L80-L98)

```
File: OptimismPortal.sol

  @audit: _paused
  162     /**
  163      * @notice Initializer.
  164      */
  165     function initialize(bool _paused) public initializer {

  @audit: _byteCount
  189     /**
  190      * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
  191      *         linearly increases based on the size of the calldata. This is to prevent
  192      *         users from creating L2 resource usage without paying for it. This function
  193      *         can be used when interacting with the portal to ensure forwards compatibility.
  194      *
  195      */
  196     function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L162-L165](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L162-L165)
## NC-10
### NatSpec ```@return``` argument is missing
#### Description



```
File: OptimismPortal.sol

  @audit: @audit Missing: '@return'
  189     /**
  190      * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
  191      *         linearly increases based on the size of the calldata. This is to prevent
  192      *         users from creating L2 resource usage without paying for it. This function
  193      *         can be used when interacting with the portal to ensure forwards compatibility.
  194      *
  195      */
  196     function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L189-L196](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L189-L196)
## NC-11
### Empty Function Body. Considering commenting why.
#### Description
There are functions without documentation


<details>
<summary>There are 11 instances of this issue:</summary>

```
File: BaseFeeVault.sol

  
  19     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/BaseFeeVault.sol#L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/BaseFeeVault.sol#L19)

```
File: GasPriceOracle.sol

  
  33     constructor() Semver(1, 0, 0) {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L33](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L33)

```
File: L1Block.sol

  
  65     constructor() Semver(1, 0, 0) {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L65](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L65)

```
File: L1ERC721Bridge.sol

  
  28     constructor(address _messenger, address _otherBridge)
  29         Semver(1, 1, 1)
  30         ERC721Bridge(_messenger, _otherBridge)
  31     {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L28-L31](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L28-L31)

```
File: L1FeeVault.sol

  
  19     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L19)

```
File: L1StandardBridge.sol

  
  98     constructor(address payable _messenger)
  99         Semver(1, 1, 0)
  100         StandardBridge(_messenger, payable(Predeploys.L2_STANDARD_BRIDGE))
  101     {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L98-L101](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L98-L101)

```
File: L2ERC721Bridge.sol

  
  28     constructor(address _messenger, address _otherBridge)
  29         Semver(1, 1, 0)
  30         ERC721Bridge(_messenger, _otherBridge)
  31     {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L28-L31](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L28-L31)

```
File: L2StandardBridge.sol

  
  66     constructor(address payable _otherBridge)
  67         Semver(1, 1, 0)
  68         StandardBridge(payable(Predeploys.L2_CROSS_DOMAIN_MESSENGER), _otherBridge)
  69     {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L66-L69](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L66-L69)

```
File: L2ToL1MessagePasser.sol

  
  70     constructor() Semver(1, 0, 0) {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L70](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L70)

```
File: OptimismPortal.sol

  
  215     function donateETH() external payable {
  216         // Intentionally empty.
  217     }

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L215-L217](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L215-L217)

```
File: SequencerFeeVault.sol

  
  20     constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L20](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L20)

            
</details>

## NC-12
### Function ordering does not follow the Solidity style guide
#### Description
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :```constructor()```, ```receive()```, ```fallback()```, ```external```, ```public```, ```internal```, ```private```, but the cases below do not follow this pattern


```
File: L2OutputOracle.sol

  @audit: 
     |Current order                   | Solidity Style Order           |
     |:------------------------------:|:------------------------------:|
     |constructor                     |constructor                     |
     |initialize() - public           |deleteL2Outputs() - external    |
     |deleteL2Outputs() - external    |proposeL2Output() - external    |
     |proposeL2Output() - external    |getL2Output() - external        |
     |getL2Output() - external        |getL2OutputAfter() - external   |
     |getL2OutputIndexAfter() - public|latestOutputIndex() - external  |
     |getL2OutputAfter() - external   |initialize() - public           |
     |latestOutputIndex() - external  |getL2OutputIndexAfter() - public|
     |nextOutputIndex() - public      |nextOutputIndex() - public      |
     |latestBlockNumber() - public    |latestBlockNumber() - public    |
     |nextBlockNumber() - public      |nextBlockNumber() - public      |
     |computeL2Timestamp() - public   |computeL2Timestamp() - public   |
  15 contract L2OutputOracle is Initializable, Semver {
  16     /**
  17      * @notice The interval in L2 blocks at which checkpoints must be submitted. Although this is
  18      *         immutable, it can safely be modified by upgrading the implementation contract.
  19      */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L15-L19](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L15-L19)

```
File: SystemConfig.sol

  @audit: 
     |Current order                     | Solidity Style Order             |
     |:--------------------------------:|:--------------------------------:|
     |constructor                       |constructor                       |
     |initialize() - public             |unsafeBlockSigner() - external    |
     |minimumGasLimit() - public        |setUnsafeBlockSigner() - external |
     |unsafeBlockSigner() - external    |setBatcherHash() - external       |
     |setUnsafeBlockSigner() - external |setGasConfig() - external         |
     |setBatcherHash() - external       |setGasLimit() - external          |
     |setGasConfig() - external         |resourceConfig() - external       |
     |setGasLimit() - external          |setResourceConfig() - external    |
     |_setUnsafeBlockSigner() - internal|initialize() - public             |
     |resourceConfig() - external       |minimumGasLimit() - public        |
     |setResourceConfig() - external    |_setUnsafeBlockSigner() - internal|
     |_setResourceConfig() - internal   |_setResourceConfig() - internal   |
  16 contract SystemConfig is OwnableUpgradeable, Semver {
  17     /**
  18      * @notice Enum representing different types of updates.
  19      *
  20      * @custom:value BATCHER              Represents an update to the batcher hash.
  21      * @custom:value GAS_CONFIG           Represents an update to txn fee config on L2.
  22      * @custom:value GAS_LIMIT            Represents an update to gas limit on L2.
  23      * @custom:value UNSAFE_BLOCK_SIGNER  Represents an update to the signer key for unsafe
  24      *                                    block distrubution.
  25      */

```
[https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16-L25](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16-L25)

