## Summary

### Low Risk Issues
|Number|Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | pause() and unpause() functions are missing important validations/modifier | 2 |
| [L&#x2011;02] | Immutable addresses lack zero-address check | 3 |
| [L&#x2011;03] | Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership | 2 |
| [L&#x2011;04] | Missing event in setResourceConfig() function | 1 |
| [L&#x2011;05] | The depositETHTo() and depositERC20To() functions do not check if the recipient is address(0) | 1 |
| [L&#x2011;06] | Missing event in updateDynamicConfig() function | 1 |
| [L&#x2011;07] | Missing event in  setL1BlockValues() function | 1 |
| [L&#x2011;08] | The withdrawTo() functions does not check if the recipient is address(0) | 1 |

### Non-Critical Issues
|Number|Issue|Instances| |
|-|:-|:-:|:-:|
| [N&#x2011;01] | Use named parameters for mapping type declarations | 1 |
| [N&#x2011;02] | Use a more recent version of Solidity  | All Contracts |
| [N&#x2011;03] | Use a latest version of openzeppelin library  | 1 |

### Low Risk Issues
### [L&#x2011;01]  pause() and unpause() functions are missing important validations/modifier
In OptimismPortal.sol contract,  The pause() and unpause() functions should have a modifier to ensure that they can only be called when the contract is not already paused or unpaused respectively But this validations/modifier is missing here. 

With the addition of the whenPaused modifier, the unpause() function can only be executed when the contract is currently paused. Similarly, the whenNotPaused modifier ensures that the pause() function can only be executed when the contract is not already paused.

These modifiers provide an extra layer of safety and prevent unintended actions when the contract is in an inappropriate state.

whenNotPaused modifier is provided in code. whenPaused modifier will be added as per below recommendation.

There are 2 instances of this issues.

```solidity
File: contracts/L1/OptimismPortal.sol

174    function pause() external {
175        require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can pause");
176        paused = true;
177        emit Paused(msg.sender);
178    }
179
180    /**
181     * @notice Unpause deposits and withdrawals.
182     */
183    function unpause() external {
184        require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can unpause");
185        paused = false;
186        emit Unpaused(msg.sender);
187    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L174-L187)

### Recommended Mitigation steps

```solidity
File: contracts/L1/OptimismPortal.sol

+    modifier whenPaused() {
+       require(paused, "OptimismPortal: paused");
+       _;
+     }

-    function pause() external {
+    function pause() external whenNotPaused {
        require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can pause");
        paused = true;
        emit Paused(msg.sender);
    }


-    function unpause() external {
+    function unpause() external whenPaused {
        require(msg.sender == GUARDIAN, "OptimismPortal: only guardian can unpause");
        paused = false;
        emit Unpaused(msg.sender);
    }
```

### [L&#x2011;02]  Immutable addresses lack zero-address check
Zero address check validations should be used in the constructors, to avoid the risk of setting a immutable storage variable as zero address at the time of deployment. If accidentally, address(0) is set it will cause redeployment of contract.

There are 3 instance of this issue:

```solidity
File: contracts/L1/L1CrossDomainMessenger.sol

27    constructor(OptimismPortal _portal)
28        Semver(1, 4, 0)
29        CrossDomainMessenger(Predeploys.L2_CROSS_DOMAIN_MESSENGER)
30    {
31        PORTAL = _portal;
32        initialize();
33    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L27-L33)

```solidity
File: contracts-bedrock/contracts/L1/L2OutputOracle.sol

90    constructor(
91        uint256 _submissionInterval,
92        uint256 _l2BlockTime,
93        uint256 _startingBlockNumber,
94        uint256 _startingTimestamp,
95        address _proposer,
96        address _challenger,
97        uint256 _finalizationPeriodSeconds
98    ) Semver(1, 3, 0) {
99        require(_l2BlockTime > 0, "L2OutputOracle: L2 block time must be greater than 0");
100        require(
101            _submissionInterval > 0,
102            "L2OutputOracle: submission interval must be greater than 0"
103        );
104
105        SUBMISSION_INTERVAL = _submissionInterval;
106        L2_BLOCK_TIME = _l2BlockTime;
107        PROPOSER = _proposer;
108        CHALLENGER = _challenger;
109        FINALIZATION_PERIOD_SECONDS = _finalizationPeriodSeconds;
110
111        initialize(_startingBlockNumber, _startingTimestamp);
112    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L90-L112)

```solidity
File: contracts-bedrock/contracts/L1/OptimismPortal.sol

150    constructor(
151        L2OutputOracle _l2Oracle,
152        address _guardian,
153        bool _paused,
154        SystemConfig _config
155    ) Semver(1, 6, 0) {
156        L2_ORACLE = _l2Oracle;
157        GUARDIAN = _guardian;
158        SYSTEM_CONFIG = _config;
159        initialize(_paused);
160    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L150-L160)

### Recommended Mitigaion steps
Add address(0) check validations for immutable address state variables.

### [L&#x2011;03]  Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership
 [Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable2Step.sol#L31-L56) and  [Ownable2StepUpgradeable](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/access/Ownable2StepUpgradeable.sol#L47-L63) prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.

There are 2 instances of this issues.

```solidity
File: contracts-bedrock/contracts/L1/SystemConfig.sol

125    function initialize(
126        address _owner,
127        uint256 _overhead,
128        uint256 _scalar,
129        bytes32 _batcherHash,
130        uint64 _gasLimit,
131        address _unsafeBlockSigner,
132        ResourceMetering.ResourceConfig memory _config
133    ) public initializer {
134        __Ownable_init();
135        transferOwnership(_owner);
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L125-L135)

```solidity
File: contracts-bedrock/contracts/deployment/SystemDictator.sol

193    function initialize(DeployConfig memory _config) public initializer {
194        config = _config;
195        currentStep = 1;
196        __Ownable_init();
197        _transferOwnership(config.globalConfig.controller);
198    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L193-L198)

### [L&#x2011;04]  Missing event in setResourceConfig() function
Whenever _config is updated by owner, the contract should emit event for setResourceConfig() function. But here the event is not emitted. All state variables setter functions must emit events. 

Here setResourceConfig() function and _setResourceConfig() internal function also does not emit event.

```solidity
File: contracts/L1/SystemConfig.sol

256    function setResourceConfig(ResourceMetering.ResourceConfig memory _config) external onlyOwner {
257        _setResourceConfig(_config);
258    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L256-L258)

### [L&#x2011;05]  The depositETHTo() and depositERC20To() functions do not check if the recipient is address(0)
In L1StandardBridge.sol, depositETHTo() and depositERC20To() functions are used to send ETH and tokens respectively. However these function does not have check of address(0) validation for recepient(address to). If the tokens accidentally transferred to address(0), It will be permanently lost and there is no way to recover it. Therefore to prevent such issue, It is recommended to have address(0) check validation in function. 

There is 1 instance of this issue.

```solidity
File: contracts-bedrock/contracts/L1/L1StandardBridge.sol

137    function depositETHTo(
138        address _to,
139        uint32 _minGasLimit,
140        bytes calldata _extraData
141    ) external payable {
142        _initiateETHDeposit(msg.sender, _to, _minGasLimit, _extraData);
143    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L137-L143)

```solidity
File: contracts-bedrock/contracts/L1/L1StandardBridge.sol

188    function depositERC20To(
189        address _l1Token,
190        address _l2Token,
191        address _to,
192        uint256 _amount,
193        uint32 _minGasLimit,
194        bytes calldata _extraData
195    ) external virtual {
196        _initiateERC20Deposit(
197            _l1Token,
198            _l2Token,
199            msg.sender,
200            _to,
201            _amount,
202            _minGasLimit,
203            _extraData
204        );
205    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L188-L205)

The above functions use internal functions [_initiateETHDeposit()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L265-L272) and [_initiateERC20Deposit()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L285-L295) respectively, which further uses StandardBridge.sol contract internal functions  [_initiateBridgeETH()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L361-L388) and  [_initiateBridgeERC20()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L402-L443) respectively and these internal function also does not have address(0) checks for token recepient.

### Recommended Mitigation steps
Add check for address(0) validations, preferably in internal functions or external function as desired.

### [L&#x2011;06]  Missing event in updateDynamicConfig() function
Whenever _l2OutputOracleDynamicConfig is updated by owner, the contract should emit event for updateDynamicConfig() function. But here the event is not emitted. All state variables setter functions must emit events. 

Here updateDynamicConfig() function does not emit event.

```solidity
File: contracts/deployment/SystemDictator.sol

206    function updateDynamicConfig(
207        L2OutputOracleDynamicConfig memory _l2OutputOracleDynamicConfig,
208        bool _optimismPortalDynamicConfig
209    ) external onlyOwner {
210        l2OutputOracleDynamicConfig = _l2OutputOracleDynamicConfig;
211        optimismPortalDynamicConfig = _optimismPortalDynamicConfig;
212        dynamicConfigSet = true;
213    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/deployment/SystemDictator.sol#L206-L213)

### [L&#x2011;07]  Missing event in  setL1BlockValues() function
Whenever  setL1BlockValues() valus are set by Depositor account, the contract should emit event for setL1BlockValues() function. But here the event is not emitted. All state variables setter functions must emit events. 

number, timestamp, basefee, hash, sequenceNumber, batcherHash, l1FeeOverhead, l1FeeScalar are state variables and for which the event must be emitted.

```solidity
File: contracts/deployment/SystemDictator.sol

79    function setL1BlockValues(
80        uint64 _number,
81        uint64 _timestamp,
82        uint256 _basefee,
83        bytes32 _hash,
84        uint64 _sequenceNumber,
85        bytes32 _batcherHash,
86        uint256 _l1FeeOverhead,
87        uint256 _l1FeeScalar
88    ) external {
89        require(
90            msg.sender == DEPOSITOR_ACCOUNT,
91            "L1Block: only the depositor account can set L1 block values"
92        );
93
94        number = _number;
95        timestamp = _timestamp;
96        basefee = _basefee;
97        hash = _hash;
98        sequenceNumber = _sequenceNumber;
99        batcherHash = _batcherHash;
100        l1FeeOverhead = _l1FeeOverhead;
101        l1FeeScalar = _l1FeeScalar;
102    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79-L102)

### [L&#x2011;08]  The withdrawTo() functions does not check if the recipient is address(0)
In L2StandardBridge.sol, withdrawTo() functions is used to withdraw ETH and tokens respectively. However this function does not have check of address(0) validation for recepient(address to). If the tokens accidentally transferred to address(0), It will be permanently lost and there is no way to recover it. Therefore to prevent such issue, It is recommended to have address(0) check validation in function. 

```solidity
File: contracts/Router.sol

121    function withdrawTo(
122        address _l2Token,
123        address _to,
124        uint256 _amount,
125        uint32 _minGasLimit,
126        bytes calldata _extraData
127    ) external payable virtual {
128        _initiateWithdrawal(_l2Token, msg.sender, _to, _amount, _minGasLimit, _extraData);
129    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L121-L129)

The above functions use internal functions [ _initiateWithdrawal()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2StandardBridge.sol#L179-L193) which further uses StandardBridge.sol contract internal functions  [_initiateBridgeETH()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L361-L388) and  [_initiateBridgeERC20()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L402-L443) respectively and these internal function also does not have address(0) checks for token recepient.

### Recommended Mitigation steps
Add check for address(0) validations, preferably in internal functions or external function as desired.


### Non-Critical Issues
### [N&#x2011;01]  Use named parameters for mapping type declarations
Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.

There are 1 instance of this issue.

In L2ToL1MessagePasser.sol,

```solidity
File: contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol

32    mapping(bytes32 => bool) public sentMessages;
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L2ToL1MessagePasser.sol#L32)

### [N&#x2011;02]  Use a more recent version of Solidity
For security and optimization, it is best practice to use the latest Solidity version.

It is recommended to use solidity v0.8.19 which comes with most of the bug fixes and gas optimization.

For the security fix list in the versions: [Link to reference](https://github.com/ethereum/solidity/blob/develop/Changelog.md)

There are 14 instances of this issue(All contracts).

### [N&#x2011;03]  Use a latest version of openzeppelin library
It is best practice to use latest openzeppelin library version. Recently openzeppelin has released version 4.9.0 which comes with most of the security patches and lots of gas optimizations. 

There is 1 instance of this issue.
