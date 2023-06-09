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

# 5: Large numeric literals should use underscores for readability

Vulnerability details

## Context:

Large numeric literals should use underscores for readability

## Proof of Concept

> ***File: OptimismPortal.sol***

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197

## Tools Used

Manual Analysis


# 6: Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

Vulnerability details

## Context:

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

## Proof of Concept

> ***File: SystemConfig.sol***

contract SystemConfig is OwnableUpgradeable, Semver 

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16

## Tools Used

Manual Analysis


# 7: Events are missing sender information

Vulnerability details

## Context:

When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the msg.sender the events of these types of action will make events much more useful to end users, especially when msg.sender is not tx.origin.

## Proof of Concept

        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L71

        emit ERC721BridgeInitiated(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L105

        emit ETHDepositInitiated(_from, _to, _amount, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L309

         emit ETHWithdrawalFinalized(_from, _to, _amount, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L325

        emit ERC20DepositInitiated(_localToken, _remoteToken, _from, _to, _amount, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L343

        emit ERC20WithdrawalFinalized(_localToken, _remoteToken, _from, _to, _amount, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L361

        emit OutputsDeleted(prevNextL2OutputIndex, _l2OutputIndex);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L166

        emit OutputProposed(_outputRoot, nextOutputIndex(), _l2BlockNumber, block.timestamp);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L220

        emit WithdrawalProven(withdrawalHash, _tx.sender, _tx.target);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L317

        emit WithdrawalFinalized(withdrawalHash, success);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L412

        emit TransactionDeposited(from, _to, DEPOSIT_VERSION, opaqueData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L482

        emit ConfigUpdate(VERSION, UpdateType.UNSAFE_BLOCK_SIGNER, data);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L184

        emit ConfigUpdate(VERSION, UpdateType.BATCHER, data);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L196

        emit ConfigUpdate(VERSION, UpdateType.GAS_CONFIG, data);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L210

        emit ConfigUpdate(VERSION, UpdateType.GAS_LIMIT, data);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L223

        emit OwnershipTransferred(oldOwner, _owner, _isLocal);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L44

        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L73

        emit ERC721BridgeInitiated(_localToken, remoteToken, _from, _to, _tokenId, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L124

        emit WithdrawerBalanceBurnt(balance);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L88

        emit WithdrawalInitiated(

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L207

        emit DepositFinalized(

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L230

        emit WithdrawalInitiated(_remoteToken, _localToken, _from, _to, _amount, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L255

        emit DepositFinalized(_remoteToken, _localToken, _from, _to, _amount, _extraData);

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L273

## Tools Used

Manual Analysis


