`Low Risk & Non Critical Findings`
# Report
## Low-Risk Findings 
**Low-Risk Findings List**
| Number | Issue Details | Instances |
|-----:|----|-----|
| L-01 | Missing `storage gaps` in Implementation contracts. | 5 |
| L-02 | Missing `zero address` checks on Critical `State` changing parameters. | 2 in all `Proxies` |
| L-03 | No need for a `redundant` Function where the Function `createStandardL2Token()` is redundant and might express a lack of trustworthiness of the protocol. | 1 |
| L-04 | Missing `non-reentrant` modifier on Critical function | 2 |
| L-05 | `Ownership` maybe be lost. | 2 |
| L-06 | `Unsafe` Down-cast. | 1 |
| L-07 | `Unbounded Loops` may result in `DOS`. | 1 |
| L-08 | Critical `Implementations` Initializers not `Locked`. | 5 |
| L-09 | The `onlyEOA` modifier can be bypassed. | 1 |
| L -10 | Loss of `Precision`. | 1 |
| L   -11 | Division by `Zero` not prevented. | 2 |
| L  -12 | Missing checks for the `basefee` and `l1FeeScalar` input values. | 1 |
> Total ~ 12 Issues

# L-01 Missing `storage gaps` in Implementation contracts.
## Summary 
See this [link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
## Vulnerability Details 

- [SystemConfig.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL4C1-L6C76)
- [L2CrossDomainMessenger.sol](https://github.com/ethereum-optimism/optimism/blob/develop/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol)
- [SystemDictator.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L466)
- [L2CrossDomainMessenger.sol](https://github.com/ethereum-optimism/optimism/blob/develop/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol)
- [OptimismPortal.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#LL72C1-L77C67)


# L-02 Missing `zero address` checks on Critical `State` changing parameters
## Summary 
`Zero-address` checks are a best-practice for input validation of critical address parameters. While the codebase applies this to most addresses in setters, there are many places where this is missing in mostly in the proxy contracts.
## Vulnerability Details 
[_changeAdmin](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/Proxy.sol#LL155C1-L161C6) in the proxy contracts.
```solidity
    function _changeAdmin(address _admin) internal {
        address previous = _getAdmin();
        assembly {
            sstore(OWNER_KEY, _admin)
        }
        emit AdminChanged(previous, _admin);
    }
```
[_setImplementation](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/Proxy.sol#LL143C1-L148C6) in the proxy contracts.
```solidity
    function _setImplementation(address _implementation) internal {
        assembly {
            sstore(IMPLEMENTATION_KEY, _implementation)
        }
        emit Upgraded(_implementation);
    }
  ```
# L-03 No need for a `redundant` Function where the Function `createStandardL2Token()` is redundant and might express a lack of trustworthiness of the protocol.
## Summary 
I understand the developers love an intuitive functions name and it's cool, but the function `createStandardL2Token()`'s only logic is to call the function `createOptimismMintableERC20()` which creates an instance of the OptimismMintableERC20 contract, but an issue results from the function being called that is the `createOptimismMintableERC20()` function having its visibility set to `public` which means it can also be called externally.
this creates confusion for the external viewer as to why two functions will perform the `same task.` This in turn will reflect on the trustworthiness of the whole protocol.
## Vulnerability Details 
```solidity
 function createStandardL2Token(
        address _remoteToken,
        string memory _name,
        string memory _symbol
    ) external returns (address) {
        return createOptimismMintableERC20(_remoteToken, _name, _symbol);
    }
  ```  
[createStandardL2Token](https://github.com/ethereum-optimism/optimism/blob/342e95273b43e1d417906316ecce65ee63bc84a0/packages/contracts-bedrock/contracts/universal/OptimismMintableERC20Factory.sol#L70C1-L76)
```solidity
 function createOptimismMintableERC20(
        address _remoteToken,
        string memory _name,
        string memory _symbol
    ) public returns (address) {
    //more code ...
    }
```
[createOptimismMintableERC20](https://github.com/ethereum-optimism/optimism/blob/342e95273b43e1d417906316ecce65ee63bc84a0/packages/contracts-bedrock/contracts/universal/OptimismMintableERC20Factory.sol#L87)
## Recommended Mitigation Step.
I recommended changing the name of the `createOptimismMintableERC20()` function to be more intuitive and making it the sole function to be called for creating an instance of the `OptimismMintableERC20` contract, thereby making the code simple and more trustworthy.

# L-04 Missing `non-reentrant` modifier on Critical function.
## Summary 
A critical function missing a non-reentrant modifier could cause the contract to be vulnerable to reentracy which could cause a lot of harm if exploited by an attacker.
## Vulnerability Details 
- [withdraw](https://github.com/ethereum-optimism/optimism/blob/d69cb12f6dcbe3d5355beca8997fbac611b7fe37/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#LL96C1-L103C6) function in `L2StandardBridge.sol`.
```solidity 
    function withdraw(
        address _l2Token,
        uint256 _amount,
        uint32 _minGasLimit,
        bytes calldata _extraData
    ) external payable virtual onlyEOA {
        _initiateWithdrawal(_l2Token, msg.sender, msg.sender, _amount, _minGasLimit, _extraData);
    }
```
- [withdrawTo](https://github.com/ethereum-optimism/optimism/blob/d69cb12f6dcbe3d5355beca8997fbac611b7fe37/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#LL121C1-L129C6) function.
# L-05 `Ownership` maybe be lost. 
## Summary 
Ownable2Step and Ownable2StepUpgradeable prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.
## Vulnerability Details 
[`SystemConfig.sol`](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL4C1-L6C76)
```solidity 
import {
    OwnableUpgradeable
} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
```

# L-06 `Unsafe` Down-cast
## Summary 
When a type is downcast to a smaller type, the higher-order bits are truncated, effectively applying a modulo to the original value. Without any other checks, this wrapping will lead to unexpected behavior and bugs
## Vulnerability Details 
 [minimumGasLimit()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL155C1-L155C98) function `SystemConfig.sol`
 ```solidity 
        return uint64(_resourceConfig.maxResourceLimit) + uint64(_resourceConfig.systemTxMaxGas);
```
# L-07 `Unbounded Loops` may result in `DOS`
## Summary 
Consider limiting the number of iterations in for-loops that can be called by anyone as they could call with an arbitrary amount of input causing the contract to surpass the block gas limit.
## Vulnerability Details 
[`getL1GasUsed`](https://github.com/ethereum-optimism/optimism/blob/d69cb12f6dcbe3d5355beca8997fbac611b7fe37/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#LL117C1-L130C2) function in [`GasPriceOracle.sol`](https://github.com/ethereum-optimism/optimism/blob/develop/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#LL117C1-L130C2)
```solidity 
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
}
``` 

# L-08 Critical `Implementations` Initializers not `Locked`.
## Summary 
OpenZeppelin’s best practice is to lock the Initializers in the function by calling the [`_disableintializers`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/cc0426317052b1850b686f2fbbcf481520399735/contracts/proxy/utils/Initializable.sol#LL137C1-L152C1) function in the contract but this isn't done here in this protocol.
## Vulnerability Details 
- [SystemConfig.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL4C1-L6C76)
- [L2CrossDomainMessenger.sol](https://github.com/ethereum-optimism/optimism/blob/develop/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol)
- [SystemDictator.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L466)
- [L2CrossDomainMessenger.sol](https://github.com/ethereum-optimism/optimism/blob/develop/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol)
- [OptimismPortal.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#LL72C1-L77C67)

# L-09 The `onlyEOA` modifier can be bypassed.
## Summary 
The onlyEOA modifier is used on a function to ensure that it cannot be called by a contract however this assumption is wrong due to the fact that the check of the modifier can be passed during the creation of a contract via its constructor, and could be exploited if it's unintended by the developers that a Smart Contract should call that function.
## Vulnerability Details 
[`receive()`](https://github.com/ethereum-optimism/optimism/blob/d69cb12f6dcbe3d5355beca8997fbac611b7fe37/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#LL71C1-L83C6) function in `L2StandardBridge.sol`
```solidity  
  /**
     * @notice Allows EOAs to bridge ETH by sending directly to the bridge.
     */
    receive() external payable override onlyEOA {
        _initiateWithdrawal(
            Predeploys.LEGACY_ERC20_ETH,
            msg.sender,
            msg.sender,
            msg.value,
            RECEIVE_DEFAULT_GAS_LIMIT,
            bytes("")
        );
    }
```
## Recommendation
add additional checks to ensure it's an EOA and be careful as to the implications.
# L-10 Loss of `Precision`.
## Summary 
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator
## Vulnerability Details 
[getL1Fee](https://github.com/ethereum-optimism/optimism/blob/d69cb12f6dcbe3d5355beca8997fbac611b7fe37/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48) function in `GasPriceOracle.sol`
```solidity
uint256 scaled = unscaled / divisor;
```
# L-11 Division by `Zero` not prevented.
## Summary 
The divisions below take an input parameter that does not have any zero-value checks, which may lead to the functions reverting when zero is passed.
## Vulnerability Details                 
[getL1Fee](https://github.com/ethereum-optimism/optimism/blob/d69cb12f6dcbe3d5355beca8997fbac611b7fe37/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48) function in `GasPriceOracle.sol`
```solidity
uint256 scaled = unscaled / divisor;
```            
[_setResourceConfig](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL290C1-L291C75) function in the   `SystemConfig.sol`
```solidity
            ((_config.maxResourceLimit / _config.elasticityMultiplier) *
                _config.elasticityMultiplier) == _config.maxResourceLimit,
 ```    
# [L‑12] Missing checks for the `basefee` and `l1FeeScalar` input values.

The input values for the `basefee` and `l1FeeScalar` variables have no checks to ensure that their values are greater than zero. If this ever happens, it will lead to disastrous consequences which will affect all other functionalities that are dependent on these variables like the fee calculations.

```solidity
function setL1BlockValues(
        uint64 _number,
        uint64 _timestamp,
        uint256 _basefee,
        bytes32 _hash,
        uint64 _sequenceNumber,
        bytes32 _batcherHash,
        uint256 _l1FeeOverhead,
        uint256 _l1FeeScalar
    ) external {
        require(
            msg.sender == DEPOSITOR_ACCOUNT,
            "L1Block: only the depositor account can set L1 block values"
        );

        number = _number;
        timestamp = _timestamp;
        basefee = _basefee;
        hash = _hash;
        sequenceNumber = _sequenceNumber;
        batcherHash = _batcherHash;
        l1FeeOverhead = _l1FeeOverhead;
        l1FeeScalar = _l1FeeScalar;
    }
  ```  

`Non-Critical Findings`

## Non-Critical Issues

**Non-Critical Issues List**

| Number | Issue Details | Instances |
|-----:|----|-----|
| NC-01 | Consider Using `Named Mappings` | 2 |
| NC-02 | Use `constants` instead of `Magic Numbers` | 3 |
| NC-03 | Use `Double Quote` for string literals | 2 |
| NC-04 | Use `Immutable` in place of `constants` | 1|
> Total ~ 4 Issues
> 
# NC-01 Consider Using `Named Mapping`
## Summary 
Consider moving to solidity version 0.8.18 or later, and using named [mappings](https://ethereum.stackexchange.com/a/145555) to make it easier to understand the purpose of each mapping.
## Vulnerability Details 
[OptimismPortal.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#LL72C1-L77C67)
```solidity
    mapping(bytes32 => bool) public finalizedWithdrawals;

    /**
     * @notice A mapping of withdrawal hashes to `ProvenWithdrawal` data.
     */
    mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;
```
# NC-02 Use `constants` instead of `Magic Numbers`
## Summary 
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#LL197C1-L197C40)
```solidity
        return _byteCount * 16 + 21000;
```
[SystemDictator.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#LL263C1-L264C1).
```solidity
          currentStep = 1;
          function step1() public onlyOwner step(1)
              function step2() public onlyOwner step(2) 
```
## Vulnerability Details 

# NC-03 Use `Double Quote` for string literals.
## Summary 
Best practice is to use double quotes for string literals rather than single literals.
## Vulnerability Details 
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/Proxy.sol#LL12C1-L25C8)
```solidity
     *         bytes32(uint256(keccak256('eip1967.proxy.admin')) - 1)
     *         bytes32(uint256(keccak256('eip1967.proxy.implementation')) - 1)
```


# NC-04 Use `Immutable` in place of `constants`
## Summary 
Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant while it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the `constructor`.
## Vulnerability Details 
[`SystemConfig.sol`](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL43C3-L43C100)
```solidity
  bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");
```