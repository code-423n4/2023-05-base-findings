# LOW FINDINGS

##

## [L-1] Unsafe Down casting 

### Impact 
When downcasting block.number from uint256 to uint64, you should exercise caution as it can be potentially unsafe. The ``block.number`` returns a uint256 value representing the current block number, which is guaranteed to fit within 256 bits.

However, downcasting to uint64 may result in a loss of precision or ``potential truncation`` if the block number exceeds the range of uint64, which can lead to unexpected behavior or errors.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol

183: prevBlockNum: uint64(block.number)

```
### Recommended Mitigation Steps:
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256.


##

## [L-1] No maximum limit for _gasLimit 

### Impact

Only Minimum value only checked. There is no limit or maxGas values. There is possibility that deployer assign very high gas values than expected.

To ensure the contract operates predictably, avoids excessive gas consumption, and protects against potential attacks, it is generally recommended to set appropriate maximum gas limits for each function or transaction. The gas limit should be determined based on careful analysis of the contract's logic, potential gas consumption patterns, and consideration of security and cost-efficiency factors

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

139:  gasLimit = _gasLimit;
142:  require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L142

### Recommended Mitigation

- Consider to implement maximumGasLimit() check

- maximumGasLimit(),minimumGasLimit() values determined based on careful analysis. Both Values should be updated based on analysis by Owner  

##

## [L-2] Possible to front-run the initialize() function in certain scenarios

- Front-running refers to the act of observing pending transactions in the mempool and quickly submitting a transaction with a higher gas price to get ahead of the original transaction. In the case of the initialize() function, if the function is publicly accessible and can be called by anyone, it is susceptible to front-running.

- When a contract is deployed, the constructor or initializer function is typically called to set the initial state of the contract. However, since the deployment transaction is publicly visible in the mempool before it gets mined, other participants can observe the pending transaction and attempt to execute the initialize() function with their own values before the original deployment transaction is confirmed

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

125: function initialize(
        address _owner,
        uint256 _overhead,
        uint256 _scalar,
        bytes32 _batcherHash,
        uint64 _gasLimit,
        address _unsafeBlockSigner,
        ResourceMetering.ResourceConfig memory _config
    ) public initializer {
        __Ownable_init();
        transferOwnership(_owner);
        overhead = _overhead;
        scalar = _scalar;
        batcherHash = _batcherHash;
        gasLimit = _gasLimit;
        _setUnsafeBlockSigner(_unsafeBlockSigner);
        _setResourceConfig(_config);
        require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");
    }

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol

193: function initialize(DeployConfig memory _config) public initializer {
        config = _config;
        currentStep = 1;
        __Ownable_init();
        _transferOwnership(config.globalConfig.controller);
    }

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L120-L131

### Recommended Mitigation 

##

## [L-3] Use Ownable2StepUpgradeable rather than OwnableUpgradeable

### Impact

[Ownable2StepUpgradeable](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/access/Ownable2StepUpgradeable.sol#L47-L63) is an extension of the [OwnableUpgradeable](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/access/OwnableUpgradeable.sol) contract that adds an additional step to the ownership transfer process. The additional step requires the new owner to accept ownership before it is transferred. This helps prevent accidental transfers of ownership and provides an additional layer of security

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

4: import {
    OwnableUpgradeable
} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L4-L6

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/deployment
/SystemDictator.sol

4: import {
    OwnableUpgradeable
} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L4-L6

### Use Ownable2step rather than Ownable 

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/universal
/ProxyAdmin.sol

4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```
https://github.com/ethereum-optimism/optimism/blob/941ae589b88e55183c7796ef8ad632bf5796e3df/packages/contracts-bedrock/contracts/universal/ProxyAdmin.sol#LL4C1-L4C70

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6



##

## [L-4] No Storage Gap for SystemConfig and SystemDictator Contract 

