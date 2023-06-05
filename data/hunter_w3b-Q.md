## Summary

### Low Risk Issues List

| Number | Issues Details                                                                                        | Context |
| :----: | :---------------------------------------------------------------------------------------------------- | :-----: |
| [L-01] | `Event` is missing sender parameters                                                                  |    2    |
| [L-02] | Use `Ownable2Step's` transfer function rather than `Ownable's` for transfers of ownership             |    1    |
| [L-03] | Use `Ownable2Step` rather than `Ownable`                                                              |    4    |
| [L-04] | Use `safeTransferOwnership` instead of `transferOwnership` function                                   |    1    |
| [L-05] | Array `lengths` not checked                                                                           |    1    |
| [L-06] | Missing checks for `address(0x0)` when assigning values to address state variables                    |    7    |
| [L-07] | Contracts are not using their `OZ Upgradeable` counterparts                                           |    1    |
| [L-08] | Vulnerable version of `package` of OpenZeppelin is being used                                         |    1    |
| [L-09] | Emit statement is missing an `Event`                                                                  |    2    |
| [L-10] | `L2ERC721Bridge.sol.finalizeBridgeERC721()` there is no check on `_to` address that receive the token |    1    |
| [L-11] | Unsafe `downcast`                                                                                     |    3    |
| [L-12] | `Floating` pragma                                                                                     |    3    |
| [L-13] | The owner is a single point of failure and a `centralization risk`                                    |    6    |

### Non-Critical Issues List

| Number | Issues Details                                                                                               | Context |
| :----: | :----------------------------------------------------------------------------------------------------------- | :-----: |
| [N-01] | Variables need not be initialized to zero                                                                    |    6    |
| [N-02] | Use named parameters for `mapping` type declarations                                                         |    4    |
| [N-03] | `Non-external/public` variable and function names should begin with an underscore                            |    5    |
| [N-04] | Use abi.encodeCall() instead of abi.encodeSignature()/abi.encodeSelector()                                   |    2    |
| [N-05] | public functions not called by the contract should be declared `external` instead                            |    3    |
| [N-06] | `constants` should be defined rather than using magic numbers                                                |    4    |
| [N-07] | `Event` is not properly indexed                                                                              |    4    |
| [N-08] | `Non-library/interface` files should use fixed compiler versions, not floating ones                          |    3    |
| [N-09] | NatSpec `@param` is missing                                                                                  |   14    |
| [N-10] | NatSpec `@return` argument is missing                                                                        |    5    |
| [N-11] | Function ordering does not follow the `Solidity style guide`                                                 |    3    |
| [N-12] | Expressions for `constant` values such as a call to `keccak256()`, should use immutable rather than constant |    1    |
| [N-13] | Use scientific notation `(e.g. 1e18)` rather than exponentiation (e.g. `10**18`)                             |    1    |
| [N-14] | `Assembly` Codes Specific – Should Have Comments                                                             |    1    |

## [L-01] Event is missing sender parameters

The `L2OutputOracle.sol.L2OutputOracle.sol()` function is missing critical parameter `challenger` when emitting an event. When we delete all output proposal the value of msg.sender must be specified in every transaction, a contract or web page listening to events cannot react to users, Including the msg.sender the events of these types of action will make events much more useful to end users, especially when msg.sender is not tx.origin, and also know that this address delete the output of proposal is a challenger.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L166

```solidity
file: L1/L2OutputOracle.sol


166:         emit OutputsDeleted(prevNextL2OutputIndex, _l2OutputIndex);

```

```diff
diff --git a/deleteL2Outputs.sol.origi b/deleteL2Outputs.sol
index 7330a58..77700fb 100644
--- a/deleteL2Outputs.sol.origi
+++ b/deleteL2Outputs.sol
@@ -1,6 +1,6 @@

-    event OutputsDeleted(uint256 indexed prevNextOutputIndex, uint256 indexed newNextOutputIndex);
+    event OutputsDeleted(address indexed CHALLENGER, uint256 indexed prevNextOutputIndex, uint256 indexed newNextOutputIndex);

@@ -29,5 +29,5 @@
             sstore(l2Outputs.slot, _l2OutputIndex)
         }

-        emit OutputsDeleted(prevNextL2OutputIndex, _l2OutputIndex);
+        emit OutputsDeleted(msg.sender, prevNextL2OutputIndex, _l2OutputIndex);
     }
```

