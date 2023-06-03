
1. `abi.encodePacked` results in Hash Collision sometimes when two dynamic argumants are encoded with it.

`>If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of an into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c").`

As the solidity docs describe, two or more dynamic types are passed to abi.encodePacked. Moreover, these dynamic values are user-specified function arguments in external functions, meaning anyone can directly specify the value of these arguments when calling the function. 
Many users can fail an issue while claiming their prizes because of this.

Recommendation : Use `abi.encode()` instead of `abi.encodePacked()`, which will prevent hash collisions

https://github.dev/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts/contracts/L1/messaging/L1CrossDomainMessenger.sol#L221

(*There are several more references of `abi.encodePacked()` in the repo)

2. Code not implemented as described in the comments - 

The timestamp is never accepted in params, so never compared with `nextTimestamp()` as described. L1 & L2 block numbers are used instead.

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L170-L172

3. The role `GUARDIAN` should only be able to call pause() when OptimismPortal is unpaused. And similarly, should be able to unpause() when OptimismPortal is paused.

Apparently there is no check, and state is changed (reassigned to same value), and event is emitted, which could cause further issues.

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L171-L187

4. The `donateETH()` function in OptimismPortal accepts ETH and makes no action. So the funds could get stuck. No way to get accidental transfers back. It is better to remove it than making the funcion 'Intentionally empty' as described in comment.

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L215-L217

5. The `depositTransaction()` from `OptimismPortal` accepts param `_isCreation` which is true if user wants to create a contract, but never validates the `_data` (bytecode) used to create the contract.

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L443-L448

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L443-L448

6. In `CrossDomainMessenger`, the variable __gap has a comment description :

`Reserve extra slots in the storage layout for future upgrades. A gap size of 41 was chosen here, so that the first slot used in a child contract would be a multiple of 50.`

```solidity
uint256[42] private __gap;
```

(Comment says that gap should be of 41, but array size is clearly 42 in declaration)

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L200

7. The tokens are never approved (`safeApprove()`) before transfer, which can cause function failure. Make sure sufficient tokens are pre-approved before contract executes the trasfer.

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/universal/StandardBridge.sol#L419-L420

8. In `L1StandardBridge` contract, the `onlyEOA` modifier is never used in `depositETHTo()` and `depositERC20To()` which I believe should exists, considering the purpose contract wants to accomplish as mentioned in comments on `depositETH()` and `depositERC20()`

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L137-L141

https://github.com/ethereum-optimism/optimism/blob/a48e53c100e6ac024f45be7bdec94ad35fe5cd1c/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L188-L195