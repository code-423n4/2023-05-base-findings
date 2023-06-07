# QA Report
## Summary
Total 07 Low and 15 Non-Critical
### Low Risk Issues
- [L-01] Erroneous comments in the `StandardBridge.sol` contract
- [L-02] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256
- [L-03] Potential EIP-1052 Deviation
- [L-04] Wrong `StandardBridge` interface
- [L-05] AVOID USING TX.ORIGIN
- [L-06] The minimum transaction value of 21,000 gas may change in the future
- [L-07] `opaqueData` is packed with 2 arguments value
### Non-Critical Issues
- [N-01] CODE COMMENT FOR `finalizeWithdrawalTransaction()` CAN BE MORE ACCURATE
- [N-02] SOLIDITY VERSION 0.8.19 CAN BE USED
- [N-03] Use require instead of assert
- [N-04] Include the project name and development team information in the contract to increase the popularity and trust of users in the project
- [N-05] Use SMTChecker
- [N-06] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword
- [N-07] Use named parameters for mapping type declarations
- [N-08] MISSING address(0) CHECKS FOR CRITICAL CONSTRUCTOR INPUTS
- [N-09] Use `bytes.concat()`
- [N-10] Use Underscores for Number Literals
- [N-11] ERROR MESSAGE FOR `initialize()` CAN BE MORE ACCURATE
- [N-12] Contracts are not using their OZ Upgradeable counterparts
- [N-13] Project Upgrade and Stop Scenario should be
- [N-14] Constants on the left are better
- [N-15] No need to initialize uints to zero


## [L-01] Erroneous comments in the `StandardBridge.sol` contract
### Descriptions:
The code comments for the `bridgeERC20` and `bridgeERC20To` functions in the `universal/StandardBridge.sol` contract do not match the actual code. The comments say the bridge returns tokens to the sender if the bridging fails, but the refund logic has been removed since [PR#3535](https://github.com/ethereum-optimism/optimism/pull/3535).
### Impact:
Specfication error only.
### Code Snippet:
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L218-L221
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L250-L253
### Recommendation:
Remove the outdated code comments.

## [L-02] Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256
### Descriptions:
```
File: OptimismPortal.sol
224:        uint256 _l2OutputIndex,
-----
313:            l2OutputIndex: uint128(_l2OutputIndex)
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L242-L318)
In the `OptimismPortal.sol` contract, the `proveWithdrawalTransaction()` function takes an argument `_l2OutputIndex` of type uint256.
Now, in the function, the value of `_l2OutputIndex` is downcasted to uint128.
### Recommendation:
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256 or define argument `_l2OutputIndex` of type uint128.

## [L-03] Potential EIP-1052 Deviation
### Descriptions:
In [setCode()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/legacy/L1ChugSplashProxy.sol#L125), the `EXTCODEHASH` opcode is realized by using the [_getAccountCodeHash](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/legacy/L1ChugSplashProxy.sol#L282) function of the [L1ChugSplashProxy.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/legacy/L1ChugSplashProxy.sol) contract. This returns the registered bytecode hashes for deployed contracts. The [EIP-1052](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1052.md#test-cases) standard dictates that the return of precompile contracts should be either `0` or `c5d246...` (the hash of the empty string). However, precompile code hashes can differ from those values by setting them to their real code hash or to an arbitrary value during a forced deployment, thereby causing an inconsistency with EIP-1052.
### Recommendation:
Consider conforming to EIP-1052 by adding additional checks to ensure that the precompile contracts return either of the values defined in the specification.

## [L-04] Wrong `StandardBridge` interface
### Description:
The [bridges.md](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/specs/bridges.md) specification file shows a wrong `StandardBridge` interface.
-  `function OTHER_BRIDGE() view external returns (address); ` does not exsit. It should be [function l2TokenBridge() external view returns (address)](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L253-L255)
```
File: bridges.md
interface StandardBridge {
function bridgeETH(uint32 _minGasLimit, bytes memory _extraData) payable external; // @audit syntax here
function bridgeETHTo(address _to, uint32 _minGasLimit, bytes memory _extraData) payable external; // @audit syntax here
function deposits(address, address) view external returns (uint256); // @audit syntax here
function finalizeBridgeETH(address _from, address _to, uint256 _amount, bytes memory _extraData) payable external; // @audit syntax here
function messenger() view external returns (address); // @audit syntax here
function OTHER_BRIDGE() view external returns (address); // @audit function does not exist
}
```
[bridges.md](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/specs/bridges.md)
### Recommendation:
Use the correct interface and adhere to the solidity syntax.

## [L-05] AVOID USING TX.ORIGIN
tx.origin is a global variable in Solidity that returns the address of the account that sent the transaction.
Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.
https://community.optimism.io/docs/developers/build/differences/#accessing-l1-information

## [L-06] The minimum transaction value of 21,000 gas may change in the future
```
File: OptimismPortal.sol
196:    function minimumGasLimit(uint64 _byteCount) public pure returns (uint64) {
197:        return _byteCount * 16 + 21000;
198:    }
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L196-L198)
Any transaction has a ‘base fee’ of 21,000 gas in order to cover the cost of an elliptic curve operation that recovers the sender address from the signature, as well as the disk space of storing the transaction, according to the Ethereum White Paper.
https://ethereum-magicians.org/t/some-medium-term-dust-cleanup-ideas/6287
Why do txs cost 21000 gas?
To understand how special-purpose cheap withdrawals could be done, it helps first to understand what goes into the 21000 gas in a tx. The cost of processing a tx includes:

