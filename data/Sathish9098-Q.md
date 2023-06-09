# LOW FINDINGS

| Issue Count | Issues | Instances |
| -------------- | -------------- | -------------- |
| [L-1]| L2OutputOracle.proposeL2Output() can receive funds | 1 |
| [L-2] | Use Ownable2StepUpgradeable rather than OwnableUpgradeable | 5 |
| [L-3]| onlyOwner single point of failure  | 5 |
| [L-4] | Avoid using tx.origin  | 1 |
| [L-5] | Usage of tranferFrom function is discouraged | 1 |
| [L-6] | Hard Coded Address for DEPOSITOR_ACCOUNT not appreciated | 1 |
| [L-7] | ``number,timestamp`` should be upgraded to uint256 prevent potential problems in the future | 2 |
| [L-8] | getL1GasUsed() estimates based on a simple gas cost calculation for zero and non-zero bytes. This is not accurately reflect the actual gas cost on the Ethereum network  | 1 |
| [L-9] | Unsafe Down casting  | 3 |
| [L-10] | Possible to front-run the initialize() function in certain scenarios | 3 |
| [L-11] | Can refactor the gasPrice(),baseFee() function into one since both returns the same values | 1 |
| [L-12] | No Storage Gap for SystemConfig and SystemDictator Contract  | 2 |
| [L-13] | Missing Event for critical parameters initialize and change | 3 |
| [L-14] | Lack of Sanity/Threshold/Limit Checks | 8 |
| [L-15] | Token ownership and approval, is not checked before transfer  | 1 |
| [L-16] | Inconsistence solidity versions  | - |
| [L-17] | Critical Address changes should use two step procedure  | 2 |
| [L-18] | Hardcoded the RECEIVE_DEFAULT_GAS_LIMIT may cause problems in the future  | 1 |
| [L-19] | No maximum limit for _gasLimit  | 1 |
| [L-20] | Any one can call burn() and remove all the Ether (ETH) held by the contract | 1 |


# NON CRITICAL FINDINGS

| Issue Count | Issues | Instances |
| -------------- | -------------- | -------------- |
| [NC-1] | Consider disabling renounceOwnership()  | 2 |
| [NC-2] | Do not calculate constants every time  | 1 |
| [NC-3] | EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT | 1 |
| [NC-4] | Move require check on top of the initialize() function | 1 |
| [NC-5] | Use private for constants instead of public  | 6 |
| [NC-6] | Use more recent version of solidity | - |
| [NC-7] | No same value input control  | 5 |
| [NC-8] | Add timelock for critical value changes  | 4 |
| [NC-9] | For functions, follow Solidity standard naming conventions (internal function style rule) | 2 |
| [NC-10] | NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS | - |
| [NC-11] | Contract layout and order of functions | - |
| [NC-12] | TYPOS | 6 |
| [NC-13] | Use SMTChecker | - |
| [NC-14] | Constants on the left are better | 1 |
| [NC-15] | According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword|3|
| [NC-16] | Assembly Codes Specific – Should Have Comments | 3 |
| [NC-17] | Event is not properly indexed | 3 |
| [NC-18] | Consider using named mappings| 3 |
| [NC-19] | Public functions not called by contract can be declared external  | - |
| [NC-20] | _recipient can be a address(0) | 1 |

##

## [L-1] L2OutputOracle.proposeL2Output() can receive funds

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

## [L-2] Use Ownable2StepUpgradeable rather than OwnableUpgradeable

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

##

## [L-3] onlyOwner single point of failure 

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

## [L-4] Avoid using tx.origin 

### Impact
tx.origin is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

This can make it easier to create a vault on behalf of another user with an external administrator (by receiving it as an argument)

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

417: if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L417

### Recommended Mitigation

Consider using msg.sender which helps maintain the security and integrity of your contract 

##

## [L-5] Usage of tranferFrom function is discouraged

As per [ERC721 Docs](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) tranferFrom function is discouraged

