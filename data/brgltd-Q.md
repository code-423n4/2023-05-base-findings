| ID    | Title |
| -------- | ------- |
| 01 | Prevent duplicated output roots
| 02 | Prevent SUBMISSION_INTERVAL from being zero
| 03 | Add a check to prevent out of bounds L2 output indexes
| 04 | Validation for withdrawal transaction in `OptimismPortal.proveWithdrawalTransaction()`
| 05 | Outdated solidity version
| 06 | Outdated OpenZeppelin version
| 07 | Floating pragma
| 08 | Missing zero address checks on the constructor
| 09 | Emit events before external calls
| 10 | Avoid large functions
| 11 | Lack of CEI
| 12 | Use function modifier for validating input arguments
| 13 | Critical setter functions should use two step pattern
| 14 | Missing event for parameter changes
| 15 | Lack of old and new value for events in setter functions
| 16 | Check for stale values on setter functions
| 17 | Allow max length to be modifiable in RLPReader
| 18 | Move input arguments validation to the top of the function
| 19 | Declare each contract/interface on a separate filed.
| 20 | Replace conditionals with ternary 
| 21 | Convert internal function to private where possible
| 22 | Use scientific notation
| 23 | Convert formula to match comments
| 24 | Usage of require statements
| 25 | Use descriptive names for function parameter names
| 26 | Array cache length and prefix increment for loops
| 27 | Order of functions 
| 28 | Missing description for return variable on NATSPEC
| 29 | External library contracts should be imported at the top
| 30 | Boolean expressions can be checked directly
| 31 | Constants in comparisons should appear on the left side
| 32 | Assigning the default values
| 33 | Use delete instead of assigning zero

## [01] Prevent duplicated output roots

Consider preventing duplicated `outputRoots` in `L2OutputOracle.proposeL2Outputs()`. The impact of having duplicated roots could be inconsistent behavior when deleting/replacing roots. 

One possible solution would be to store the roots into an auxiliary set/mapping  and checking if the new item is not present in the set before populating the `l2Outputs` array.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L179-L218

## [02] Prevent SUBMISSION_INTERVAL from being zero

If SUBMISSION_INTERVAL gets set with the value zero, the proposer could submit identical `_l2BlockNumbers` in `L2OutputOracle.proposeL2Output()` since the function `nextBlockNumber()` won't behave as expected. 

Consider adding input validation when setting SUBMISSION_INTERVAL.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L105

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L179-L218

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L336-L338

## [03] Add a check to prevent out of bounds L2 output indexes

Consider adding a check in `L2OutputOracle.getL2Output()` when `_l2OutputIndex` is bigger or equal the length of the `l2Outputs` array. 

Accessing elements for non-existing indexes will panic disregardless, but adding a check helps prevent accessing invalid memory and provides better security and stability for the contract since this function will be called throughout all withdrawals. 

## [04] Validation for withdrawal transaction in `OptimismPortal.proveWithdrawalTransaction()`

Consider validating that `_tx.target` and `_tx.sender` are not address(0) to prevent inconsistent behavior when processing transactions payloads. If possible, consider also validating that `tx_value` is within an expected range.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L243

## [05] Outdated solidity version

Consider using the latest stable version of solidity (0.8.19) to ensure the compiler contains the latest security fixes.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L2

## [06] Outdated OpenZeppelin version

The project is currently using OpenZeppelin version 4.7.3. Consider updating to the latest stable version - v.4.9.1 to ensure the project is using the up-to-date contracts from OpenZeppelin.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/package.json#L57-L58

## [07] Floating pragma

Consider avoiding locking the pragma when possible.

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

## [08] Missing zero address checks on the constructor

Consider adding a validation for `_guardian` in `OptimismPortal`. If this variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L157

## [09] Emit events before external calls

Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L101-L105

## [10] Avoid large functions

