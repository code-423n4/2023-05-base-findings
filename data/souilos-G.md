
# VULN 1 

## [GAS] Use != 0 instead of > 0 for unsigned integer comparison
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 99 at optimismContest/L1/L2OutputOracle.sol:

        require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");


Found in line 101 at optimismContest/L1/L2OutputOracle.sol:

            _submissionInterval > 0,


Found in line 285 at optimismContest/L1/SystemConfig.sol:

            _config.elasticityMultiplier > 0,


Found in line 100 at optimismContest/L1/ResourceMetering.sol:

        if (blockDiff > 0) {

------------------------------------------------------------------------ 

### Mitigation 

Use != 0 instead of > 0 for unsigned integer comparison.










# VULN 2 

## [GAS] Use shift Right/Left instead of division/multiplication if possible
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 272 at optimismContest/L1/L2OutputOracle.sol:

            uint256 mid = (lo + hi) / 2;

------------------------------------------------------------------------ 

### Mitigation 

Use shift Right/Left instead of division/multiplication if possible.










# VULN 3 

## [GAS] ++i/i++ should be unchecked{++i}/unchecked{i++} and ++i costs less gas than i++
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 120 at optimismContest/L2/GasPriceOracle.sol:

        for (uint256 i = 0; i < length; i++) {

------------------------------------------------------------------------ 

### Mitigation 

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for and while-loops. Moreover ++i costs less gas than i++, especially when its used in for-loops (--i/i-- too).










# VULN 4 

## [GAS] Don’t initialize variables with default value
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 118 at optimismContest/L2/GasPriceOracle.sol:

        uint256 total = 0;


Found in line 120 at optimismContest/L2/GasPriceOracle.sol:

        for (uint256 i = 0; i < length; i++) {


Found in line 269 at optimismContest/L1/L2OutputOracle.sol:

        uint256 lo = 0;

------------------------------------------------------------------------ 

### Mitigation 

In such cases, initializing the variables with default values would be unnecessary and can be considered a waste of gas. Additionally, initializing variables with default values can sometimes lead to unnecessary storage operations, which can increase gas costs. For example, if you have a large array of variables, initializing them all with default values can result in a lot of unnecessary storage writes, which can increase the gas costs of your contract.










# VULN 5 

## [GAS] Use Custom Errors
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 38 at optimismContest/L2/CrossDomainOwnable3.sol:

        require(_owner != address(0), "CrossDomainOwnable3: new owner is the zero address");


Found in line 54 at optimismContest/L2/CrossDomainOwnable3.sol:

            require(owner() == msg.sender, "CrossDomainOwnable3: caller is not the owner");


Found in line 54 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_localToken != address(this), "L2ERC721Bridge: local token cannot be self");


Found in line 88 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_remoteToken != address(0), "L2ERC721Bridge: remote token cannot be address(0)");


Found in line 138 at optimismContest/L1/OptimismPortal.sol:

        require(paused == false, "OptimismPortal: paused");


Found in line 175 at optimismContest/L1/OptimismPortal.sol:

        require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can pause");


Found in line 184 at optimismContest/L1/OptimismPortal.sol:

        require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can unpause");


Found in line 418 at optimismContest/L1/OptimismPortal.sol:

            revert("OptimismPortal: withdrawal failed");


Found in line 461 at optimismContest/L1/OptimismPortal.sol:

        require(_data.length <= 120_000, "OptimismPortal: data too large");


Found in line 54 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");


Found in line 86 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");


Found in line 99 at optimismContest/L1/L2OutputOracle.sol:

        require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");


Found in line 142 at optimismContest/L1/SystemConfig.sol:

        require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");


Found in line 219 at optimismContest/L1/SystemConfig.sol:

        require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");

------------------------------------------------------------------------ 

### Mitigation 

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.










# VULN 6 

## [GAS] Use calldata instead of memory for function arguments that do not get mutated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 43 at optimismContest/L2/GasPriceOracle.sol:

    function getL1Fee(bytes memory _data) external view returns (uint256) {


Found in line 117 at optimismContest/L2/GasPriceOracle.sol:

    function getL1GasUsed(bytes memory _data) public view returns (uint256) {


Found in line 325 at optimismContest/L1/OptimismPortal.sol:

    function finalizeWithdrawalTransaction(Types.WithdrawalTransaction memory _tx)


Found in line 256 at optimismContest/L1/SystemConfig.sol:

    function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {


Found in line 266 at optimismContest/L1/SystemConfig.sol:

    function _setResourceConfig(ResourceMetering.ResourceConfig memory _config) internal {

------------------------------------------------------------------------ 

### Mitigation 

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.










# VULN 7 

## [GAS] Use assembly to check for address(0)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 151 at optimismContest/L2/L2StandardBridge.sol:

        if (_l1Token == address(0) && _l2Token == Predeploys.LEGACY_ERC20_ETH) {


Found in line 445 at optimismContest/L1/OptimismPortal.sol:

                _to == address(0),

------------------------------------------------------------------------ 

### Mitigation 

Using assembly to check for the zero address can result in significant gas savings compared to using a Solidity expression, especially if the check is performed frequently or in a loop. However, it's important to note that using assembly can make the code less readable and harder to maintain, so it should be used judiciously and with caution.










# VULN 8 

## [GAS] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 99 at optimismContest/L2/L2StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 125 at optimismContest/L2/L2StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 184 at optimismContest/L2/L2StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 27 at optimismContest/L2/L2ToL1MessagePasser.sol:

    uint16 public constant MESSAGE_VERSION = 1;


Found in line 85 at optimismContest/L2/L2ERC721Bridge.sol:

        uint32 _minGasLimit,


Found in line 83 at optimismContest/L1/L1ERC721Bridge.sol:

        uint32 _minGasLimit,


Found in line 119 at optimismContest/L1/L1StandardBridge.sol:

    function depositETH(uint32 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {


Found in line 139 at optimismContest/L1/L1StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 161 at optimismContest/L1/L1StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 193 at optimismContest/L1/L1StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 268 at optimismContest/L1/L1StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 291 at optimismContest/L1/L1StandardBridge.sol:

        uint32 _minGasLimit,


Found in line 52 at optimismContest/L1/ResourceMetering.sol:

        uint32 maxResourceLimit;


Found in line 53 at optimismContest/L1/ResourceMetering.sol:

        uint8 elasticityMultiplier;


Found in line 54 at optimismContest/L1/ResourceMetering.sol:

        uint8 baseFeeMaxChangeDenominator;


Found in line 55 at optimismContest/L1/ResourceMetering.sol:

        uint32 minimumBaseFee;


Found in line 56 at optimismContest/L1/ResourceMetering.sol:

        uint32 systemTxMaxGas;

------------------------------------------------------------------------ 

### Mitigation 

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.










# VULN 9 

## [GAS] <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 122 at optimismContest/L2/GasPriceOracle.sol:

                total += 4;


Found in line 124 at optimismContest/L2/GasPriceOracle.sol:

                total += 16;


Found in line 141 at optimismContest/L1/ResourceMetering.sol:

        params.prevBoughtGas += _amount;

------------------------------------------------------------------------ 

### Mitigation 

When you use the += operator on a state variable, the EVM has to perform three operations: load the current value of the state variable, add the new value to it, and then store the result back in the state variable. On the other hand, when you use the = operator and then add the values separately, the EVM only needs to perform two operations: load the current value of the state variable and add the new value to it. Better use <x> = <x> + <y> For State Variables.










# VULN 10 

## [GAS] Use hardcode address instead address(this)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 86 at optimismContest/L2/L2ToL1MessagePasser.sol:

        uint256 balance = address(this).balance;


Found in line 73 at optimismContest/L2/L2CrossDomainMessenger.sol:

        return _target == address(this) || _target == address(Predeploys.L2_TO_L1_MESSAGE_PASSER);


Found in line 54 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_localToken != address(this), "L2ERC721Bridge: local token cannot be self");


Found in line 252 at optimismContest/L1/OptimismPortal.sol:

            _tx.target != address(this),


Found in line 54 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");


Found in line 68 at optimismContest/L1/L1ERC721Bridge.sol:

        IERC721(_localToken).safeTransferFrom(address(this), _to, _tokenId);


Found in line 101 at optimismContest/L1/L1ERC721Bridge.sol:

        IERC721(_localToken).transferFrom(_from, address(this), _tokenId);


Found in line 65 at optimismContest/L1/L1CrossDomainMessenger.sol:

        return _target == address(this) || _target == address(PORTAL);

------------------------------------------------------------------------ 

### Mitigation 

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.










# VULN 11 

## [GAS] Functions guaranteed to revert when called by normal users can be marked payable
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 37 at optimismContest/L2/CrossDomainOwnable3.sol:

    function transferOwnership(address _owner, bool _isLocal) external onlyOwner {


Found in line 180 at optimismContest/L1/SystemConfig.sol:

    function setUnsafeBlockSigner(address _unsafeBlockSigner) external onlyOwner {


Found in line 192 at optimismContest/L1/SystemConfig.sol:

    function setBatcherHash(bytes32 _batcherHash) external onlyOwner {


Found in line 205 at optimismContest/L1/SystemConfig.sol:

    function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {


Found in line 218 at optimismContest/L1/SystemConfig.sol:

    function setGasLimit(uint64 _gasLimit) external onlyOwner {


Found in line 256 at optimismContest/L1/SystemConfig.sol:

    function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {

------------------------------------------------------------------------ 

### Mitigation 

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.










# VULN 12 

## [GAS] Don’t compare boolean expressions to boolean literals
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 417 at optimismContest/L1/OptimismPortal.sol:

        if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {

------------------------------------------------------------------------ 

### Mitigation 

In Solidity, when a boolean expression is compared to a boolean literal, it can result in additional gas costs due to the additional comparison operation that is performed.










# VULN 13 

## [GAS] Using private rather than public for constants, saves gas
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 20 at optimismContest/L1/L2OutputOracle.sol:

    uint256 public immutable SUBMISSION_INTERVAL;


Found in line 25 at optimismContest/L1/L2OutputOracle.sol:

    uint256 public immutable L2_BLOCK_TIME;


Found in line 40 at optimismContest/L1/L2OutputOracle.sol:

    uint256 public immutable FINALIZATION_PERIOD_SECONDS;

------------------------------------------------------------------------ 

### Mitigation 

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.










# VULN 14 

## [GAS] >= costs less gas than >
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 505 at optimismContest/L1/OptimismPortal.sol:

        return block.timestamp > _timestamp + L2_ORACLE.FINALIZATION_PERIOD_SECONDS();

------------------------------------------------------------------------ 

### Mitigation 

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.










# VULN 15 

## [GAS] Empty blocks should be removed or emit something
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 69 at optimismContest/L2/L2StandardBridge.sol:

    {}


Found in line 20 at optimismContest/L2/SequencerFeeVault.sol:

    constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}


Found in line 33 at optimismContest/L2/GasPriceOracle.sol:

    constructor() Semver(1, 0, 0) {}


Found in line 19 at optimismContest/L2/BaseFeeVault.sol:

    constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}


Found in line 70 at optimismContest/L2/L2ToL1MessagePasser.sol:

    constructor() Semver(1, 0, 0) {}


Found in line 19 at optimismContest/L2/L1FeeVault.sol:

    constructor(address _recipient) FeeVault(_recipient, 10 ether) Semver(1, 1, 0) {}


Found in line 31 at optimismContest/L2/L2ERC721Bridge.sol:

    {}


Found in line 65 at optimismContest/L2/L1Block.sol:

    constructor() Semver(1, 0, 0) {}


Found in line 31 at optimismContest/L1/L1ERC721Bridge.sol:

    {}


Found in line 101 at optimismContest/L1/L1StandardBridge.sol:

    {}

------------------------------------------------------------------------ 

### Mitigation 

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}).










# VULN 16 

## [GAS] Use assembly to write address storage values
------------------------------------------------------------------------ 

### Proof of concept 

Found in lines 150 to 155 at optimismContest/L1/OptimismPortal.sol:

    constructor(
        L2OutputOracle _l2Oracle,
        address _guardian,
        bool _paused,
        SystemConfig _config
    ) Semver(1, 6, 0) {


Found in lines 90 to 98 at optimismContest/L1/L2OutputOracle.sol:

    constructor(
        uint256 _submissionInterval,
        uint256 _l2BlockTime,
        uint256 _startingBlockNumber,
        uint256 _startingTimestamp,
        address _proposer,
        address _challenger,
        uint256 _finalizationPeriodSeconds
    ) Semver(1, 3, 0) {


Found in lines 93 to 101 at optimismContest/L1/SystemConfig.sol:

    constructor(
        address _owner,
        uint256 _overhead,
        uint256 _scalar,
        bytes32 _batcherHash,
        uint64 _gasLimit,
        address _unsafeBlockSigner,
        ResourceMetering.ResourceConfig memory _config
    ) Semver(1, 3, 0) {

------------------------------------------------------------------------ 

### Mitigation 

Use assembly to write address storage values. Here are a few reasons: * Reduced opcode usage: When using assembly, you can directly manipulate storage values using lower-level instructions like sstore (storage store) instead of relying on higher-level Solidity storage assignments. These direct operations typically result in fewer opcode executions, reducing gas costs. * Avoiding unnecessary checks: Solidity storage assignments often involve additional checks and operations, such as enforcing security modifiers or triggering events. By using assembly, you can bypass these additional checks and perform the necessary storage operations directly, resulting in gas savings. * Optimized packing: Assembly provides greater flexibility in packing and unpacking data structures. By carefully arranging and manipulating the storage layout in assembly, you can achieve more efficient storage utilization and minimize wasted storage space. * Fine-grained control: Assembly allows for precise control over gas-consuming operations. You can optimize gas usage by selecting specific instructions and minimizing unnecessary operations or data copying.










# VULN 17 

## [GAS] Use ERC721A instead ERC721
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 21 at optimismContest/L2/L2ERC721Bridge.sol:

contract L2ERC721Bridge is ERC721Bridge, Semver {


Found in line 30 at optimismContest/L2/L2ERC721Bridge.sol:

        ERC721Bridge(_messenger, _otherBridge)


Found in line 46 at optimismContest/L2/L2ERC721Bridge.sol:

    function finalizeBridgeERC721(


Found in line 54 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_localToken != address(this), "L2ERC721Bridge: local token cannot be self");


Found in line 59 at optimismContest/L2/L2ERC721Bridge.sol:

            ERC165Checker.supportsInterface(_localToken, type(IOptimismMintableERC721).interfaceId),


Found in line 60 at optimismContest/L2/L2ERC721Bridge.sol:

            "L2ERC721Bridge: local token interface is not compliant"


Found in line 64 at optimismContest/L2/L2ERC721Bridge.sol:

            _remoteToken == IOptimismMintableERC721(_localToken).remoteToken(),


Found in line 65 at optimismContest/L2/L2ERC721Bridge.sol:

            "L2ERC721Bridge: wrong remote token for Optimism Mintable ERC721 local token"


Found in line 70 at optimismContest/L2/L2ERC721Bridge.sol:

        IOptimismMintableERC721(_localToken).safeMint(_to, _tokenId);


Found in line 73 at optimismContest/L2/L2ERC721Bridge.sol:

        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);


Found in line 79 at optimismContest/L2/L2ERC721Bridge.sol:

    function _initiateBridgeERC721(


Found in line 88 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_remoteToken != address(0), "L2ERC721Bridge: remote token cannot be address(0)");


Found in line 92 at optimismContest/L2/L2ERC721Bridge.sol:

            _from == IOptimismMintableERC721(_localToken).ownerOf(_tokenId),


Found in line 93 at optimismContest/L2/L2ERC721Bridge.sol:

            "L2ERC721Bridge: Withdrawal is not being initiated by NFT owner"


Found in line 98 at optimismContest/L2/L2ERC721Bridge.sol:

        address remoteToken = IOptimismMintableERC721(_localToken).remoteToken();


Found in line 101 at optimismContest/L2/L2ERC721Bridge.sol:

            "L2ERC721Bridge: remote token does not match given value"


Found in line 107 at optimismContest/L2/L2ERC721Bridge.sol:

        IOptimismMintableERC721(_localToken).burn(_from, _tokenId);


Found in line 110 at optimismContest/L2/L2ERC721Bridge.sol:

            L1ERC721Bridge.finalizeBridgeERC721.selector,


Found in line 124 at optimismContest/L2/L2ERC721Bridge.sol:

        emit ERC721BridgeInitiated(_localToken, remoteToken, _from, _to, _tokenId, _extraData);


Found in line 15 at optimismContest/L1/L1ERC721Bridge.sol:

contract L1ERC721Bridge is ERC721Bridge, Semver {


Found in line 30 at optimismContest/L1/L1ERC721Bridge.sol:

        ERC721Bridge(_messenger, _otherBridge)


Found in line 46 at optimismContest/L1/L1ERC721Bridge.sol:

    function finalizeBridgeERC721(


Found in line 54 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");


Found in line 59 at optimismContest/L1/L1ERC721Bridge.sol:

            "L1ERC721Bridge: Token ID is not escrowed in the L1 Bridge"


Found in line 68 at optimismContest/L1/L1ERC721Bridge.sol:

        IERC721(_localToken).safeTransferFrom(address(this), _to, _tokenId);


Found in line 71 at optimismContest/L1/L1ERC721Bridge.sol:

        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);


Found in line 77 at optimismContest/L1/L1ERC721Bridge.sol:

    function _initiateBridgeERC721(


Found in line 86 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");


Found in line 90 at optimismContest/L1/L1ERC721Bridge.sol:

            L2ERC721Bridge.finalizeBridgeERC721.selector,


Found in line 101 at optimismContest/L1/L1ERC721Bridge.sol:

        IERC721(_localToken).transferFrom(_from, address(this), _tokenId);


Found in line 105 at optimismContest/L1/L1ERC721Bridge.sol:

        emit ERC721BridgeInitiated(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);

------------------------------------------------------------------------ 

### Mitigation 

ERC721A standard, ERC721A is an improvement standard for ERC721 tokens. It was proposed by the Azuki team and used for developing their NFT collection. Compared with ERC721, ERC721A is a more gas-efficient standard to mint a lot of of NFTs simultaneously. It allows developers to mint multiple NFTs at the same gas price. This has been a great improvement due to Ethereum’s sky-rocketing gas fee. Reference: https://nextrope.com/erc721-vs-erc721a-2/










# VULN 18 

## [GAS] require() or revert() statements that check input arguments should be at the top of the function (Also restructured some if’s)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 31 at optimismContest/L2/CrossDomainOwnable2.sol:

        require(


Found in line 60 at optimismContest/L2/CrossDomainOwnable3.sol:

            require(


Found in line 65 at optimismContest/L2/CrossDomainOwnable3.sol:

            require(


Found in line 54 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_localToken != address(this), "L2ERC721Bridge: local token cannot be self");


Found in line 58 at optimismContest/L2/L2ERC721Bridge.sol:

        require(


Found in line 63 at optimismContest/L2/L2ERC721Bridge.sol:

        require(


Found in line 88 at optimismContest/L2/L2ERC721Bridge.sol:

        require(_remoteToken != address(0), "L2ERC721Bridge: remote token cannot be address(0)");


Found in line 91 at optimismContest/L2/L2ERC721Bridge.sol:

        require(


Found in line 99 at optimismContest/L2/L2ERC721Bridge.sol:

        require(


Found in line 89 at optimismContest/L2/L1Block.sol:

        require(


Found in line 138 at optimismContest/L1/OptimismPortal.sol:

        require(paused == false, "OptimismPortal: paused");


Found in line 251 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 261 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 276 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 297 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 332 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 344 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 352 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 361 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 375 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 381 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 387 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 418 at optimismContest/L1/OptimismPortal.sol:

            revert("OptimismPortal: withdrawal failed");


Found in line 444 at optimismContest/L1/OptimismPortal.sol:

            require(


Found in line 452 at optimismContest/L1/OptimismPortal.sol:

        require(


Found in line 461 at optimismContest/L1/OptimismPortal.sol:

        require(_data.length <= 120_000, "OptimismPortal: data too large");


Found in line 54 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");


Found in line 57 at optimismContest/L1/L1ERC721Bridge.sol:

        require(


Found in line 86 at optimismContest/L1/L1ERC721Bridge.sol:

        require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");


Found in line 99 at optimismContest/L1/L2OutputOracle.sol:

        require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");


Found in line 100 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 148 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 154 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 185 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 190 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 195 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 200 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 214 at optimismContest/L1/L2OutputOracle.sol:

            require(


Found in line 263 at optimismContest/L1/L2OutputOracle.sol:

        require(


Found in line 142 at optimismContest/L1/SystemConfig.sol:

        require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");


Found in line 273 at optimismContest/L1/SystemConfig.sol:

        require(


Found in line 279 at optimismContest/L1/SystemConfig.sol:

        require(


Found in line 284 at optimismContest/L1/SystemConfig.sol:

        require(


Found in line 289 at optimismContest/L1/SystemConfig.sol:

        require(


Found in line 142 at optimismContest/L1/ResourceMetering.sol:

        require(

------------------------------------------------------------------------ 

### Mitigation 

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.
