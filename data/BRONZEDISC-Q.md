## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.


---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

```solidity
// external function coming before public ones
85:    function burn() external {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

```solidity
// external functions coming before public ones
43:    function getL1Fee(bytes memory _data) external view returns (uint256) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol

```solidity
// external view function coming before non-view external functions, place it last
166:    function unsafeBlockSigner() external view returns (address) {

// internal functions coming before external ones
232:    function _setUnsafeBlockSigner(address _unsafeBlockSigner) internal {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol

```solidity
// non view/pure function coming after view ones, place it first
179:    function __ResourceMetering_init() internal onlyInitializing {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

```solidity
//receive function should come right after constructor
207:    receive() external payable {

// public functions coming after external ones, should come before
196:    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {
434:    function depositTransaction(

// internal functions coming before public and external ones
225:    function _resourceConfig()
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

```solidity
// public functions coming after external ones, should come before
255:    function getL2OutputIndexAfter(uint256 _l2BlockNumber) public view returns (uint256) {
314:    function nextOutputIndex() public view returns (uint256) {
324:    function latestBlockNumber() public view returns (uint256) {
336:    function nextBlockNumber() public view returns (uint256) {
347:    function computeL2Timestamp(uint256 _l2BlockNumber) public view returns (uint256) {
```
---

### natSpec missing [3]

Some functions are missing @params or @returns. Specification Format.” These are written with a triple slash (///) or a double asterisk block(/** ... */) directly above function declarations or statements to generate documentation in JSON format for developers and end-users. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). These comments contain different types of tags:
- @title: A title that should describe the contract/interface @author: The name of the author (for contract, interface) 
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event) 
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event) 
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)
- @custom…: Custom tag, semantics is application-defined (for everywhere)


https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol

```solidity
// @params missing
26:    event OwnershipTransferred(
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

```solidity
// @param missing
106:    event WithdrawalProven(
165:    function initialize(bool _paused) public initializer {

// @return missing
196:    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

```solidity
// last @param missing
90:    constructor(
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE


https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

```solidity
37:    uint240 internal msgNonce;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol

```solidity
// private and internal `variables` should preppend with `underline`
55:    Types.OutputProposal[] internal l2Outputs;
```


---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol

```solidity
// different version from the rest of the repo, also lose the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol

```solidity
// different version from the rest of the repo, also lose the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol

```solidity
// different version from the rest of the repo, also lose the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

---

### Initialized variables [6]

- Variables are default initialized with 0 for `uint / int`, 0x0 for `address` and false for `bool`

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol

```solidity
118:        uint256 total = 0;
120:        for (uint256 i = 0; i < length; i++) {
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol 

```solidity
36:    uint256 public constant VERSION = 0;
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol

```solidity
40:    uint256 internal constant DEPOSIT_VERSION = 0;
```

---

### Observations [7]

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

```solidity
// params not being prefixed with underdeline as the rest of the repo as a pattern
50:    event MessagePassed(
65:    event WithdrawerBalanceBurnt(uint256 indexed amount);
```

