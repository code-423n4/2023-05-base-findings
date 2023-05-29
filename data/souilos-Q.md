
# VULN 1 

## [LOW] Keccak Constant values should used to immutable rather than constant
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 43 at optimismContest/L1/SystemConfig.sol:

    bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

------------------------------------------------------------------------ 

### Mitigation 

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.










# VULN 2 

## [LOW] Empty receive()/payable fallback() function does not authenticate requests
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 74 at optimismContest/L2/L2StandardBridge.sol:

    receive() external payable override onlyEOA {


Found in line 75 at optimismContest/L2/L2ToL1MessagePasser.sol:

    receive() external payable {


Found in line 207 at optimismContest/L1/OptimismPortal.sol:

    receive() external payable {


Found in line 106 at optimismContest/L1/L1StandardBridge.sol:

    receive() external payable override onlyEOA {

------------------------------------------------------------------------ 

### Mitigation 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))).Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas. Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.










# VULN 3 

## [LOW] Use Ownable2Step rather than Ownable
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 5 at optimismContest/L1/SystemConfig.sol:

    OwnableUpgradeable


Found in line 6 at optimismContest/L1/SystemConfig.sol:

} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";


Found in line 16 at optimismContest/L1/SystemConfig.sol:

contract SystemConfig is OwnableUpgradeable, Semver {

------------------------------------------------------------------------ 

### Mitigation 

Ownable2Step and Ownable2StepUpgradeable prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.










# VULN 4 

## [LOW] Keccak Constant values should used to immutable rather than constant
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 43 at optimismContest/L1/SystemConfig.sol:

    bytes32 public constant UNSAFE_BLOCK_SIGNER_SLOT = keccak256("systemconfig.unsafeblocksigner");

------------------------------------------------------------------------ 

### Mitigation 

By using immutable variables, the Keccak256 hash is computed at contract deployment time rather than at compilation time. This means that the value can be updated if the algorithm changes in a future compiler version, without breaking backward compatibility. Additionally, it provides better gas optimization, as the Keccak256 hash is computed only once at contract deployment instead of every time the variable is accessed during execution.










# VULN 5 

## [LOW] Avoid using tx.origin
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 417 at optimismContest/L1/OptimismPortal.sol:

        if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {


Found in line 465 at optimismContest/L1/OptimismPortal.sol:

        if (msg.sender != tx.origin) {

------------------------------------------------------------------------ 

### Mitigation 

tx.origin is a global variable in Solidity that returns the address of the account that sent the transaction. Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.










# VULN 6 

## [LOW] initialize() function can be called by anybody
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 34 at optimismContest/L2/L2CrossDomainMessenger.sol:

    function initialize() public initializer {


Found in line 38 at optimismContest/L1/L1CrossDomainMessenger.sol:

    function initialize() public initializer {

------------------------------------------------------------------------ 

### Mitigation 

initialize() function can be called by anybody when the contract is not initialized. Add a control that makes initialize() only call the Deployer Contract or EOA.










# VULN 7 

## [LOW] Use safeTransferOwnership instead of transferOwnership function
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 37 at optimismContest/L2/CrossDomainOwnable3.sol:

    function transferOwnership(address _owner, bool _isLocal) external onlyOwner {


Found in line 135 at optimismContest/L1/SystemConfig.sol:

        transferOwnership(_owner);

------------------------------------------------------------------------ 

### Mitigation 

Use a 2 structure transferOwnership which is safer. safeTransferOwnership, use it is more secure due to 2-stage ownership transfer. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol










# VULN 8 

## [LOW] Immutables should be in uppercase
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 50 at optimismContest/L1/OptimismPortal.sol:

    L2OutputOracle public immutable L2_ORACLE;


Found in line 55 at optimismContest/L1/OptimismPortal.sol:

    SystemConfig public immutable SYSTEM_CONFIG;


Found in line 60 at optimismContest/L1/OptimismPortal.sol:

    address public immutable GUARDIAN;


Found in line 20 at optimismContest/L1/L2OutputOracle.sol:

    uint256 public immutable SUBMISSION_INTERVAL;


Found in line 25 at optimismContest/L1/L2OutputOracle.sol:

    uint256 public immutable L2_BLOCK_TIME;


Found in line 30 at optimismContest/L1/L2OutputOracle.sol:

    address public immutable CHALLENGER;


Found in line 35 at optimismContest/L1/L2OutputOracle.sol:

    address public immutable PROPOSER;


Found in line 40 at optimismContest/L1/L2OutputOracle.sol:

    uint256 public immutable FINALIZATION_PERIOD_SECONDS;


Found in line 20 at optimismContest/L1/L1CrossDomainMessenger.sol:

    OptimismPortal public immutable PORTAL;

------------------------------------------------------------------------ 

### Mitigation 

Immutables should be in uppercase, it helps to distinguish immutables from other types of variables and provides better code readability.
