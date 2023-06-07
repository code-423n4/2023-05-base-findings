# LOW FINDINGS

##

## [L-1] No maximum limit for _gasLimit 

Only Minimum value only checked.  

To ensure the contract operates predictably, avoids excessive gas consumption, and protects against potential attacks, it is generally recommended to set appropriate maximum gas limits for each function or transaction. The gas limit should be determined based on careful analysis of the contract's logic, potential gas consumption patterns, and consideration of security and cost-efficiency factors

```solidity
FILE: Breadcrumbsoptimism/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

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

Description
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

 it is recommended to emit an event for critical parameter changes. Missing events for critical parameters that change should be detected. This is because events allow capturing the changed parameters so that they can be tracked off-chain

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198


Recommendation
Add Event-Emit

##

## [L-6] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values.

Consider also adding an optional codehash check for immutable address at the constructor since the zero address check cannot guarantee a matching address has been inputted.

These checks are generally not implemented in the contracts

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180-L185

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L210

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218-L224

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

## [L-] L2OutputOracle.proposeL2Output() can receive funds

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

## [L-] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256

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

## [L-] Possibility of overflow when multiplying two uint256 values together

There is a possibility of overflow when multiplying two uint256 values together. The result of the multiplication may exceed the maximum value that can be represented by a uint256







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

## [NC] Meaning less variable declarations






 