[SystemConfig.sol#L16](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16)

[SystemDictator.sol#L28](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L28)

### Impact
For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

If no storage gap is added, when the upgradable contract introduces new variables, it may override the variables in the inheriting contract.

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts.
To create a storage gap, declare a fixed-size array in the base contract with an initial number of slots.
This can be an array of uint256 so that each element reserves a 32 byte slot. Use the naming convention __gap so that OpenZeppelin Upgrades will recognize the gap:

Classification for a similar problem:

https://code4rena.com/reports/2022-05-alchemix/#m-05-no-storage-gap-for-upgradeable-contract-might-lead-to-storage-slot-collision

Openzeppelin Storage Gaps notification:

```
Storage Gaps
This makes the storage layouts incompatible, as explained in Writing Upgradeable Contracts. 
The size of the __gap array is calculated so that the amount of storage used by a contract 
always adds up to the same number (in this case 50 storage slots).

```

### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract

```solidity
uint256[50] private __gap;
```

##

## [L-5] Missing Event for critical parameters initialize and change

### Impact

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

 it is recommended to emit an event for critical parameter changes. Missing events for critical parameters that change should be detected. This is because events allow capturing the changed parameters so that they can be tracked off-chain

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198


Recommendation
Add Event-Emit

##

## [L-6] Lack of Sanity/Threshold/Limit Checks

### Impact

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values.

Consider also adding an optional codehash check for immutable address at the constructor since the zero address check cannot guarantee a matching address has been inputted.

These checks are generally not implemented in the contracts

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180-L185

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L210

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218-L224

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79-L103

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L19

##

## [L-7] Token ownership is not checked before transfer 

The safeTransferFrom function in the ERC-721 standard does not perform an ownership check on the token being transferred. Instead, it assumes that the caller of the function has the rightful ownership of the token and allows them to initiate the transfer.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol

68: IERC721(_localToken).safeTransferFrom(address(this), _to, _tokenId);

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L68

##

## [L-8] Tokenid Not checked whether this tokenid exist 

##

##

## [L-9] L2OutputOracle.proposeL2Output() can receive funds

### Impact

``payable`` for no apparent reason, so the contract can receive funds and there are no apparent mechanism to retrieve them. If ``payable`` is really relevant and the contract is expected to be able to receive funds, consider documenting it, otherwise it's possible that funds could be locked (no immediately visible/apparent/documented ways to retrieve funds)

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

179: function proposeL2Output(
        bytes32 _outputRoot,
        uint256 _l2BlockNumber,
        bytes32 _l1BlockHash,
        uint256 _l1BlockNumber
    ) external payable {

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L179-L184

### Recommended Mitigation:

``payable`` is not necessary for the proposeL2Output function

##

## [L-10] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256

### IMPACT

By downcasting to uint128, the code is essentially truncating the higher-order bits of the original uint256 values, potentially resulting in loss of precision if the original values exceed the range of uint128. It's important to note that if the original uint256 values are larger than the maximum value representable by uint128, this downcasting may lead to unexpected behavior or data loss

### ``block.timestamp,_l2BlockNumber`` uint256 values down casted to uint128 

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

225: timestamp: uint128(block.timestamp),
226: l2BlockNumber: uint128(_l2BlockNumber)

```

### Recommended Mitigation Steps:
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256.

##

## [L-11] Can refactor the gasPrice(),baseFee() function into one since both returns the same values

### Impact

By refactoring the code in this way, you achieve the same outcome with a single function, eliminating redundancy and providing a simplified interface for accessing the shared value.

```diff
FILE: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

- 57: function gasPrice() public view returns (uint256) {
- 58:        return block.basefee;
- 59:    }

- 66: function baseFee() public view returns (uint256) {
- 67:        return block.basefee;
- 68:    }

Recommended Mitigation:

+ function getGasPriceOrBaseFee() public view returns (uint256) {
+        return block.basefee;
+    }

``` 

##

## [L-12] Inconsistence solidity versions 

If there are inconsistencies in the Solidity versions used in your codebase or contracts, it can lead to compilation errors and potential compatibility issues. Different Solidity versions may introduce changes, deprecate certain features, or modify syntax, resulting in code that may not compile or behave as expected

- Compilation Errors
- Syntax Differences
- Compatibility Issues
- Limited Access to New Features

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L2

### CrossDomainOwnable3.sol,CrossDomainOwnable.sol contracts using the lower version 0.8.0 but other contracts using 0.8.15

Recommended Mitigation:
Maintain same versions for all contracts 

##

## [L-13] Critical Address changes should use two step procedure 

The critical procedures should be two step process.
See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

180:function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {
        _setUnsafeBlockSigner(_unsafeBlockSigner);

        bytes memory data = abi.encode(_unsafeBlockSigner);
        emit ConfigUpdate(VERSION, UpdateType.UNSAFE_BLOCK_SIGNER, data);
    }

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL180C5-L185C6

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol

function transferOwnership(address _owner, bool _isLocal) external onlyOwner {
        require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");

        address oldOwner = owner();
        _transferOwnership(_owner);
        isLocal = _isLocal;

        emit OwnershipTransferred(oldOwner, _owner, _isLocal);
    }

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37-L45

##

## [L-14] onlyOwner single point of failure 

### Impact
The ``onlyOwner`` role has a single point of failure and ``onlyOwner`` can use critical a few functions.

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol

function transferOwnership(address _owner, bool _isLocal) external onlyOwner {
        require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");

        address oldOwner = owner();
        _transferOwnership(_owner);
        isLocal = _isLocal;

        emit OwnershipTransferred(oldOwner, _owner, _isLocal);
    }

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37-L45

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

180: function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {
192: function setBatcherHash(bytes32 _batcherHash) external onlyOwner {
205: function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {
218: function setGasLimit(uint64 _gasLimit) external onlyOwner {

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL218C5-L218C64


### Recommended Mitigation Steps
Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Also detail them in documentation and NatSpec comments.

##

## [L-15] getL1GasUsed() estimates based on a simple gas cost calculation for zero and non-zero bytes. This is not accurately reflect the actual gas cost on the Ethereum network 

Gas costs can vary based on the specific opcode operations executed, memory usage, storage interactions, and other factors

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol 

 function getL1GasUsed(bytes memory _data) public view returns (uint256) {
        uint256 total = 0;
        uint256 length = _data.length;
        for (uint256 i = 0; i < length; i++) {
            if (_data[i] == 0) {
                total += 4;
            } else {
                total += 16;
            }
        }
        uint256 unsigned = total + overhead();
        return unsigned + (68 * 16);
    }


```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L117-L130

### Recommended Mitigation:

it's recommended to use gas profiling tools, perform local testing, or simulate transactions in test environments to understand the gas 

##

## [L-16] Hard Coded Address for DEPOSITOR_ACCOUNT not appreciated 

### Impact
Hardcoding addresses can introduce security risks if the address is public and known to attackers. They can attempt to exploit vulnerabilities in your contract or target the hardcoded address directly

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L2/L1Block.sol

19:  address public constant DEPOSITOR_ACCOUNT = 0xDeaDDEaDDeAdDeAdDEAdDEaddeAddEAdDEAd0001;

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#LL19C4-L19C92

### Recommended Mitigation:
You can consider using configurable addresses or address registry patterns

##


[L-17] Use safeTransferOwnership instead of transferOwnership function


##

## [L-18] number,timestamp should be upgraded to uint256 prevent potential problems in the future

Impact:
``block.number`` represents the current block number, and ``timestamp`` represents the timestamp of the current block. Both values are expected to increase over time as the blockchain progresses. By using uint256, you can accommodate larger values and ensure that your code remains compatible with future blockchains that might have larger block numbers or timestamps.

Using uint256 provides a wider range of values and reduces the risk of encountering overflow issues if the block number or timestamp exceeds the maximum value that can be stored in uint64. It also aligns with the natural data type for Ethereum's native arithmetic operations, which are typically performed with uint256.

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L2/L1Block.sol

   /**
     * @notice The latest L1 block number known by the L2 system.
     */
24:    uint64 public number;

 /**
     * @notice The latest L1 timestamp known by the L2 system.
     */
29:    uint64 public timestamp;


```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L21-L29

### Recommended Mitigation
Generally recommended to use uint256 for variables like block.number and timestamp to ensure compatibility and prevent potential problems in the future








Use require instead of assert 2

Add deadline argument to structHash` variable 1

A single point of failure 10

Add verifyingContract to EIP712_DOMAIN_TYPEHASH

BytecodeCompressor.publishCompressedBytecode() can receive funds

No event emitted when updating a state variable

 Unjustified unchecked

(OOS) Discouraged use of safeApprove. Use safeIncreaseAllowance here instead

No need to check that v == 27 || v == 28 with ecrecover

Tautology when checking recoveredAddress

191:         return recoveredAddress == address(this) && recoveredAddress != address(0);  
Indeed:

if recoveredAddress == address(this) is true, then recoveredAddress != address(0) will always be true
if recoveredAddress == address(this) is false, then recoveredAddress != address(0) will never be evaluated

Document why all fields under ZkSyncMeta aren't returned in SystemContractHelper.getZkSyncMeta()

Default Visibility for constants 

SystemContext.sol:48:    uint256 constant BLOCK_INFO_BLOCK_NUMBER_PART = 2 ** 128;  

unction DefaultAccount._validateTransaction() shouln't check trx.value for required balance, maybe user wanted the transaction to fail. also maybe paymaster is going to transfer required balance later

In functions unsafeOverrideBlock() and setNewBlock() of SystemContext, code should check that timestamp is less than BLOCK_INFO_BLOCK_NUMBER_PART, otherwise it can overflow and change the block number when combining them to calculate block info.

Lose of precision 


Avoid divide by zero 

##



## [NC] 















# NON CRITICAL FINDINGS

##

## [NC-1] Owner Can renounce the Ownership





## [NC-1] Do not calculate constants every time 

it is generally a good practice to avoid calculating constants every time they are needed. Instead, you can define them as constants and assign them a value.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

43: bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

##

## [NC-2] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

43: bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

##

## [NC-3] Move require check on top of the initialize() function

Consider more require check on top of the initialize() function to avoid unwanted revert after all state changes.
First check gaslimit then perform necessary value and state changes 
 
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L133-L143

##

## [NC] Use private for constants instead of public 

If decide to change the value of a constant or modify its internal representation, using the private modifier allows you to make those changes without affecting external contracts or users. It provides you with the freedom to refactor your contract's internal logic without breaking the functionality of other contracts that depend on your constants.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

28: uint256 public constant DECIMALS = 6;

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L28

##

## [NC] Use more recent version of solidity

Latest solidity version is 0.8.19 

### Instances 
ALL SCOPE CONTRACTS

Recommended Mitigation:
Use at least solidity 0.8.17 

##

## [NC-] No same value input control 

Ensure that the ownership transfer only occurs when the new owner address is different from the current owner address. If they are the same, the function will simply emit the event without modifying the ownership state

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180-L185

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218C5-L224

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37-L45



##

## [N-] Add timelock for critical value changes 

Adding a timelock mechanism for critical value changes is a good practice to enhance the security and governance of your smart contracts. A timelock introduces a delay between proposing a value change and actually executing it, allowing stakeholders to review and approve the change before it takes effect.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180-L185

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218C5-L224

##

## [NC-9] For functions, follow Solidity standard naming conventions (internal function style rule)

Description
The bellow codes don’t follow Solidity’s standard naming convention,

internal and private functions and variables : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L37

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L55

##

## [NC-12] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

### Recommendation
NatSpec comments should be increased in Contracts

##

## [NC-15] Contract layout and order of functions

The Solidity style guide recommends declaring state variables before all functions. 

(https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout)

CONTEXT
ALL CONTRACT

### Layout contract elements in the following order:

- Pragma statements

- Import statements

Interfaces

- Libraries

- Contracts

### Inside each contract, library or interface, use the following order:

- Type declarations

- State variables

- Events

- Modifiers

- Functions

### Recommendations 

All contracts should follow the solidity style guide 

##

## [NC-19] TYPOS

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol

/// @audit unaliased meaning less word 
48:  * @notice Overrides the implementation of the `onlyOwner` modifier to check that the unaliased

FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol

/// @audit transfering => transferring 
@notice The L2StandardBridge is responsible for transfering ETH and ERC20 tokens between L1 and

FILE: optimism/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
11: * @notice The L1StandardBridge is responsible for transfering ETH and ERC20 tokens between L1 and

FILE: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

/// @audit whcih => which
28: * @custom:field timestamp     Timestamp at whcih the withdrawal was proven.

FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

/// @audit derviation => derivation 
13: *         configuration is stored on L1 and picked up by L2 as part of the derviation of the L2

/// @audit distrubution=> distribution
24: *                                    block distrubution.


```
## [NC-20] public functions not called by the contract should be declared external instead

Contracts are [allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from external to public.

```solidity
FILE: 2023-04-rubicon/contracts/BathHouseV2.sol

45: function getBathTokenFromAsset(
        address asset
    ) public view returns (address) {

```
[BathHouseV2.sol#L45-L47](https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/BathHouseV2.sol#L45-L47)

```solidity
FILE: 2023-04-rubicon/contracts/RubiconMarket.sol

574:  function getFeeBPS() public view returns (uint256) {
1010: function getFirstUnsortedOffer() public view returns (uint256) {
1016: function getNextUnsortedOffer(uint256 id) public view returns (uint256) {
1020: function isOfferSorted(uint256 id) public view returns (bool) {

1165: function getPayAmountWithFee(
        ERC20 pay_gem,
        ERC20 buy_gem,
        uint256 buy_amt
    ) public view returns (uint256 fill_amt) {


```
[RubiconMarket.sol#L574](https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L574)

##

## [NC-21] Use scientific notations rather than exponential notations

https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1175-L1177
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1142-L1144
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1099-L1101
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1057-L1059
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L331
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L317

##

## [NC-22] Use underscores for number literals

```solidity
FILE: 2023-04-rubicon/contracts/utilities/poolsUtility/Position.sol

459: uint256 _fee = _maxFill.mul(rubiconMarket.getFeeBPS()).div(10000);
481: uint256 _fee = _minFill.mul(_feeBPS).div(10000);
490:  _fee = _payAmount.mul(_feeBPS).div(10000);

```
```solidity
FILE : 2023-04-rubicon/contracts/RubiconMarket.sol

346:  uint256 mFee = mul(spend, makerFee()) / 100_000;
338:  uint256 fee = mul(spend, feeBPS) / 100_000;

583:  _amount -= mul(amount, feeBPS) / 100_000;
583: _amount -= mul(amount, makerFee()) / 100_000;

```
[RubiconMarket.sol#L583](https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L583)


### Recommended Mitigation

```solidity
459: uint256 _fee = _maxFill.mul(rubiconMarket.getFeeBPS()).div(10_000);

```

##

## [NC-23] Unused variables 

```solidity

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> contracts/utilities/poolsUtility/Position.sol:528:9:
    |
528 |         address _asset,
    |         ^^^^^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> contracts/RubiconMarket.sol:298:9:
    |
298 |         uint256 id = uint256(id_);
    |         ^^^^^^^^^^


```

##

## [NC-24] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand

```solidity
FILE: 2023-04-rubicon/contracts/RubiconMarket.sol

232: bytes32 internal constant MAKER_FEE_SLOT = keccak256("WOB_MAKER_FEE");

```
[RubiconMarket.sol#L232](https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L232)

##

## [Nc-25] Tokens accidentally sent to the contract cannot be recovered

### Context
contracts/staking/NeoTokyoStaker.sol:

It can’t be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps
Add this code:

 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

##

## [NC-26] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

##

## [NC-27] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns).

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1040
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1080
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1233
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1244
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1252
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1319
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1372
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1392
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L1426
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L392-L393
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L340

##

## [NC-28] Use constants instead of using numbers directly  

https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L490
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L481
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/utilities/poolsUtility/Position.sol#L459
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L346
https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L338

##

## [NC-29] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword

```solidity
FILE: 2023-04-rubicon/contracts/RubiconMarket.sol

691: mapping(uint256 => sortInfo) public _rank; 
692: mapping(address => mapping(address => uint256)) public _best; 
693: mapping(address => mapping(address => uint256)) public _span;  sorted orderbook
694: mapping(address => uint256) public _dust; 
695: mapping(uint256 => uint256) public _near; 
```
### Recommended Mitigation

```solidity
FILE: 2023-04-rubicon/contracts/RubiconMarket.sol

691: mapping (uint256 => sortInfo) public _rank; 
```

##

## [NC-30] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

```Solidity
FILE: 2023-04-rubicon/contracts/RubiconMarket.sol

648:  assembly {
```
[RubiconMarket.sol#L648](https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L648)

```solidity
FILE: 2023-04-rubicon/contracts/utilities/poolsUtility/Position.sol

367:  assembly {

```
##

## [NC-31] Large multiples of ten should use scientific notation (e.g. 1e5) rather than decimal literals (e.g. 100000), for readability

```solidity
FILE : 2023-04-rubicon/contracts/RubiconMarket.sol

346:  uint256 mFee = mul(spend, makerFee()) / 100_000;
338:  uint256 fee = mul(spend, feeBPS) / 100_000;

583:  _amount -= mul(amount, feeBPS) / 100_000;
583: _amount -= mul(amount, makerFee()) / 100_000;

```
[RubiconMarket.sol#L583](https://github.com/code-423n4/2023-04-rubicon/blob/511636d889742296a54392875a35e4c0c4727bb7/contracts/RubiconMarket.sol#L583)














 


