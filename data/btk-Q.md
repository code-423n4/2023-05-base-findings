| Total Low issues |
|------------------|

| Risk   | Issues Details                                                                                            | Number        |
|--------|-----------------------------------------------------------------------------------------------------------|---------------|
| [L-01] | Critical changes should use-two step procedure                                                            | 4             |
| [L-02] | Avoid using `tx.origin`                                                                                   | 2             |
| [L-03] | No Storage Gap for Upgradeable contracts                                                                  | 5             |
| [L-04] | Loss of precision due to rounding                                                                         | 1             |
| [L-05] | Integer overflow by unsafe casting                                                                        | 1             |
| [L-06] | Add `address(0)` check for the critical changes                                                           | 1             |
| [L-07] | Multiple Pragma used                                                                                      | All Contracts |
| [L-08] | Missing Event for initialize                                                                              | 5             |

## [L-01] Critical changes should use-two step procedure

#### Description

The following contracts inherits the Openzeppelin [`Ownable`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) contract which have a function that allows the owner to transfer the ownership to a different address. If the owner accidentally uses an invalid address for which they do not have the private key, then the system will gets locked.

#### Lines of code 

```solidity
import {
    OwnableUpgradeable
} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
```

- [SystemConfig.sol#L6](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L6)

```solidity
import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

- [CrossDomainOwnable.sol#L4](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4)

```solidity
import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

- [CrossDomainOwnable2.sol#L6](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6)

```solidity
import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

- [CrossDomainOwnable3.sol#L6](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6)

#### Recommended Mitigation Steps

Consider inheriting [`Ownable2Step`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) instead.

A similar issue was reported in a previous contest and was assigned a severity of medium: code-423n4/2021-06-realitycards-findings#105

## [L-02] Avoid using `tx.origin`

#### Description

`tx.origin` is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

#### Lines of code 

```solidity
        if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {
```

- [OptimismPortal.sol#L417](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L417)

```solidity
        if (msg.sender != tx.origin) {
```

- [OptimismPortal.sol#L465](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L465)

#### Recommended Mitigation Steps

Avoid using `tx.origin`.

## [L-03] No Storage Gap for Upgradeable contracts

#### Description

For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

#### Lines of code 

```solidity
    function initialize() public initializer {
```

- [L1CrossDomainMessenger.sol#L38](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L38)

```solidity
    function initialize(uint256 _startingBlockNumber, uint256 _startingTimestamp)
```

- [L2OutputOracle.sol#L120](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L120)

```solidity
    function initialize(bool _paused) public initializer {
```

- [OptimismPortal.sol#L165](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L165)

```solidity
    function initialize(
```

- [SystemConfig.sol#L125](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125)

```solidity
    function initialize() public initializer {
```

- [L2CrossDomainMessenger.sol#L34](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L34)

#### Recommended Mitigation Steps

Consider adding a storage gap at the end of the upgradeable contract:
```solidity
  /**
   * @dev This empty reserved space is put in place to allow future versions to add new
   * variables without shifting down storage in the inheritance chain.
   * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
   */
  uint256[50] private __gap;
```

## [L-04] Loss of precision due to rounding

#### Description

Solidity may truncate the result due to the nature of arithmetics and rounding errors. Hence we must multiply before dividing to prevent such loss in precision

#### Lines of code 

```solidity
            ((_config.maxResourceLimit / _config.elasticityMultiplier) *
                _config.elasticityMultiplier) == _config.maxResourceLimit,
```

- [SystemConfig.sol#L290-L291](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L290-L291)

#### Recommended Mitigation Steps

Multiplication should always be performed before division to avoid loss of precision.

## [L-05] Integer overflow by unsafe casting

#### Description

Keep in mind that the version of solidity used, despite being greater than 0.8, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

#### Lines of code 

```solidity
            params.prevBaseFee = uint128(uint256(newBaseFee));
```

- [ResourceMetering.sol#L135](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L135)

#### Recommended Mitigation Steps

Use OpenZeppelin [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) library.

## [L-06] Add `address(0)` check for the critical changes

#### Description

Check of `address(0)` to protect the code from `(0x0000000000000000000000000000000000000000)` address problem just in case. This is best practice or instead of suggesting that they verify `_address != address(0)`, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

#### Lines of code 

```solidity
    function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {
        _setUnsafeBlockSigner(_unsafeBlockSigner);

        bytes memory data = abi.encode(_unsafeBlockSigner);
        emit ConfigUpdate(VERSION, UpdateType.UNSAFE_BLOCK_SIGNER, data);
    }
```

- [SystemConfig.sol#L180-L185](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180-L185)

#### Recommended Mitigation Steps

Add checks for `address(0)` when assigning values to address state variables.	

## [L-07] Multiple Pragma used 

#### Description

Solidity pragma versions should be exactly same in all contracts. Currently some contracts use `0.8.15` and others use `^0.8.0`

#### Recommended Mitigation Steps

Use one version for all contracts.

## [L-08] Missing Event for initialize

#### Description

Events help non-contract tools to track changes, and events prevent users from being surprised by changes Issuing event-emit during initialization is a detail that many projects skip.

#### Lines of code 

```solidity
    function initialize() public initializer {
```

- [L1CrossDomainMessenger.sol#L38](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L38)

```solidity
    function initialize(uint256 _startingBlockNumber, uint256 _startingTimestamp)
```

- [L2OutputOracle.sol#L120](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L120)

```solidity
    function initialize(bool _paused) public initializer {
```

- [OptimismPortal.sol#L165](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L165)

```solidity
    function initialize(
```

- [SystemConfig.sol#L125](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125)

```solidity
    function initialize() public initializer {
```

- [L2CrossDomainMessenger.sol#L34](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L34)

#### Recommended Mitigation Steps

Add Event-Emit