```
transferFrom(address from, address to, uint256 tokenId) public

Transfers the ownership of a given token ID to another address. Usage of this method is discouraged, use safeTransferFrom whenever possible. Requires the msg.sender to be the owner, approved, or operator.

```
### Impact

The reason for discouraging the use of transferFrom is primarily for security purposes. The transferFrom function requires the msg.sender (the caller of the function) to be the owner of the token, approved by the owner, or an approved operator. This requirement ensures that only authorized entities can transfer the token

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol

101: IERC721(_localToken).transferFrom(_from, address(this), _tokenId);

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L101

### Recommended Mitigation

use safeTransferFrom() function


##

## [L-6] Hard Coded Address for DEPOSITOR_ACCOUNT not appreciated 

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

## [L-7] ``number,timestamp`` should be upgraded to uint256 prevent potential problems in the future

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


##

## [L-8] getL1GasUsed() estimates based on a simple gas cost calculation for zero and non-zero bytes. This is not accurately reflect the actual gas cost on the Ethereum network 

### Impact

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

## [L-9] Unsafe Down casting 

### Impact 
When downcasting block.number from uint256 to uint64, you should exercise caution as it can be potentially unsafe. The ``block.number`` returns a uint256 value representing the current block number, which is guaranteed to fit within 256 bits.

However, downcasting to uint64 may result in a loss of precision or ``potential truncation`` if the block number exceeds the range of uint64, which can lead to unexpected behavior or errors.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol

183: prevBlockNum: uint64(block.number)

```

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

225: timestamp: uint128(block.timestamp),
226: l2BlockNumber: uint128(_l2BlockNumber)

```
### Recommended Mitigation Steps:
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256.

##

## [L-10] Possible to front-run the initialize() function in certain scenarios

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
Add control initialize() functions 
 

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

## [L-12] No Storage Gap for SystemConfig and SystemDictator Contract 

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

## [L-13] Missing Event for critical parameters initialize and change

### Impact

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

 it is recommended to emit an event for critical parameter changes. Missing events for critical parameters that change should be detected. This is because events allow capturing the changed parameters so that they can be tracked off-chain

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L120-L131

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L165-L169

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79-L103

### Recommendation
Add Event-Emit

##

## [L-14] Lack of Sanity/Threshold/Limit Checks

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

## [L-15] Token ownership and approval, is not checked before transfer 

The address executing the safeTransferFrom function should have the necessary ownership or approval to transfer the token. Otherwise, the function call will revert, and the transfer will not occur

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol

68: IERC721(_localToken).safeTransferFrom(address(this), _to, _tokenId);

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L68

### Recommended Mitigation:
 Implement proper security measures before token transfer 



##

## [L-16] Inconsistence solidity versions 

### Impact

If there are inconsistencies in the Solidity versions used in your codebase or contracts, it can lead to compilation errors and potential compatibility issues. Different Solidity versions may introduce changes, deprecate certain features, or modify syntax, resulting in code that may not compile or behave as expected

- Compilation Errors
- Syntax Differences
- Compatibility Issues
- Limited Access to New Features

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L2

```
CrossDomainOwnable3.sol,CrossDomainOwnable.sol contracts using the lower version 0.8.0 but other contracts using 0.8.15

```
### Recommended Mitigation:
Maintain same versions for all contracts 

##

## [L-17] Critical Address changes should use two step procedure 

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


##

## [L-18] Hardcoded the RECEIVE_DEFAULT_GAS_LIMIT may cause problems in the future 

The variable ``RECEIVE_DEFAULT_GAS_LIMIT`` is defined as constant and once value is assigned cannot be changed later.

EVM-Based blockchains are hardforked and there is no such thing as Gas Limit etc. values may change, this has happened in the past, so it is recommended to have this value updated in the future.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

45: uint64 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#LL45C1-L45C1

##

## [L-19] No maximum limit for _gasLimit 

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