Consider limiting the max number of lines per function. For example, NASA recommends not going beyond 60 lines per function (25th rule of NASA coding standard). [Reference](https://yurichev.com/mirrors/C/JPL_Coding_Standard_C.pdf).

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L242-L318

## [11] Lack of CEI

Wherever possible, consider making state updates before external calls to follow the checks-effects-interactions, even when the external calls are made to trusted contracts.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L278

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L310-L314

## [12] Use function modifier for validating input arguments

Consider converting manual validation with a function modifier where possible to improve code composability and abstraction.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L142-L145

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L185-L188

## [13] Critical setter functions should use two step pattern

Lack of two-step procedure for critical operations leaves them error-prone.

Consider adding a two-steps pattern on critical changes to avoid mistakenly transferring ownership of roles or critical functionalities to the wrong address.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218-L224

## [14] Missing event for parameter changes

Adding events will facilitate offchain monitoring.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L256-L258

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L266-L297

## [15] Lack of old and new value for events in setter functions

Events that mark critical parameter changes should contain both the old and the new value.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218-L224

## [16] Check for stale values on setter functions

Add a check ensuring that the new value if different than the current value to avoid emitting unnecessary events.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192-L197

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218-L224

## [17] Allow max length to be modifiable in RLPReader

`RLPReader.sol` will be used by the `MerkleTrie.sol` which will be used by `OptimismPortal` to validate withdrawal proofs. Consider allowing the array `RLPItem[]` to have a modifiable max length. One improvement could modify the contract to dynamically resize the array based on the actual number of RLP items encountered in the input. This would ensure that all elements are properly stored without exceeding the array bounds.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/rlp/RLPReader.sol#L90

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/rlp/RLPReader.sol#L42

## [18] Move input arguments validation to the top of the function

Consider validation input arguments on the top of the function body. Converting to a function modifier where possible is also recommended. Validating arguments on the top of the function will consume less execution gas for the failed calls.

For example, for `L2OutputOracle.depositTransaction()`, it could be helpful to validate `_gasLimit` and `_data.length` before validating `_isCreation` since there will be more calls for non contract creation instead of contract creation.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L443-L461

## [19] Declare each contract/interface on a separate filed.

Consider avoiding multiple contracts/interfaces on the same file.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L17

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L33

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L115

## [20] Replace conditionals with ternary 

Consider refactoring conditionals with ternary expressions where possible. Ternary expressions will make the code more compact.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L463-L467

## [21] Convert internal function to private where possible

Internal functions not called by child contracts should be market private.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L504-L506

## [22] Use scientific notation

Consider converting values with 4+ zeros to use scientific notation to improve the readability of the codebase, e.g. 40000 => 40e3.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/SafeCall.sol#L96

## [23] Convert formula to match comments

Consider refactoring the following equation in `SafeCall.hasMinGas()`.

`lt(mul(gas(), 63), add(mul(_minGas, 64), mul(add(40000, _reservedGas), 63)))`

With

`lt(mul(gas(), 63), add(mul(_minGas, 64), mul(63, add(40000, _reservedGas))))` 

To match the comment. Alternatively consider switch the values in the comment, e.g. `gas × 63 ≥ minGas × 64 + (40_000 + reservedGas) × 63`.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/SafeCall.sol#L94-L97

## [24] Usage of require statements

Consider refactoring require statements to custom errors where possible. 

Custom errors are recommended when using the latest versions of solidity and using custom errors instead of require statements can also save gas.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L251-L254

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L261-L264

## [25] Use descriptive names for function parameter names

Consider declaring names correlated to the intended usage and avoid generic names when possible.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/rlp/RLPReader.sol#L51

## [26] Array cache length and prefix increment for loops

Consider caching the array length outside the loop and applying an unchecked prefix increment where possible.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/trie/MerkleTrie.sol#L104

## [27] Order of functions 

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows public above external.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L145-L173

## [28] Missing description for return variable on NATSPEC

Consider documenting all parameters and return values to improve the documentation present in the codebase.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L152

## [29] External library contracts should be imported at the top

External dependencies/libraries (e.g. OpenZeppelin) should be imported at the top. This is a common rule for linters on most programming languages.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L5

## [30] Boolean expressions can be checked directly

Consider refactoring from:

`require(deposits[_localToken][_remoteToken][_tokenId] == true)`

To:

`require(deposits[_localToken][_remoteToken][_tokenId] == true)`

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L58

## [31] Constants in comparisons should appear on the left side

Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html).

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L417

## [32] Assigning the default values

In some cases it might be beneficial to let the default value be computed instead of initializing with the default value.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L269

## [33] Use delete instead of assigning zero

Consider using the delete keyword when resetting an integer.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L136
