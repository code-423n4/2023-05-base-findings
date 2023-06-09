# 1: LOCK PRAGMAS TO SPECIFIC COMPILER VERSION

Vulnerability details

## Context:

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with L2 contracts. Otherwise, the developer would need to manually update the pragma in order to compile locally.

For reference, see https://swcregistry.io/docs/SWC-103


## Proof of Concept

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler versions. 

For reference, see https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

# 2: Variables need not be initialized to zero

Vulnerability details

## Context:

The default value for variables is zero, so initializing them to zero is superfluous.

There are 2 instances of this issue:

    uint256 public constant VERSION = 0;

    uint256 internal constant DEPOSIT_VERSION = 0;

## Proof of Concept

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L36

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L40

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Mitigation goes here

# 3: IMPORTS CAN BE GROUPED TOGETHER

Vulnerability details

## Context:

Imports can be grouped together.

## Proof of Concept

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L4-L14

## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Consider importing OZ first, then all interfaces, then all utils.


# 4: WORD & TYPING TYPOS

Vulnerability details

## Context:

Word & typing typos

## Proof of Concept

__transfering__  can be changed to __transferring__  in the following comment.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L11

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L13

__be send__  can be changed to __be sent__  in the following comment.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L44

__mloads__  can be changed to __loads__  in the following comment.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L256


## Tools Used

Manual Analysis