## [L-20] Any one can call burn() and remove all the Ether (ETH) held by the contract

### Impact
The burn function allows anyone to call it and remove all the Ether (ETH) held by the contract from the state

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

85: function burn() external {
        uint256 balance = address(this).balance;
        Burn.eth(balance);
        emit WithdrawerBalanceBurnt(balance);
    }

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L85-L89

### Recommended Mitigation

Consider implementing proper access restrictions, permission checks, or other security measures to ensure that only authorized entities can invoke critical functions like burn


##

# NON CRITICAL FINDINGS

##

## [NC-1] Consider disabling renounceOwnership() 

If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's Ownable's renounceOwnership() function in order to disable it.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L4-L6

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6

##

## [NC-2] Do not calculate constants every time 

it is generally a good practice to avoid calculating constants every time they are needed. Instead, you can define them as constants and assign them a value.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

43: bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

##

## [NC-3] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

43: bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

##

## [NC-4] Move require check on top of the initialize() function

Consider more require check on top of the initialize() function to avoid unwanted revert after all state changes.
First check gaslimit then perform necessary value and state changes 
 
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L133-L143

##

## [NC-5] Use private for constants instead of public 

If decide to change the value of a constant or modify its internal representation, using the private modifier allows you to make those changes without affecting external contracts or users. It provides you with the freedom to refactor your contract's internal logic without breaking the functionality of other contracts that depend on your constants.

```solidity
FILE: optimism/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

28: uint256 public constant DECIMALS = 6;

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L28

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L36

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L19

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L27



##

## [NC-6] Use more recent version of solidity

Latest solidity version is 0.8.19 

### Instances 
ALL SCOPE CONTRACTS

Recommended Mitigation:
Use at least solidity 0.8.17 

##

## [NC-7] No same value input control 

Ensure that the ownership transfer only occurs when the new owner address is different from the current owner address. If they are the same, the function will simply emit the event without modifying the ownership state

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180-L185

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218C5-L224

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37-L45

##

## [NC-8] Add timelock for critical value changes 

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

## [NC-10] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

### Recommendation
NatSpec comments should be increased in Contracts

##

## [NC-11] Contract layout and order of functions

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

## [NC-12] TYPOS


FILE: optimism/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol
/// @audit unaliased meaning less word 
48:  * @notice Overrides the implementation of the `onlyOwner` modifier to check that the unaliased

FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol
/// @audit transfering => transferring 
@notice The L2StandardBridge is responsible for transfering ETH and ERC20 tokens between L1 and

FILE: optimism/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
/// @audit transfering => transferring 
11: * @notice The L1StandardBridge is responsible for transfering ETH and ERC20 tokens between L1 and

FILE: optimism/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol
/// @audit whcih => which
28: * @custom:field timestamp     Timestamp at whcih the withdrawal was proven.

FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol
/// @audit derviation => derivation 
13: *         configuration is stored on L1 and picked up by L2 as part of the derviation of the L2

/// @audit distrubution=> distribution
24: *                                    block distrubution.

##

## [NC-13] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

##

## [NC-14] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns).

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L121

##

## [NC-15] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L20

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72-L77

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L32

##

## [NC-16] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L169-L171

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L234-L236

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L162-L164

##

## [NC-17] Event is not properly indexed

Index event fields make the field more quickly accessible to off-chain tools that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be indexed.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L30-L35

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L80

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L26-L30

##

## [NC-18] Consider using named mappings

Consider moving to solidity version 0.8.18 or later, and using named mappings to make it easier to understand the purpose of each mapping

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L20

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72-L77

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L32

##

## [NC-19] Public functions not called by contract can be declared external 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L28

##

## [NC-20] _recipient can be a address(0)

Assigning address(0) to the _recipient parameter typically indicates that the transfer or action associated with the function is not intended to have a specific recipient.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L20

### Recommended Mitigation

_recipient should be checked before transfer tokens 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L19






















 


