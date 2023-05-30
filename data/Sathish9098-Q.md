# LOW FINDINGS

##

## [L-4] Possible to front-run the initialize() function in certain scenarios

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

### Recommended Mitigation 







# [L-1] Use Ownable2StepUpgradeable rather than OwnableUpgradeable

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

## [L-2] No Storage Gap for SystemConfig and SystemDictator Contract 

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

## [L-3] Missing Event for critical parameters initialize and change

Description
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

 it is recommended to emit an event for critical parameter changes. Missing events for critical parameters that change should be detected. This is because events allow capturing the changed parameters so that they can be tracked off-chain

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198


Recommendation
Add Event-Emit

##

## [L-5] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values.

Consider also adding an optional codehash check for immutable address at the constructor since the zero address check cannot guarantee a matching address has been inputted.

These checks are generally not implemented in the contracts

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L143

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198


A single point of failure









# NON CRITICAL FINDINGS

##

## [NC-1] 

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

 


