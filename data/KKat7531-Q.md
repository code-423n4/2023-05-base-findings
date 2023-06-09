# Findings Summary

| ID     | Title                                                                              | Severity      |
| ------ | ---------------------------------------------------------------------------------- | ------------- |
| [L-01] | Missing checks for address(0x0) when assigning values to address state variables   | Low     |
| [L-02] | Keccak Constant values should used to immutable rather than constant               | Low     |
| [L-03] | Use Ownable2Step rather than Ownable.                                              | Low     |
| [L-04] | Use safeTransferFrom Instead of transferFrom for ERC721                            | Low  |
| [N-01] | Consider using named mappings                                                      | Non-critical |
| [N-02] | Use abi.encodeCall() instead of abi.encodeSignature()/abi.encodeSelector()     | Non-critical|
| [N-03] | Consider using delete rather than assigning zero/false to clear values                                                              | Non-critical |
| [N-04] |   Floating pragma                                                          | Non-critical |
| [N-05] |Use a more recent version of Solidity                                                     | Non-critical |

# [ L-01 ] Missing checks for address(0x0) when assigning values to address state variables

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#LL107C29-L107C29
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L108
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L157 

# [ L-02 ] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be
used in their appropriate contexts.
While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s
still best to use the right tool for the task at hand.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

# [ L-03 ] Use Ownable2Step rather than Ownable

Ownable2Step and Ownable2StepUpgradeable prevent the contract ownership
from mistakenly being transferred to an address that cannot handle it (e.g. due to
a typo in the address), by requiring that the recipient of the owner permissions
actively accept via a contract call of its own.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L14

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L15

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L15 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L6

# [ L-04 ]Use safeTransferFrom Instead of transferFrom for ERC721 

Use of transferFrom method for ERC721 transfer is discouraged and recommended to use
safeTransferFrom whenever possible by OpenZeppelin

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L68


# [ N-01 ] Consider using named mappings 

Consider moving to solidity version 0.8.18 or later, and using named mappings to make
it easier to understand the purpose of each mapping

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L20

# [ N-02 ] Use abi.encodeCall() instead of abi.encodeSignature()/abi.encodeSelector()

abi.encodeCall() has compiler type safety, whereas the other two functions do not 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L89 

# [ N-03 ] Consider using delete rather than assigning zero/false to clear values

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L136 

# [ N-04 ] Floating pragma 
Contracts should be deployed with the same compiler version and flags that they
have been tested with thoroughly. Locking the pragma helps to ensure that
contracts do not accidentally get deployed using, for example, an outdated
compiler version that might introduce bugs that affect the contract system
negatively.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2 

# [ N-05 ] Use a more recent version of Solidity 
More recent version can be used in order to gain some optimizations and new features for the smart contracts

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2 