And also the `L2OutputOracle.sol.proposeL2Output.sol()` missing the `Proposer` in emitting an event.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L220

```solidity
file: L1/L2OutputOracle.sol


220:        emit OutputProposed(_outputRoot, nextOutputIndex(), _l2BlockNumber, block.timestamp);
```

## [L-02] Use `Ownable2Step's` transfer function rather than `Ownable's` for transfers of ownership

[`Ownable2Step`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable2Step.sol#L31-L56) and [`Ownable2StepUpgradeable`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/access/Ownable2StepUpgradeable.sol#L47-L63) prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L41

```solidity
File: L2/CrossDomainOwnable3.sol

37    function transferOwnership(address _owner, bool _isLocal) external onlyOwner {
38        require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");
39
40        address oldOwner = owner();
41:       _transferOwnership(_owner);
42        isLocal = _isLocal;
43
44        emit OwnershipTransferred(oldOwner, _owner, _isLocal);
    }

```

## [L-03] Use `Ownable2Step` rather than `Ownable`

[`Ownable2Step`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable2Step.sol#L31-L56) and [`Ownable2StepUpgradeable`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/access/Ownable2StepUpgradeable.sol#L47-L63) prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L14

```solidity
File: L2/CrossDomainOwnable.sol

14:     abstract contract CrossDomainOwnable is Ownable {

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L15

```solidity
File: L2/CrossDomainOwnable2.sol

15:    abstract contract CrossDomainOwnable2 is Ownable {

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L15

```solidity
File:  L2/CrossDomainOwnable3.sol


15:       abstract contract CrossDomainOwnable3 is Ownable {

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L16

```solidity
File:  L1/SystemConfig.sol


16:     contract SystemConfig is OwnableUpgradeable, Semver {
```

## [L-04] Use `safeTransferOwnership` instead of `transferOwnership` function

safeTransferOwnershipis more secure due to 2-stage ownership transfer.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L135

```solidity
File: L1/SystemConfig.sol

135:        transferOwnership(_owner);
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37

```solidity
File: L2/CrossDomainOwnable3.sol

37:     function transferOwnership(address _owner, bool _isLocal) external onlyOwner {

```

## [L-05] Array `lengths` not checked

If the length of the arrays are not required to be of the same length, user operations may not be fully executed

If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L242

```solidity
File: L1/OptimismPortal.sol

242    function proveWithdrawalTransaction(
243        Types.WithdrawalTransaction memory _tx,
244        uint256 _l2OutputIndex,
245        Types.OutputRootProof calldata _outputRootProof,
246:        bytes[] calldata _withdrawalProof
247    ) external whenNotPaused {
```

## [L-06] Missing checks for `address(0x0)` when assigning values to address state variables

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L31

```solidity
File: L1/L1CrossDomainMessenger.sol

27    constructor(OptimismPortal _portal)
28        Semver(1, 4, 0)
29        CrossDomainMessenger(Predeploys.L2_CROSS_DOMAIN_MESSENGER)
30    { /// @audit  no check assigning values to address state variables
31:        PORTAL = _portal;
32        initialize();
33    }
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L107

```solidity
File: L1/L2OutputOracle.sol

107        PROPOSER = _proposer;
108       CHALLENGER = _challenger;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L150

```solidity
File:  L1/OptimismPortal.sol


150    constructor(
151        L2OutputOracle _l2Oracle,
152        address _guardian,
153        bool _paused,
154        SystemConfig _config
155    ) Semver(1, 6, 0) {
156:        L2_ORACLE = _l2Oracle; /// @audit miss checks for address(0)
157 :       GUARDIAN = _guardian; /// @audit miss checks for address(0)
158        SYSTEM_CONFIG = _config;
159        initialize(_paused);
160    }
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL93C1-L111C6

```solidity
File: L1/SystemConfig.sol

93    constructor(
94        address _owner,
95        uint256 _overhead,
96        uint256 _scalar,
97        bytes32 _batcherHash,
98        uint64 _gasLimit,
99        address _unsafeBlockSigner,
100        ResourceMetering.ResourceConfig memory _config
101    ) Semver(1, 3, 0) {
102        initialize({
103:            _owner: _owner,/// @audit miss check
104            _overhead: _overhead,
105            _scalar: _scalar,
106            _batcherHash: _batcherHash,
107            _gasLimit: _gasLimit,
108:            _unsafeBlockSigner: _unsafeBlockSigner,  /// @audit miss check
109            _config: _config
110        });
111    }
```

## [L-07] Contracts are not using their `OZ Upgradeable` counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts. It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.
Where applicable, use the contracts from @openzeppelin/contracts-upgradeable instead of @openzeppelin/contracts. See `https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts` for list of available upgradeable contracts

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L4

```solidity
File: L1/L2OutputOracle.sol

4:   import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L4

```solidity
File: L1/OptimismPortal.sol

4:      import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L5

```solidity
File: L1/L1ERC721Bridge.sol

5:      import { IERC721 } from "@openzeppelin/contracts/token/ERC721/IERC721.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L4-L5

```solidity
File: L1/ResourceMetering.sol

4:      import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
5:      import { Math } from "@openzeppelin/contracts/utils/math/Math.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L4

```solidity
File: L2/CrossDomainOwnable.sol

4:      import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6

```solidity
File: L2/CrossDomainOwnable2.sol

6:    import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L6

```solidity
File: L2/CrossDomainOwnable3.sol

6:      import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L5

```solidity
File: L2/L2ERC721Bridge.sol

5:      import { ERC165Checker } from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";
```

## [L-08] Vulnerable version of `package` of OpenZeppelin is being used

This project's specific package versions are vulnerable to the specific CVEs listed below. Consider switching to more recent versions of these packages that don't have these vulnerabilities

https://github.com/ethereum-optimism/optimism/blob/develop/packages/contracts-bedrock/package.json

```javascript

57    "@openzeppelin/contracts": "4.7.3",
```

- [CVE-2023-26488](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-26488) - MEDIUM - OpenZeppelin Contracts is a library for secure smart contract development. The ERC721Consecutive contract designed for minting NFTs in batches does not update balances when a batch has size 1 and consists of a single token. Subsequent transfers from the receiver of that token may overflow the balance as reported by `balanceOf`. The issue exclusively presents with batches of size 1. The issue has been patched in 4.8.2. (@openzeppelin/contracts >=4.8.0 <4.8.2)
- [CVE-2023-30541](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-30541) - MEDIUM - OpenZeppelin Contracts is a library for secure smart contract development. A function in the implementation contract may be inaccessible if its selector clashes with one of the proxy's own selectors. Specifically, if the clashing function has a different signature with incompatible ABI encoding, the proxy could revert while attempting to decode the arguments from calldata. The probability of an accidental clash is negligible, but one could be caused deliberately and could cause a reduction in availability. The issue has been fixed in version 4.8.3. As a workaround if a function appears to be inaccessible for this reason, it may be possible to craft the calldata such that ABI decoding does not fail at the proxy and the function is properly proxied through. (@openzeppelin/contracts >=3.2.0 <4.8.3)
- [CVE-2023-30542](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-30542) - HIGH - OpenZeppelin Contracts is a library for secure smart contract development. The proposal creation entrypoint (`propose`) in `GovernorCompatibilityBravo` allows the creation of proposals with a `signatures` array shorter than the `calldatas` array. This causes the additional elements of the latter to be ignored, and if the proposal succeeds the corresponding actions would eventually execute without any calldata. The `ProposalCreated` event correctly represents what will eventually execute, but the proposal parameters as queried through `getActions` appear to respect the original intended calldata. This issue has been patched in 4.8.3. As a workaround, ensure that all proposals that pass through governance have equal length `signatures` and `calldatas` parameters. (@openzeppelin/contracts >=4.3.0 <4.8.3)

## [L-09] Emit statement is missing an `Event`

Events are an important way for to communicate with external applications, and emit statements are used to trigger those events. If an emit statement is missing an event, it means that the information that the event was supposed to communicate is not being communicated to external applications.

external applications not receiving important information about the contract's behavior, or not being able to respond appropriately to changes in the contract state.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol

```solidity
File: L1/L1ERC721Bridge.sol

71        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);

105        emit ERC721BridgeInitiated(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol

```solidity
File: L2/L2ERC721Bridge.sol

73        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);

124        emit ERC721BridgeInitiated(_localToken, remoteToken, _from, _to, _tokenId, _extraData);
```

## [L-10] `L2ERC721Bridge.sol.finalizeBridgeERC721()` there is no check on `_to` address that receive the token

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L70

```solidity
File: L2/L2ERC721Bridge.sol

/// @audit _to
70:         IOptimismMintableERC721(_localToken).safeMint(_to, _tokenId);
```

## [L‑11] Unsafe `downcast`

When a type is downcast to a smaller type, the higher order bits are truncated, effectively applying a modulo to the original value. Without any other checks, this wrapping will lead to unexpected behavior and bugs

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol

```solidity
File: L1/ResourceMetering.sol


135            params.prevBaseFee = uint128(uint256(newBaseFee));

137            params.prevBlockNum = uint64(block.number);

183            prevBlockNum: uint64(block.number)
```

## [L-12] `Floating` pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively. https://swcregistry.io/docs/SWC-103

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

```solidity
File: L2/CrossDomainOwnable.sol

2      pragma solidity ^0.8.0;
```

pragma solidity ^0.8.0;

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

```solidity
File: L2/CrossDomainOwnable2.sol

2      pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2

```solidity
File: L2/CrossDomainOwnable3.sol

2      pragma solidity ^0.8.0;
```

## [L-13] The owner is a single point of failure and a `centralization risk`

Having a single EOA as the only owner of contracts is a large centralization risk and a single point of failure. A single private key may be taken in a hack, or the sole holder of the key may become unable to retrieve the key when necessary. Consider changing to a multi-signature setup, or having a role-based authorization model.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L37

```solidity
File:  L2/CrossDomainOwnable3.sol

37    function transferOwnership(address _owner, bool _isLocal) external onlyOwner {
38        require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");
39
40        address oldOwner = owner();
41        _transferOwnership(_owner);
42        isLocal = _isLocal;
43
44        emit OwnershipTransferred(oldOwner, _owner, _isLocal);
45  }
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

```solidity
File: L1/SystemConfig.sol


180    function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {

192    function setBatcherHash(bytes32 _batcherHash) external onlyOwner {

205    function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {

218    function setGasLimit(uint64 _gasLimit) external onlyOwner {

256    function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {

```

## [N-01] Variables need not be initialized to zero

The default value for variables is zero, so initializing them to zero is superfluous.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L269

```solidity
File: L1/L2OutputOracle.sol

269        uint256 lo = 0;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L40

```solidity
File: L1/OptimismPortal.sol

40    uint256 internal constant DEPOSIT_VERSION = 0;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L136

```solidity
File: L1/ResourceMetering.sol

136            params.prevBoughtGas = 0;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L36

```solidity
File: L1/SystemConfig.sol

36    uint256 public constant VERSION = 0;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

```solidity
File: L2/GasPriceOracle.sol

118        uint256 total = 0;

120        for (uint256 i = 0; i < length; i++) {

```

## [N-02] Use named parameters for `mapping` type declarations

Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72

```solidity
File: L1/OptimismPortal.sol

72    mapping(bytes32 => bool) public finalizedWithdrawals;

77    mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L20

```solidity
File: L1/L1ERC721Bridge.sol

20    mapping(address => mapping(address => mapping(uint256 => bool))) public deposits;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L32

```solidity
File: L2/L2ToL1MessagePasser.sol

32    mapping(bytes32 => bool) public sentMessages;
```

## [N-03] `Non-external/public` variable and function names should begin with an underscore

According to the Solidity Style Guide, Non-external/public variable and function names should begin with an underscore

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

```solidity
File: L1/L2OutputOracle.sol

55    Types.OutputProposal[] internal l2Outputs;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

```solidity
File: L1/OptimismPortal.sol

40    uint256 internal constant DEPOSIT_VERSION = 0;

45    uint64 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

```solidity
File: L2/L2ToL1MessagePasser.sol

22    uint256 internal constant RECEIVE_DEFAULT_GAS_LIMIT = 100_000;

37    uint240 internal msgNonce;
```

## [N‑04] Use abi.encodeCall() instead of abi.encodeSignature()/abi.encodeSelector()

`abi.encodeCall()` has compiler [type safety](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693), whereas the other two functions do not

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L89

```solidity
File: L1/L1ERC721Bridge.sol

89        bytes memory message = abi.encodeWithSelector(
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L109

```solidity

109        bytes memory message = abi.encodeWithSelector(
```

## [N-05] public functions not called by the contract should be declared `external` instead

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L57

```solidity
File: L2/GasPriceOracle.sol

57     function gasPrice() public view returns (uint256) {

103    function decimals() public pure returns (uint256) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L28

```solidity
File: L2/SequencerFeeVault.sol

28    function l1FeeWallet() public view returns (address) {
```

## [N-06] `constants` should be defined rather than using magic numbers

Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197

```solidity
File: L1/OptimismPortal.sol

/// @audit  16    21000
197        return _byteCount * 16 + 21000;

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L128

```solidity
File: L2/GasPriceOracle.sol

/// @audit  68   16
128        return unsigned + (68 * 16);

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1FeeVault.sol#L19

```solidity
File: L2/L1FeeVault.sol

/// @audit  10 ether
19    constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/SequencerFeeVault.sol#L20

```solidity
File: L2/SequencerFeeVault.sol

/// @audit  10 ether
20    constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}

```

## [N-07] `Event` is not properly indexed

Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be indexed.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

```solidity
File: L1/OptimismPortal.sol

118    event WithdrawalFinalized(bytes32 indexed withdrawalHash, bool success);

125    event Paused(address account);

132    event Unpaused(address account);
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#LL50C1-L58C7

```solidity
File: L2/L2ToL1MessagePasser.sol

50    event MessagePassed(
51        uint256 indexed nonce,
52        address indexed sender,
53        address indexed target,
54        uint256 value,
55        uint256 gasLimit,
56        bytes data,
57        bytes32 withdrawalHash
58    );
```

## [N-08] `Non-library/interface` files should use fixed compiler versions, not floating ones

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2

```solidity
File: L2/CrossDomainOwnable.sol

2      pragma solidity ^0.8.0;
```

pragma solidity ^0.8.0;

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L2

```solidity
File: L2/CrossDomainOwnable2.sol

2      pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2

```solidity
File: L2/CrossDomainOwnable3.sol

2      pragma solidity ^0.8.0;
```

## [N-09] NatSpec `@param` is missing

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L45

```solidity
File: L1/L1CrossDomainMessenger.sol


/// @audit Missing: '@param _to'
/// @audit Missing: '_gasLimit'
/// @audit Missing: '_value'
/// @audit Missing: '_data'
42      /**
43         * @inheritdoc CrossDomainMessenger
44      */
45      function _sendMessage(
46           address _to,
47           uint64 _gasLimit,
48           uint256 _value,
49           bytes memory _data
50      ) internal override {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol

```solidity
File: L1/L1StandardBridge.sol

/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
297    /**
298     * @notice Emits the legacy ETHDepositInitiated event followed by the ETHBridgeInitiated event.
299     *         This is necessary for backwards compatibility with the legacy bridge.
300     *
301     * @inheritdoc StandardBridge
302     */
303    function _emitETHBridgeInitiated(
304        address _from,
305        address _to,
306        uint256 _amount,
307        bytes memory _extraData
308    ) internal override {



/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
313    /**
314     * @notice Emits the legacy ETHWithdrawalFinalized event followed by the ETHBridgeFinalized
315     *         event. This is necessary for backwards compatibility with the legacy bridge.
316     *
317     * @inheritdoc StandardBridge
318     */
319    function _emitETHBridgeFinalized(
320        address _from,
321        address _to,
322        uint256 _amount,
323        bytes memory _extraData
324    ) internal override {



/// @audit Missing: '_localToken'
/// @audit Missing: '_remoteToken'
/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
329    /**
330     * @notice Emits the legacy ERC20DepositInitiated event followed by the ERC20BridgeInitiated
331     *         event. This is necessary for backwards compatibility with the legacy bridge.
332     *
333     * @inheritdoc StandardBridge
334     */
335    function _emitERC20BridgeInitiated(
336        address _localToken,
337        address _remoteToken,
338        address _from,
339        address _to,
340        uint256 _amount,
341        bytes memory _extraData
342    ) internal override {



/// @audit Missing: '_localToken'
/// @audit Missing: '_remoteToken'
/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
347    /**
348     * @notice Emits the legacy ERC20WithdrawalFinalized event followed by the ERC20BridgeFinalized
349     *         event. This is necessary for backwards compatibility with the legacy bridge.
350     *
351     * @inheritdoc StandardBridge
352     */
353    function _emitERC20BridgeFinalized(
354        address _localToken,
355        address _remoteToken,
356        address _from,
357        address _to,
358        uint256 _amount,
359        bytes memory _extraData
360    ) internal override {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L196

```solidity
File: L1/OptimismPortal.sol

/// @audit Missing: '_byteCount'
189    /**
190     * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
191     *         linearly increases based on the size of the calldata. This is to prevent
192     *         users from creating L2 resource usage without paying for it. This function
193     *         can be used when interacting with the portal to ensure forwards compatibility.
194     *
195     */
196    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {
197       return _byteCount * 16 + 21000;
198    }
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L77

```solidity
File: L1/L1ERC721Bridge.sol

/// @audit Missing: '_localToken'
/// @audit Missing: '_remoteToken'
/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_tokenId'
/// @audit Missing: '_minGasLimit'
/// @audit Missing: '_extraData'
74    /**
75     * @inheritdoc ERC721Bridge
76     */
78    function _initiateBridgeERC721(
79        address _localToken,
80        address _remoteToken,
81        address _from,
82        address _to,
83        uint256 _tokenId,
84        uint32 _minGasLimit,
85        bytes calldata _extraData
86    ) internal override {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol

```solidity
File: L2/L2CrossDomainMessenger.sol

/// @audit Missing: '_to'
/// @audit Missing: '_gasLimit'
/// @audit Missing: '_value'
/// @audit Missing: '_data'
48    /**
49     * @inheritdoc CrossDomainMessenger
50     */
51    function _sendMessage(
52        address _to,
53        uint64 _gasLimit,
54        uint256 _value,
55        bytes memory _data
56    ) internal override {


/// @audit Missing: '_target'
69    /**
70     * @inheritdoc CrossDomainMessenger
71     */
72    function _isUnsafeTarget(address _target) internal view override returns (bool) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ERC721Bridge.sol#L79

```solidity
File: L2/L2ERC721Bridge.sol

/// @audit Missing: '_localToken'
/// @audit Missing: '_remoteToken'
/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_tokenId'
/// @audit Missing: '_minGasLimit'
/// @audit Missing: '_extraData'
76    /**
77     * @inheritdoc ERC721Bridge
78     */
79    function _initiateBridgeERC721(
80        address _localToken,
81        address _remoteToken,
82        address _from,
83        address _to,
84        uint256 _tokenId,
85        uint32 _minGasLimit,
86        bytes calldata _extraData
87    ) internal override {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol

```solidity
File: L2/L2StandardBridge.sol

/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
195    /**
     * @notice Emits the legacy WithdrawalInitiated event followed by the ETHBridgeInitiated event.
     *         This is necessary for backwards compatibility with the legacy bridge.
     *
     * @inheritdoc StandardBridge
     */
    function _emitETHBridgeInitiated(
        address _from,
        address _to,
        uint256 _amount,
        bytes memory _extraData
    ) internal override {



/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
218    /**
     * @notice Emits the legacy DepositFinalized event followed by the ETHBridgeFinalized event.
     *         This is necessary for backwards compatibility with the legacy bridge.
     *
     * @inheritdoc StandardBridge
     */
    function _emitETHBridgeFinalized(
        address _from,
        address _to,
        uint256 _amount,
        bytes memory _extraData
    ) internal override {


/// @audit Missing: '_localToken'
/// @audit Missing: '_remoteToken'
/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
241    /**
     * @notice Emits the legacy WithdrawalInitiated event followed by the ERC20BridgeInitiated
     *         event. This is necessary for backwards compatibility with the legacy bridge.
     *
     * @inheritdoc StandardBridge
     */
    function _emitERC20BridgeInitiated(
        address _localToken,
        address _remoteToken,
        address _from,
        address _to,
        uint256 _amount,
        bytes memory _extraData
    ) internal override {


/// @audit Missing: '_localToken'
/// @audit Missing: '_remoteToken'
/// @audit Missing: '_from'
/// @audit Missing: '_to'
/// @audit Missing: '_amount'
/// @audit Missing: '_extraData'
259    /**
     * @notice Emits the legacy DepositFinalized event followed by the ERC20BridgeFinalized event.
     *         This is necessary for backwards compatibility with the legacy bridge.
     *
     * @inheritdoc StandardBridge
     */
    function _emitERC20BridgeFinalized(
        address _localToken,
        address _remoteToken,
        address _from,
        address _to,
        uint256 _amount,
        bytes memory _extraData
    ) internal override {
```

## [N-10] NatSpec `@return` argument is missing

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol

```solidity
File: L1/L1CrossDomainMessenger.sol

/// @audit Missing: '@return'
54    /**
55     * @inheritdoc CrossDomainMessenger
56     */
57    function _isOtherMessenger() internal view override returns (bool) {


/// @audit Missing: '@return'
61    /**
62     * @inheritdoc CrossDomainMessenger
63     */
64    function _isUnsafeTarget(address _target) internal view override returns (bool) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L196

```solidity
File: L1/OptimismPortal.sol


/// @audit Missing: '@return'
189    /**
190     * @notice Computes the minimum gas limit for a deposit. The minimum gas limit
191     *         linearly increases based on the size of the calldata. This is to prevent
192     *         users from creating L2 resource usage without paying for it. This function
193     *         can be used when interacting with the portal to ensure forwards compatibility.
194     *
195     */
196    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2CrossDomainMessenger.sol

```solidity
File: L2/L2CrossDomainMessenger.sol

/// @audit Missing: '@return'
62    /**
63     * @inheritdoc CrossDomainMessenger
64     */
65    function _isOtherMessenger() internal view override returns (bool) {
66        return AddressAliasHelper.undoL1ToL2Alias(msg.sender) == OTHER_MESSENGER;
67    }


/// @audit Missing: '@return'
69    /**
70     * @inheritdoc CrossDomainMessenger
71     */
72    function _isUnsafeTarget(address _target) internal view override returns (bool) {
```

## [N-11] Function ordering does not follow the `Solidity style guide`

According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L255

```solidity
File: L1/L2OutputOracle.sol


/// @audit L2OutputOracle.getL2OutputIndexAfter()  came earlier

255     function getL2OutputIndexAfter(uint256 _l2BlockNumber) public view returns (uint256) {

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L207

```solidity
File: L1/OptimismPortal.sol

/// @audit receive function came after a constructor()
207    receive() external payable {
208        depositTransaction(msg.sender, msg.value, RECEIVE_DEFAULT_GAS_LIMIT, false, bytes(""));
209    }


/// @audit OptimismPortal.minimumGasLimit() came earlier
196    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {


/// @audit came earlier
225:     function _resourceConfig()


/// @audit came earlier
434:    function depositTransaction(

```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL154C1-L155C1

```solidity
File: L1/SystemConfig.sol

/// @audit came earlier
154:     function minimumGasLimit() public view returns (uint64) {

```

## [N-12] Expressions for `constant` values such as a call to `keccak256()`, should use immutable rather than constant

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L43

```solidity
File: L1/SystemConfig.sol

43    bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

```

## [N-13] Use scientific notation `(e.g. 1e18)` rather than exponentiation (e.g. `10**18`)

While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L46

```solidity
File: L2/GasPriceOracle.sol


46        uint256 divisor = 10**DECIMALS;

```

## [N-14] `Assembly` Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L234

```solidity
File: L1/SystemConfig.sol

232    function _setUnsafeBlockSigner(address _unsafeBlockSigner) internal {
233        bytes32 slot = UNSAFE_BLOCK_SIGNER_SLOT;
234:       assembly {
235:            sstore(slot, _unsafeBlockSigner)
236:        }
237    }
```