Two account writes (a balance-editing CALL normally costs 9000 gas)
A signature verification (compare: the ECDSA precompile costs 3000 gas)
The transaction data (~100 bytes, so 1600 gas, though originally it cost 6800)
Some more gas was tacked on to account for transaction-specific overhead, bringing the total to 21000.
protocol_params.go#L31

The minimum transaction value of 21,000 gas may change in the future, so it is recommended to make this value updatable.
### Recommendation:
Add this code:
```
uint256 txcost = 21_000;
 function changeTxCost(uint256 _amount) public onlyOwner {
        txcost = _amount;
    }
```


## [L-07] `opaqueData` is packed with 2 arguments value
### Description:
```
File: OptimismPortal.sol
472:        bytes memory opaqueData = abi.encodePacked(
473:            msg.value, // @audit must be _to rather than msg.value
474:            _value,
475:            _gasLimit,
476:            _isCreation,
477:            _data
478:        );
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L472-L478)
Opaque data is used to update the TransactionDeposited event in the future, current implementation is not correct
### Recomendation:
`msg.value` should be replaced by `_to`

## [N-01] CODE COMMENT FOR `finalizeWithdrawalTransaction()` CAN BE MORE ACCURATE
### Descriptions:
```
File: OptimismPortal.sol
349:        // As a sanity check, we make sure that the proven withdrawal's timestamp is greater than
350:        // starting timestamp inside the L2OutputOracle. Not strictly necessary but extra layer of
351:        // safety against weird bugs in the proving step.
```
[The commnents](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L349-L351) says: `we make sure that the proven withdrawal's timestamp is greater than starting timestamp inside the L2OutputOracle` but in the code used `greater than or equal to(>=)` to compare.
```
File: OptimismPortal.sol
353:            provenWithdrawal.timestamp >= L2_ORACLE.startingTimestamp(),
354:            "OptimismPortal: withdrawal timestamp less than L2 Oracle starting timestamp"
```
[OptimismPortal.sol#L353](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L353)
### Recomendation:
The code must implement exactly as described

## [N-02] SOLIDITY VERSION 0.8.19 CAN BE USED
Most contracts are using 0.8.15 or lower. Consider updating to the latest version 0.8.19 to ensure the compiler contains the latest security fixes.
Using the more updated version of Solidity can enhance security. As described in https://github.com/ethereum/solidity/releases, Version 0.8.19 is the latest version of Solidity, which "contains a fix for a long-standing bug that can result in code that is only used in creation code to also be included in runtime bytecode". To be more secured and more future-proofed, please consider using Version 0.8.19 for the following contracts.
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L2

## [N-03] Use require instead of assert
### Description:
assert should not be used except for tests, require should be used.
Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.
The big difference between the two is that the assert()function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.
This is the most common Solidity function used by developers for debugging and error handling.
assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The assert function generates an error of type Panic(uint256).Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. There's a mistake".
### Contexts:
```
File: CrossDomainMessenger.sol
341:            assert(msg.value == _value);
342:            assert(!failedMessages[versionedHash]);
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L341-L342)

## [N-04] Include the project name and development team information in the contract to increase the popularity and trust of users in the project
### Recommendation: Use form like FraxFinance project
https://github.com/FraxFinance/frxETH-public/blob/7f7731dbc93154131aba6e741b6116da05b25662/src/sfrxETH.sol#L4-L24

## [N-05] Use SMTChecker
The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## [N-06] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword
Eg:
```
File: OptimismPortal.sol
72:    mapping(bytes32 => bool) public finalizedWithdrawals;
77:    mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72-L77)


