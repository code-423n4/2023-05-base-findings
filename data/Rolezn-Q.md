### Note: Copy the RAW markdown file into a viewer such as https://markdownlivepreview.com to allow page hyperlinking for easier browsing experience as Github's markdown viewer does not support page hyperlinking
### i.e. By pressing the link on the left side of the finding will jump you to the finding or back to the table of contents

## QA Summary<a name="QA Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Add to `blacklist` function | 9 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Avoid using `tx.origin` | 2 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Event is missing parameters | 3 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Possible rounding issue | 3 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing Contract-existence Checks Before Low-level Calls | 1 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Contracts are not using their OZ Upgradeable counterparts | 9 |
| [LOW&#x2011;7](#LOW&#x2011;7) | TransferOwnership Should Be Two Step | 3 |
| [LOW&#x2011;8](#LOW&#x2011;8) | Unbounded loop | 1 |
| [LOW&#x2011;9](#LOW&#x2011;9) | Unsafe downcast | 5 |
| [LOW&#x2011;10](#LOW&#x2011;10) | No Storage Gap For Upgradeable Contracts | 1 |
| [LOW&#x2011;11](#LOW&#x2011;11) | Upgrade OpenZeppelin Contract Dependency | 2 |
| [LOW&#x2011;12](#LOW&#x2011;12) | Use `Ownable2Step` rather than `Ownable` | 6 |
| [LOW&#x2011;13](#LOW&#x2011;13) | Use `safeTransferOwnership` instead of `transferOwnership` function | 3 |

Total: 48 contexts over 13 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Avoid Floating Pragmas: The Version Should Be Locked | 3 |
| [NC&#x2011;2](#NC&#x2011;2) | No need for `== true` or `== false` checks | 1 |
| [NC&#x2011;3](#NC&#x2011;3) | Consider disabling `renounceOwnership()` | 8 |
| [NC&#x2011;4](#NC&#x2011;4) | Consider using named mappings | 3 |
| [NC&#x2011;5](#NC&#x2011;5) | Constants Should Be Defined Rather Than Using Magic Numbers | 6 |
| [NC&#x2011;6](#NC&#x2011;6) | Contract does not follow the Solidity style guide's suggested layout ordering | 2 |
| [NC&#x2011;7](#NC&#x2011;7) | Critical Changes Should Use Two-step Procedure | 6 |
| [NC&#x2011;8](#NC&#x2011;8) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 2 |
| [NC&#x2011;9](#NC&#x2011;9) | `block.timestamp` is already used when emitting events, no need to input timestamp | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Event emit should emit a parameter | 1 |
| [NC&#x2011;11](#NC&#x2011;11) | Function writing that does not comply with the Solidity Style Guide  | 2 |
| [NC&#x2011;12](#NC&#x2011;12) | Large or complicated code bases should implement fuzzing tests | 1 |
| [NC&#x2011;13](#NC&#x2011;13) | Use `delete` to Clear Variables | 1 |
| [NC&#x2011;14](#NC&#x2011;14) | Imports can be grouped together | 15 |
| [NC&#x2011;15](#NC&#x2011;15) | No need to initialize uints to zero | 2 |
| [NC&#x2011;16](#NC&#x2011;16) | Initial value check is missing in Set Functions | 5 |
| [NC&#x2011;17](#NC&#x2011;17) | Missing event for critical parameter change | 1 |
| [NC&#x2011;18](#NC&#x2011;18) | NatSpec comments should be increased in contracts | 1 |
| [NC&#x2011;19](#NC&#x2011;19) | NatSpec `@param` is missing | 2 |
| [NC&#x2011;20](#NC&#x2011;20) | NatSpec `@return` argument is missing | 1 |
| [NC&#x2011;21](#NC&#x2011;21) | Use a more recent version of Solidity | 19 |
| [NC&#x2011;22](#NC&#x2011;22) | Omissions in Events | 3 |
| [NC&#x2011;23](#NC&#x2011;23) | Public Functions Not Called By The Contract Should Be Declared External Instead | 3 |
| [NC&#x2011;24](#NC&#x2011;24) | Empty blocks should be removed or emit something | 10 |
| [NC&#x2011;25](#NC&#x2011;25) | Use `bytes.concat()` | 1 |

Total: 100 contexts over 25 issues

## Low Risk Issues

### <a href="#QA Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Add to `blacklist` function

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```


#### <ins>Recommended Mitigation Steps</ins>
Add to Blacklist function and modifier.



### <a href="#QA Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Avoid using `tx.origin`

`tx.origin` is a global variable in Solidity that returns the address of the account that sent the transaction.

Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.

This can make it easier to create a vault on behalf of another user with an external administrator (by receiving it as an argument).

#### <ins>Proof Of Concept</ins>

<details>

```solidity
417: if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L417

```solidity
465: if (msg.sender != tx.origin) {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L465



</details>




### <a href="#QA Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Event is missing parameters

The following functions are missing critical parameters when emitting an event.
When dealing with source address which uses the value of `msg.sender`, the `msg.sender` value must be specified in every transaction, a contract or web page listening to events cannot react to users, emit does not serve the purpose. Basically, this event cannot be used.

#### <ins>Proof Of Concept</ins>


<details>

```solidity

function deleteL2Outputs(uint256 _l2OutputIndex) external {
        require(
            msg.sender == CHALLENGER,
            "L2OutputOracle: only the challenger address can delete outputs"
        );

        
        require(
            _l2OutputIndex < l2Outputs.length,
            "L2OutputOracle: cannot delete outputs after the latest output index"
        );

        
        require(
            block.timestamp - l2Outputs[_l2OutputIndex].timestamp < FINALIZATION_PERIOD_SECONDS,
            "L2OutputOracle: cannot delete outputs that have already been finalized"
        );

        uint256 prevNextL2OutputIndex = nextOutputIndex();

        
        assembly {
            sstore(l2Outputs.slot, _l2OutputIndex)
        }

        emit OutputsDeleted(prevNextL2OutputIndex, _l2OutputIndex);
    }

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L166

```solidity

function proposeL2Output(
        bytes32 _outputRoot,
        uint256 _l2BlockNumber,
        bytes32 _l1BlockHash,
        uint256 _l1BlockNumber
    ) external payable {
        require(
            msg.sender == PROPOSER,
            "L2OutputOracle: only the proposer address can propose new outputs"
        );

        require(
            _l2BlockNumber == nextBlockNumber(),
            "L2OutputOracle: block number must be equal to next expected block number"
        );

        require(
            computeL2Timestamp(_l2BlockNumber) < block.timestamp,
            "L2OutputOracle: cannot propose L2 output in the future"
        );

        require(
            _outputRoot != bytes32(0),
            "L2OutputOracle: L2 output proposal cannot be the zero hash"
        );

        if (_l1BlockHash != bytes32(0)) {
            
            
            
            
            
            
            
            
            require(
                blockhash(_l1BlockNumber) == _l1BlockHash,
                "L2OutputOracle: block hash does not match the hash at the expected height"
            );
        }

        emit OutputProposed(_outputRoot, nextOutputIndex(), _l2BlockNumber, block.timestamp);

        l2Outputs.push(
            Types.OutputProposal({
                outputRoot: _outputRoot,
                timestamp: uint128(block.timestamp),
                l2BlockNumber: uint128(_l2BlockNumber)
            })
        );
    }

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L220

```solidity

function depositTransaction(
        address _to,
        uint256 _value,
        uint64 _gasLimit,
        bool _isCreation,
        bytes memory _data
    ) public payable metered(_gasLimit) {
        
        
        if (_isCreation) {
            require(
                _to == address(0),
                "OptimismPortal: must send to address(0) when creating a contract"
            );
        }

        
        
        require(
            _gasLimit >= minimumGasLimit(uint64(_data.length)),
            "OptimismPortal: gas limit too small"
        );

        
        
        
        
        require(_data.length <= 120_000, "OptimismPortal: data too large");

        
        address from = msg.sender;
        if (msg.sender != tx.origin) {
            from = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }

        
        
        
        bytes memory opaqueData = abi.encodePacked(
            msg.value,
            _value,
            _gasLimit,
            _isCreation,
            _data
        );

        
        
        emit TransactionDeposited(from, _to, DEPOSIT_VERSION, opaqueData);
    }

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L482



</details>


#### <ins>Recommended Mitigation Steps</ins>
Add `msg.sender` parameter in event-emit



### <a href="#QA Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Possible rounding issue

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

#### <ins>Proof Of Concept</ins>


<details>


```solidity
97: int256 targetResourceLimit = int256(uint256(config.maxResourceLimit)) /
            int256(uint256(config.elasticityMultiplier));
155: uint256 gasCost = resourceCost / Math.max(block.basefee, 1 gwei);

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L97

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L155



```solidity
48: uint256 scaled = unscaled / divisor;

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48



</details>




### <a href="#QA Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


<details>

```solidity
405: bool success = SafeCall.callWithMinGas(_tx.target, _tx.gasLimit, _tx.value, _tx.data);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L405



</details>


#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#QA Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

<details>

```solidity
5: import { IERC721 } from "@openzeppelin/contracts/token/ERC721/IERC721.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L5

```solidity
4: import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L4

```solidity
4: import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L4

```solidity
4: import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
5: import { Math } from "@openzeppelin/contracts/utils/math/Math.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L4-L5

```solidity
4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4

```solidity
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6

```solidity
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6

```solidity
5: import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L5



</details>

#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts





### <a href="#QA Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> TransferOwnership Should Be Two Step

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
135: function initialize: transferOwnership(_owner);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L135

```solidity
37: function transferOwnership: function transferOwnership(address _owner, bool _isLocal) external onlyOwner {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37

```solidity
41: function transferOwnership: _transferOwnership(_owner);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L41



</details>

#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#QA Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Unbounded loop

New items are pushed into the following arrays but there is no option to `pop` them out. Currently, the array can grow indefinitely. E.g. there's no maximum limit and there's no functionality to remove array values.
If the array grows too large, calling relevant functions might run out of gas and revert. Calling these functions could result in a DOS condition.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
222: l2Outputs.push(

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L222



</details>

#### <ins>Recommended Mitigation Steps</ins>
Add a functionality to delete array values or add a maximum size limit for arrays.



### <a href="#QA Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Unsafe Downcast
When a type is downcast to a smaller type, the higher order bits are truncated, effectively applying a modulo to the original value. Without any other checks, this wrapping will lead to unexpected behavior and bugs

#### <ins>Proof Of Concept</ins>


<details>

```solidity
225: timestamp: uint128(block.timestamp),
226: l2BlockNumber: uint128(_l2BlockNumber)

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L225-L226

```solidity
312: timestamp: uint128(block.timestamp),

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L312

```solidity
137: params.prevBlockNum = uint64(block.number);
183: prevBlockNum: uint64(block.number)

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L137

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L183





</details>




### <a href="#QA Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> No storage gap for upgradeable contract might lead to storage slot collision

For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.

Refer to the bottom part of this article: https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

#### <ins>Proof Of Concept</ins>

However, the contract doesn't contain a storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

<details>

```solidity
SystemConfig.sol
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L5



</details>

#### <ins>Recommended Mitigation Steps</ins>
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
```solidity
    uint256[50] private __gap;
```



### <a href="#QA Summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).
Release: https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0

#### <ins>Proof Of Concept</ins>

Require dependencies to be at least version of 4.9.0 to include critical vulnerability patches.

<details>

```solidity
    "@openzeppelin/contracts": "4.7.3"
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/../package.json#L57

```solidity
    "@openzeppelin/contracts-upgradeable": "4.7.3"
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/../package.json#L58



</details>

#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.9.0 via `>=4.9.0`



### <a href="#QA Summary">[LOW&#x2011;12]</a><a name="LOW&#x2011;12"> Use `Ownable2Step` rather than `Ownable`

`Ownable2Step` and `Ownable2StepUpgradeable` prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
5: OwnableUpgradeable
6: OwnableUpgradeable
16: OwnableUpgradeable
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L5

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L6

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16


```solidity
4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4


```solidity
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6


```solidity
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6



</details>




### <a href="#QA Summary">[LOW&#x2011;13]</a><a name="LOW&#x2011;13"> Use `safeTransferOwnership` instead of `transferOwnership` function

Use `safeTransferOwnership` which is safer. Use it as it is more secure due to 2-stage ownership transfer.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4

```solidity
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6

```solidity
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6



</details>

#### <ins>Recommended Mitigation Steps</ins>

Use <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol">Ownable2Step.sol</a>

```solidity
    function acceptOwnership() external {
        address sender = _msgSender();
        require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
        _transferOwnership(sender);
    }
}
```





## Non Critical Issues

### <a href="#QA Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [CrossDomainOwnable.sol]
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [CrossDomainOwnable2.sol]
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [CrossDomainOwnable3.sol]
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2



</details>




### <a href="#QA Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> No need for `== true` or `== false` checks

There is no need to verify that `== true` or `== false` when the variable checked upon is a boolean as well.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
417: if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L417



</details>


#### <ins>Recommended Mitigation Steps</ins>

Instead simply check for `variable` or `!variable`



### <a href="#QA Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Consider disabling `renounceOwnership()`

If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's `Ownable`'s `renounceOwnership()` function in order to disable it.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
5: OwnableUpgradeable
6: OwnableUpgradeable
16: OwnableUpgradeable
16: contract SystemConfig is OwnableUpgradeable, Semver {

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L5

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L6

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16



```solidity
14: abstract contract CrossDomainOwnable is Ownable {

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L14

```solidity
15: abstract contract CrossDomainOwnable2 is Ownable {

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L15

```solidity
15: abstract contract CrossDomainOwnable3 is Ownable {
18: *         it uses the standard Ownable _checkOwner function.

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L15

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L18





</details>



### <a href="#QA Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Consider using named mappings

Consider moving to solidity version 0.8.18 or later, and using <a href='https://ethereum.stackexchange.com/a/145555'>named mappings</a> to make it easier to understand the purpose of each mapping

#### <ins>Proof Of Concept</ins>

<details>

```solidity
72: mapping(bytes32 => bool) public finalizedWithdrawals;
77: mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L77



```solidity
32: mapping(bytes32 => bool) public sentMessages;

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L32



</details>



### <a href="#QA Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Constants Should Be Defined Rather Than Using Magic Numbers

Even <a href="https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39">assembly</a> can benefit from using readable constants instead of hex/numeric literals

#### <ins>Proof Of Concept</ins>

<details>

```solidity
461: require(_data.length <= 120_000, "OptimismPortal: data too large");
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L461

```solidity
119: if (blockDiff > 1) {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L119

```solidity
274: _config.baseFeeMaxChangeDenominator > 1,
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L274

```solidity
122: total += 4;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L122

```solidity
124: total += 16;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L124

```solidity
128: return unsigned + (68 * 16);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L128



</details>



### <a href="#QA Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Contract does not follow the Solidity style guide's suggested layout ordering

The <a href="https://docs.soliditylang.org/en/v0.8.20/style-guide.html#order-of-layout">style guide</a> says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Errors 5) Modifiers, and 6) Functions, but the contract(s) below do not follow this ordering

#### <ins>Proof Of Concept</ins>

<details>

```solidity
OptimismPortal.sol
```



```solidity
GasPriceOracle.sol
```





</details>



### <a href="#QA Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

<details>

```solidity
180: function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180

```solidity
192: function setBatcherHash(bytes32 _batcherHash) external onlyOwner {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192

```solidity
205: function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205

```solidity
218: function setGasLimit(uint64 _gasLimit) external onlyOwner {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218

```solidity
256: function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L256

```solidity
79: function setL1BlockValues(
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79



</details>

#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



#### <a href="#QA Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

<details>

```solidity
142: require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");
219: require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L142

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L219





</details>




### <a href="#QA Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> block.timestamp is already used when emitting events, no need to input timestamp

#### <ins>Proof Of Concept</ins>

<details>

```solidity
220: emit OutputProposed(_outputRoot, nextOutputIndex(), _l2BlockNumber, block.timestamp);

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L220



</details>




### <a href="#QA Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Event emit should emit a parameter

Some emitted events do not have any emitted parameters. It is recommended to add some parameter such as state changes or value changes when events are emitted

#### <ins>Proof Of Concept</ins>


<details>

```solidity
220: emit OutputProposed(_outputRoot, nextOutputIndex()

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L220



</details>




### <a href="#QA Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

<details>

```solidity
OptimismPortal.sol
```



```solidity
SystemConfig.sol
```





</details>




### <a href="#QA Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Large or complicated code bases should implement fuzzing tests

Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement <a href="https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05">fuzzing tests</a>. Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

#### <ins>Proof Of Concept</ins>

Various in-scope contract files.




### <a href="#QA Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
136: params.prevBoughtGas = 0;

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L136



</details>



### <a href="#QA Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Imports can be grouped together

Consider importing OZ first, then all interfaces, then all utils.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
4: import { ERC721Bridge } from "../universal/ERC721Bridge.sol";
5: import { IERC721 } from "@openzeppelin/contracts/token/ERC721/IERC721.sol";
6: import { L2ERC721Bridge } from "../L2/L2ERC721Bridge.sol";
7: import { Semver } from "../universal/Semver.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L4-L7

```solidity
4: import { Predeploys } from "../libraries/Predeploys.sol";
5: import { L2CrossDomainMessenger } from "./L2CrossDomainMessenger.sol";
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L4-L6

```solidity
4: import { Predeploys } from "../libraries/Predeploys.sol";
5: import { L2CrossDomainMessenger } from "./L2CrossDomainMessenger.sol";
6: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L4-L6

```solidity
4: import { ERC721Bridge } from "../universal/ERC721Bridge.sol";
5: import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";
6: import { L1ERC721Bridge } from "../L1/L1ERC721Bridge.sol";
7: import { IOptimismMintableERC721 } from "../universal/IOptimismMintableERC721.sol";
8: import { Semver } from "../universal/Semver.sol";

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L4-L8



</details>





### <a href="#QA Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

<details>

```solidity
269: uint256 lo = 0;

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L269

```solidity
118: uint256 total = 0;

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L118



</details>



### <a href="#QA Summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Initial value check is missing in Set Functions

A check regarding whether the current value and the new value are the same should be added

#### <ins>Proof Of Concept</ins>

<details>

```solidity
180: function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {
        _setUnsafeBlockSigner(_unsafeBlockSigner);

        bytes memory data = abi.encode(_unsafeBlockSigner);
        emit ConfigUpdate(VERSION, UpdateType.UNSAFE_BLOCK_SIGNER, data);
    }
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L180

```solidity
192: function setBatcherHash(bytes32 _batcherHash) external onlyOwner {
        batcherHash = _batcherHash;

        bytes memory data = abi.encode(_batcherHash);
        emit ConfigUpdate(VERSION, UpdateType.BATCHER, data);
    }
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L192

```solidity
205: function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {
        overhead = _overhead;
        scalar = _scalar;

        bytes memory data = abi.encode(_overhead, _scalar);
        emit ConfigUpdate(VERSION, UpdateType.GAS_CONFIG, data);
    }
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205

```solidity
218: function setGasLimit(uint64 _gasLimit) external onlyOwner {
        require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");
        gasLimit = _gasLimit;

        bytes memory data = abi.encode(_gasLimit);
        emit ConfigUpdate(VERSION, UpdateType.GAS_LIMIT, data);
    }
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L218

```solidity
256: function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {
        _setResourceConfig(_config);
    }
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L256



</details>


### <a href="#QA Summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
79: function setL1BlockValues(
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79



</details>



### <a href="#QA Summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> NatSpec comments should be increased in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#QA Summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> NatSpec `@param` is missing

#### <ins>Proof Of Concept</ins>

<details>

```solidity
    function initialize(bool _paused) public initializer {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L165

```solidity
189: /**
     * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
     *         linearly increases based on the size of the calldata. This is to prevent
     *         users from creating L2 resource usage without paying for it. This function
     *         can be used when interacting with the portal to ensure forwards compatibility.
     *
     */
    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L189



</details>



### <a href="#QA Summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> NatSpec `@return` argument is missing

#### <ins>Proof Of Concept</ins>

<details>

```solidity
189: /**
     * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
     *         linearly increases based on the size of the calldata. This is to prevent
     *         users from creating L2 resource usage without paying for it. This function
     *         can be used when interacting with the portal to ensure forwards compatibility.
     *
     */
    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L189



</details>



### <a href="#QA Summary">[NC&#x2011;21]</a><a name="NC&#x2011;21"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

<a href="https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/">0.8.19</a>:
SMTChecker: New trusted mode that assumes that any compile-time available code is the actual used code, even in external calls. 
Bug Fixes: 
- Assembler: Avoid duplicating subassembly bytecode where possible.
- Code Generator: Avoid including references to the deployed label of referenced functions if they are called right away.
- ContractLevelChecker: Properly distinguish the case of missing base constructor arguments from having an unimplemented base function.
- SMTChecker: Fix internal error caused by unhandled z3 expressions that come from the solver when bitwise operators are used.
- SMTChecker: Fix internal error when using the custom NatSpec annotation to abstract free functions.
- TypeChecker: Also allow external library functions in using for.

<a href="https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/">0.8.20</a>:
- Assembler: Use push0 for placing 0 on the stack for EVM versions starting from “Shanghai”. This decreases the deployment and runtime costs.
- EVM: Set default EVM version to “Shanghai”.
- EVM: Support for the EVM Version “Shanghai”.
- Optimizer: Re-implement simplified version of UnusedAssignEliminator and UnusedStoreEliminator. It can correctly remove some unused assignments in deeply nested loops that were ignored by the old version.
- Parser: Unary plus is no longer recognized as a unary operator in the AST and triggers an error at the parsing stage (rather than later during the analysis).
- SMTChecker: Group all messages about unsupported language features in a single warning. The CLI option --model-checker-show-unsupported and the JSON option settings.modelChecker.showUnsupported can be enabled to show the full list.
- SMTChecker: Properties that are proved safe are now reported explicitly at the end of analysis. By default, only the number of safe properties is shown. The CLI option --model-checker-show-proved-safe and the JSON option settings.modelChecker.showProvedSafe can be enabled to show the full list of safe properties.
- Standard JSON Interface: Add experimental support for importing ASTs via Standard JSON.
- Yul EVM Code Transform: If available, use push0 instead of codesize to produce an arbitrary value on stack in order to create equal stack heights between branches.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/BaseFeeVault.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L2

```solidity
pragma solidity 0.8.15;
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L2



</details>

#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#QA Summary">[NC&#x2011;22]</a><a name="NC&#x2011;22"> Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters
The following events should also add the previous original value in addition to the new value.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
20: event OverheadUpdated(uint256 overhead);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L20

```solidity
21: event ScalarUpdated(uint256 scalar);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L21

```solidity
22: event DecimalsUpdated(uint256 decimals);
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L22



</details>


#### <ins>Recommended Mitigation Steps</ins>
The events should include the new value and old value where possible.



### <a href="#QA Summary">[NC&#x2011;23]</a><a name="NC&#x2011;23"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
function gasPrice() public view returns (uint256) {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L57

```solidity
function baseFee() public view returns (uint256) {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L66

```solidity
function l1FeeWallet() public view returns (address) {
```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L28



</details>



### <a href="#QA Summary">[NC&#x2011;24]</a><a name="NC&#x2011;24"> Empty blocks should be removed or emit something

#### <ins>Proof Of Concept</ins>

<details>

```solidity
31: {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L31

```solidity
101: {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L101

```solidity
19: constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/BaseFeeVault.sol#L19

```solidity
33: constructor() Semver(1, 0, 0) {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L33

```solidity
65: constructor() Semver(1, 0, 0) {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L65

```solidity
19: constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L19

```solidity
31: {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L31

```solidity
69: {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L69

```solidity
70: constructor() Semver(1, 0, 0) {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L70

```solidity
20: constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L20



</details>




### <a href="#QA Summary">[NC&#x2011;25]</a><a name="NC&#x2011;25"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


<details>

```solidity
472: bytes memory opaqueData = abi.encodePacked(
            msg.value,
            _value,
            _gasLimit,
            _isCreation,
            _data
        )

```

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L472



</details>


#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



