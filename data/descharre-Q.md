# Overview
Overall, the protocol is extremely well designed. Apart from one low risk finding, all issues I found are non critical. The documentation online as well as the comments in the contracts are excellent and makes it a lot easier to understand the complex code. I was really impressed that for most contracts, the solidity style guide was followed perfectly.
# Summary
## Low Risk
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|L-01       |Missing checks for 0 address when assigning to immutable state variables|-|


## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|N-01       | Use a more recent version of solidity| - |
|N-02       | Use a constant instead of a number calculated with a constant|1 |
|N-03       | Consider using named mappings| - |
|N-04       | Don't compare boolean expressions to boolean literals| 4 |
|N-05       | Modifier can be used when using duplicate require| 1 |
|N-06       | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant| 1 |
|N-07       | Duplicate functions| 1 |
|N-08       | Variables don't need to be initialized to zero| 1 |
|N-09       | Order of Functions do not follow the solidity style guide| 3 |
|N-10       | Missing events for storage variable change| 1 |

# Details
## Low Risk
## [L-01] Missing checks for 0 address when assigning to immutable state variables
All constructors are missing 0 checks. When the state variable is immutable, it can lead to redeployment of the contract.

## Non critical
## [N-01] Use a more recent version of solidity
Most contracts use solidity version 0.8.15.
Using old versions of Solidity prevents benefits of bug fixes and newer security checks. Use at least solidity 0.8.18 or 0.8.19 to get all the latest features and bug fixes.


## [N-02] Use a constant instead of a number calculated with a constant
When a number that is always the same is calculated with a constant, the calculation should be a variable in the form of a constant or immutable. 

[`uint256 divisor = 10**DECIMALS;`](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L46) should become a state variable like `uint256 public constant DIVISOR = 1000000;` or `uint256 public immutable DIVISOR = 10**DECIMALS;`

## [N-03] Consider using named mappings
Since solidity version 0.8.18 it is possible to use named parameters in mappings. Using named mapping will increase readability and makes it easier to understand what the mapping is used for.

[OptimismPortal.sol#L72](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72)
```solidity
    mapping(bytes32 => bool) public finalizedWithdrawals;
```
Can be changed into
```solidity
    mapping(bytes32 withdrawal hash => bool isFinalized) public finalizedWithdrawals;
```

## [N-04] Don't compare boolean expressions to boolean literals
A boolean shouldn't be compared to a literal, this has the same result but it is redundant.

[`require(paused == false, "OptimismPortal: paused");`](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L138) should be `require(!paused, "OptimismPortal: paused");`

This also occures at:
- [L1ERC721Bridge.sol#L58](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L58)
- [OptimismPortal.sol#L388](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L388)
- [OptimismPortal.sol#L417](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L417)
## [N-05] Modifier can be used when using duplicate require
When a require statement checks input paremeters and is used in multiple places, it can be replaced by a modifier.
The 2 require statements in [`pause()`](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L175) and [`unpause()`]() both check if msg.sender is GUARDIAN. This is redundant code so a modifier should be used. Even though the error messages are both different, it can be perfectly done in one modifier. This reduces code and increases readability.

```diff
+   modifier onlyGuardian() {
+       require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can do pause actions");
+       _;
+   }
  
L174: 
-   function pause() external {
+   function pause() external onlyGuardian {
-       require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can pause");
        paused = true;
        emit Paused(msg.sender);
    }  
```
## [N-06] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant
 There is a difference between constant variables and immutable variables in solidity. Constants should be used for literal values that are written into the code, immutable variables should be used for expressions, or parameters passed into the constructor.
- [SystemConfig.sol#L43](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43)

## [N-07] Duplicate functions
[`gasPrice() and baseFee()`](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L57-L68) have a different name but they do exactly the same.
```solidity
    function gasPrice() public view returns (uint256) {
        return block.basefee;
    }

    /**
     * @notice Retrieves the current base fee.
     *
     * @return Current L2 base fee.
     */
    function baseFee() public view returns (uint256) {
        return block.basefee;
    }
```
## [N-08] Variables don't need to be initialized to zero
It's useless to initialize a variable to it's default value or zero.
- [L2OutputOracle.sol#L269](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L269)

## [N-09] Order of Functions do not follow the solidity style guide
According to the solidity [style guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#order-of-functions) public functions should be placed after external functions. Which is not always the case in the protocol. In the [L2OutputOracle](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L291-L297) there is an external function placed after a public function.

The OptimismPortal and SystemConfig contracts do not follow the style guide at all. Everything is just mixed here.

## [N-10] Missing events for storage variable change
[_resourceConfig](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L71) is used to calculate the minimum gas limit. When _resourceConfig is [set](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L266-L297) there is no event emitted. An important storage variable change should always have an event associated with it.