## [N-07] Use named parameters for mapping type declarations
Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18
Eg:
```
File: OptimismPortal.sol
72:    mapping(bytes32 => bool) public finalizedWithdrawals;
77:    mapping(bytes32 => ProvenWithdrawal) public provenWithdrawals;
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L72-L77)

## [N-08] MISSING address(0) CHECKS FOR CRITICAL CONSTRUCTOR INPUTS
To prevent unintended behaviors, critical constructor inputs should be checked against address(0).
```
File: OptimismPortal.sol
157:        GUARDIAN = _guardian; // @audit missing address(0) checks

File: L2OutputOracle.sol
107:        PROPOSER = _proposer;
108:        CHALLENGER = _challenger;
```
[OptimismPortal.sol#L157](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L157)
[L2OutputOracle.sol#L107-L108](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L107-L108)

## [N-09] Use `bytes.concat()`
Solidity version 0.8.4 introduces bytes.concat() (vs abi.encodePacked(<bytes>,<bytes>))
Eg:
```
File: OptimismPortal.sol
472:        bytes memory opaqueData = abi.encodePacked(
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L472)

## [N-10] Use Underscores for Number Literals
```
File: OptimismPortal.sol
197:        return _byteCount * 16 + 21000;
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L197)

## [N-11] ERROR MESSAGE FOR `initialize()` CAN BE MORE ACCURATE
```
File: L2OutputOracle.sol
125:            _startingTimestamp <= block.timestamp, // @audit less than or equal to
126:            "L2OutputOracle: starting L2 timestamp must be less than current time"
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L125-L126)
### Recommendation:
Consider change to: "L2OutputOracle:  starting L2 timestamp must be less than or equal to current time"


## [N-12] Contracts are not using their OZ Upgradeable counterparts
The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts. It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.
```
File: L2OutputOracle.sol
4: import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L4
```
File: OptimismPortal.sol
4: import { Initializable } from "@openzeppelin/contracts/proxy/utils/Initializable.sol";
```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L4
### Recomendation:
Where applicable, use the contracts from @openzeppelin/contracts-upgradeable instead of @openzeppelin/contracts. See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/Initializable.sol

## [N-13] Project Upgrade and Stop Scenario should be
At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN
https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

## [N-14] Constants on the left are better
If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns).
https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html
Eg:
```
File: OptimismPortal.sol
461:        require(_data.length <= 120_000, "OptimismPortal: data too large"); // @audit consider change to 120_000 >= _data.length
```
[Link to code](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L461)

## [N-15] No need to initialize uints to zero
There is no need to initialize `uint` variables to zero as their default value is `0`
```
File: L2OutputOracle.sol
269:        uint256 lo = 0;
```
[L2OutputOracle.sol#L269](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L269)

```
File: OptimismPortal.sol
40:    uint256 internal constant DEPOSIT_VERSION = 0;
```
[OptimismPortal.sol#L40](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L40)

```
File: SystemConfig.sol
36:    uint256 public constant VERSION = 0;
```
[SystemConfig.sol#L36](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L36)

```
File: GasPriceOracle.sol
118:        uint256 total = 0;
```
[GasPriceOracle.sol#L118](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L118)

