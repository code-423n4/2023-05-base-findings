# **Base QA Report**

## **Table of Contents**

**Low Issues**

|      | Issue                                                                                        |
| ---- | -------------------------------------------------------------------------------------------- |
| L-01 | Critical Address Changes Should Use a Two-step Procedure                                     |
| L-02 | Missing events for state changing operations                                                 |
| L-03 | Use of transferFrom instead of safeTransferFrom in L1ERC721Bridge.sol                        |
| L-04 | Modifiers should only be used for checks                                                     |
| L-05 | Using vulnerable dependency version of OpenZeppelin                                          |
| L-06 | L1ERC721Bridge does not comply with ERC721, breaking composability                           |
| L-07 | Avoid using abi.encodePacked() with dynamic types when passing the result to a hash function |
| L-08 | Missing address(0) checks in critical functions                                              |
| L-09 | Known issues with compiler versions used to compile the contracts (sol 0.8.15 & 0.6.0)       |
| L-10 | Low level function call does not check for contract existence                                |
| L-11 | Use of assert() instead of require()                                                         |

**Non-Critical Issues**

|       | Issue                                                            |
| ----- | ---------------------------------------------------------------- |
| NC-01 | Unspecific Compiler Version Pragma                               |
| NC-02 | Event is emited before the important operation                   |
| NC-03 | Owner can renounce ownership                                     |
| NC-04 | Uninitialized implementation contracts in proxy pattern          |
| NC-05 | Use a more recent version of solidity                            |
| NC-06 | uint256 can be used instead of uint                              |
| NC-07 | Finally, best practices of source file layout should be followed |

# Low Issues

## L-01 Critical Address Changes Should Use a Two-step Procedure

### Vulnerability Details

OpenZepplin's Ownable provides a One-Step process for Transfer Ownership of the contract. Critical Address Changes Should always be Two-step Procedure.

The lack of a two-step procedure for critical operations leaves them error-prone. A One-Step Transfer OwnerShip procedure can fall victim to accidentally inputting the wrong future owner's address, resulting in loss of contract's ownership forever.

[CrossDomainOwnable.sol#L1-L6](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L1-L5)

### Recommendation

A two step approach should be instead implemented as this saves a ton incase a mistake is made while transferring ownership

There is another Openzeppelin Ownable contract, `Ownable2Step.sol`, which provides a two-step procedure for transfer ownership. `transferOwnership()` & `acceptOwnership()`, this could be inherited insteasd.

## L-02 Missing events for state changing operations

### Vulenerability Details

Take a look at
[L1Block.setL1BlockValues()](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L79-L103)

```
    function setL1BlockValues(
        uint64 _number,
        uint64 _timestamp,
        uint256 _basefee,
        bytes32 _hash,
        uint64 _sequenceNumber,
        bytes32 _batcherHash,
        uint256 _l1FeeOverhead,
        uint256 _l1FeeScalar
    ) external {
        require(
            msg.sender == DEPOSITOR_ACCOUNT,
            "L1Block: only the depositor account can set L1 block values"
        );

        number = _number;
        timestamp = _timestamp;
        basefee = _basefee;
        hash = _hash;
        sequenceNumber = _sequenceNumber;
        batcherHash = _batcherHash;
        l1FeeOverhead = _l1FeeOverhead;
        l1FeeScalar = _l1FeeScalar;
    }
```

### Recommendation

Consider emiting an event.

## L-03 Use of transferFrom instead of safeTransferFrom in L1ERC721Bridge.sol

### Vulenerability Details

Note that both ERC721 transferFrom method and ERC20 transferFrom method has the same byte4 signature 0x23b872dd

This means that If localToken is not ERC721 but a ERC20, transfer can still go through and the ERC20 is mistakenly bridged from L1 to L2.

However, the token cannot be bridged back, because when bridged back, the function below called:

[L1ERC721Bridge.sol#L100-L107](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L100-L107)

```solidity
        deposits[_localToken][_remoteToken][_tokenId] = true;
        IERC721(_localToken).transferFrom(_from, address(this), _tokenId);

        // Send calldata into L2
        MESSENGER.sendMessage(OTHER_BRIDGE, message, _minGasLimit);
        emit ERC721BridgeInitiated(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);
    }
```

Also The recipient could have logic in the onERC721Received() function, which is only triggered in the safeTransferFrom() function and not in transferFrom(). A notable example of such contracts is the Sudoswap pair:

```
function onERC721Received(
  address,
  address,
  uint256 id,
  bytes memory
) public virtual returns (bytes4) {
  IERC721 _nft = nft();
  // If it's from the pair's NFT, add the ID to ID set
  if (msg.sender == address(_nft)) {
    idSet.add(id);
  }
  return this.onERC721Received.selector;
}
```

### Recommendation

It is recommended to use safeTransferFrom() instead of transferFrom() when transferring ERC721s to hinder the aforementioned issues

```diff
- IERC721(_localToken).transferFrom(_from, address(this), _tokenId);
+ IERC721(_localToken).safeTransferFrom(_from, address(this), _tokenId);
```

## L-04 Modifiers should only be used for checks

### Vulnerability Details

In [ResourceMetering.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol) the [metered](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L75-L84) modifier used for more than just for checks

```
    modifier metered(uint64 _amount) {
        // Record initial gas amount so we can refund for it later.
        uint256 initialGas = gasleft();

        // Run the underlying function.
        _;

        // Run the metering function.
        _metered(_amount, initialGas);
    }
```

See [this](https://consensys.net/blog/developers/solidity-best-practices-for-smart-contract-security/), but in short it's known that the code inside a modifier is usually executed before the function body, so any state changes or external calls will violate the Checks-Effects-Interactions pattern. Moreover, these statements may also remain unnoticed by the developer, as the code for the modifier may be far from the function declaration.

### Recommendation

Try to use modifiers only for checks

## L-05 Using vulnerable dependency version of OpenZeppelin

### Vulnerability Details

The [package.json](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/package.json#L57-L58) configuration file says that the project is using 4.7.3 of OZ which is not the latest update version:

```json
    "@openzeppelin/contracts": "4.7.3",
    "@openzeppelin/contracts-upgradeable": "4.7.3",

```

### Recommendation

Upgrade the dependencies to their latest counterparts

## L-06 L1ERC721Bridge does not comply with ERC721, breaking composability

### Velnerability Details

Not implementing the `ERC721Receiver` interface in the `L1ERC721Bridge` contract while holding ERC721 tokens is considered an anti-pattern and goes against the desired standard for handling ERC721 tokens.

The contract [L1ERC721Bridge](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L100-L107) should implement the `ERC721Receiver` interface to ensure compatibility with the ERC721 token standard.

### Recommendation

To address this vulnerability, it is recommended to modify the `ERC721Bridge` contract to implement the `ERC721Receiver` interface. This will ensure compliance with the ERC721 token standard and align with best practices for handling ERC721 tokens.

## L-07 Avoid using abi.encodePacked() with dynamic types when passing the result to a hash function

### Vulnerability Details

Take a look at [Oracle.sol#L99](https://github.com/ethereum-optimism/op-geth/blob/bdab05ca786fddaa81c60cbd4ee06df3b2585fa3/contracts/checkpointoracle/contract/oracle.sol#L99)

```
bytes32 signedHash = keccak256(abi.encodePacked(byte(0x19), byte(0), this, _sectionIndex, _hash));
```

Using `abi.encodePacked()` with dynamic types when passing the result to a hash function can lead to potential hash collisions. The `abi.encode()` function pads items to 32 bytes, ensuring better collision prevention. It is important to note that if there is only one parameter, it is possible to cast it to `bytes()` or `bytes32()` instead of using `abi.encodePacked()`. When dealing with multiple parameters that are strings or bytes, it is recommended to use `bytes.concat()`.
Key to also note that `abi.encodePacked()` is deprecated in newest solidity versions

### Recommendation

To prevent hash collisions, it is recommended to avoid using `abi.encodePacked()` with dynamic types when passing the result to a hash function. Instead, use `abi.encode()` which pads items to 32 bytes, ensuring better collision prevention. If there is only one parameter, consider casting it to `bytes()` or `bytes32()` in place of `abi.encodePacked()`. Additionally, when dealing with multiple parameters that are strings or bytes, use `bytes.concat()` to concatenate them before passing them to the hash function. By following these recommendations, you can enhance the integrity and security of the hash-based operations in your contract.

### Vulnerability Details

### Recommendation

## L-08 Missing address(0) checks in critical functions

### Vulenerability Details

Most functions do not check whether input parameter is address(0).

Look at the instance below:

```solidity
    function initiateWithdrawal(
        address _target,
        uint256 _gasLimit,
        bytes memory _data
    ) public payable {
        bytes32 withdrawalHash = Hashing.hashWithdrawal(
            Types.WithdrawalTransaction({
                nonce: messageNonce(),
                sender: msg.sender,
                target: _target,
                value: msg.value,
                gasLimit: _gasLimit,
                data: _data
            })
        );
```

And a lot of instances in multiple contracts in spec.

### Recommendation

Include zero address checks

## L-09 Known issues with compiler versions used to compile the contracts (sol 0.8.15 & 0.6.0)

### Vulnerability Details

The contracts in scope have been compiled using older versions of the Solidity compiler (specifically, up to version 0.8.15). However, there are known issues that have been addressed in the latest compiler versions (> 0.8.15). Unfortunately, these addressed issues are still present in the 0.8.15 version used by the contracts.

One known issue relates to the `abi.encode` family of functions, which can lead to vulnerabilities when certain conditions are met. The contracts in question fulfill the criteria for being vulnerable to this issue. For more technical details about this issue, you can refer to the following link: [Solidity Blog Post](https://blog.soliditylang.org/2022/08/08/calldata-tuple-reencoding-head-overflow-bug/).

Additionally, another known issue is specific to the 0.6.0 compiler version. This version is used in the `oracle.sol` contract from `op-geth`. The issue arises when attempting to push an empty admin address (zero) in a function call. It occurs due to how the admin list is loaded into memory and stored in storage. More information about this issue can be found here: [Solidity Blog Post](https://blog.soliditylang.org/2022/06/15/dirty-bytes-array-to-storage-bug/).

### Recommendation

To mitigate these known issues and benefit from the improvements and bug fixes in the Solidity compiler, it is strongly recommended to upgrade the compiler version to either 0.8.16 or 0.8.17 for all the contracts.

By using the latest compiler version, the contracts can avoid potential vulnerabilities and ensure better code quality. Updating the compiler version is an essential step in maintaining the security and reliability of the contracts.

It is also advisable to regularly check and follow the Solidity documentation for any updates, security recommendations, and bug fixes related to the compiler version being used.

Links to the affected code snippets and the Solidity documentation for more information:

Solidity Bugs Documentation: [Solidity Documentation](https://docs.soliditylang.org/en/v0.8.18/bugs.html)

By addressing these compiler-related vulnerabilities and staying up-to-date with the latest Solidity compiler version, the contracts can enhance their security and maintain compliance with best practices.

## L-10 Low level function call does not check for contract existence

### Vulnerability Details

The safeCall.call() is being implemented in multiple instances in code

Here is the current implementation of the call from the SafeCall library

```
library SafeCall {
    /**
     * @notice Perform a low level call without copying any returndata
     *
     * @param _target   Address to call
     * @param _gas      Amount of gas to pass to the call
     * @param _value    Amount of value to pass to the call
     * @param _calldata Calldata to pass to the call
     */
    function call(
        address _target,
        uint256 _gas,
        uint256 _value,
        bytes memory _calldata
    ) internal returns (bool) {
        bool _success;
        assembly {
            _success := call(
                _gas, // gas
                _target, // recipient
                _value, // ether value
                add(_calldata, 0x20), // inloc
                mload(_calldata), // inlen
                0, // outloc
                0 // outlen
            )
        }
        return _success;
    }
}
```

As seen this call does not check for account's existence prior to interacting with account

Note that this low level is performed in OptimismPortal when a withdrawal transaction is to be finalized

```
    function finalizeWithdrawalTransaction(Types.WithdrawalTransaction memory _tx) external {
....
// the above function  calls the beloe
....
// Trigger the call to the target contract. We use SafeCall because we don't
// care about the returndata and we don't want target contracts to be able to force this
// call to run out of gas via a returndata bomb.
bool success = SafeCall.call(
	_tx.target,
	gasleft() - FINALIZE_GAS_BUFFER,
	_tx.value,
	_tx.data
);
```

Do keep in mind that the same function is used in both of `relayMessage()` and `finalizeBridgeETH()` The low level is used in CrossDomainMessager.sol when relay the message.

```
function relayMessage(
	uint256 _nonce,
	address _sender,
	address _target,
	uint256 _value,
	uint256 _minGasLimit,
	bytes calldata _message
) external payable nonReentrant whenNotPaused {
  .....
    // the above calls
.....

xDomainMsgSender = _sender;
bool success = SafeCall.call(_target, gasleft() - RELAY_GAS_BUFFER, _value, _message);
xDomainMsgSender = Constants.DEFAULT_L2_SENDER;

****

And finally, the low level call is used in  the finalizeBridgeETH() too

function finalizeBridgeETH(
	address _from,
	address _to,
	uint256 _amount,
	bytes calldata _extraData
) public payable onlyOtherBridge {
	require(msg.value == _amount, "StandardBridge: amount sent does not match amount required");
	require(_to != address(this), "StandardBridge: cannot send to self");
	require(_to != address(MESSENGER), "StandardBridge: cannot send to messenger");

	emit ETHBridgeFinalized(_from, _to, _amount, _extraData);

	bool success = SafeCall.call(_to, gasleft(), _amount, hex"");
	require(success, "StandardBridge: ETH transfer failed");
}
```

Whenever the function call is a smart contract call with call data and ETH attached if the target address to call does not exist, the transaction should instead fail, but it sliently goes through.

### Recommendation

If the function call is a smart contract call with call data attached, check the contract existense before executing the function, to not let the transaction that expect to fail sliently execute.

## L-11 Use of assert() instead of require()

### Vulnerability Details

Contracts use assert() instead of require() in multiple places. This causes a Panic error on failure and prevents the use of error strings.

Prior to solc 0.8.0, assert() used the invalid opcode which used up all the remaining gas while require() used the revert opcode which refunded the gas and therefore the importance of using require() instead of assert() was greater. However, after 0.8.0, assert() uses revert opcode just like require() but creates a Panic(uint256) error instead of Error(string) created by require(). Nevertheless, Solidity’s documentation says:

"Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic.”

whereas

“The require function either creates an error without any data or an error of type Error(string). It should be used to ensure valid conditions that cannot be detected until execution time. This includes conditions on inputs or return values from calls to external contracts.”

Also, you can optionally provide a message string for require, but not for assert.

Take a look at [CrossDomainMessenger.sol#L341-L342](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L341-L342)

```
            assert(msg.value == _value);
            assert(!failedMessages[versionedHash]);
```

Now also look at this instance from [ProxyAdmin.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/ProxyAdmin.sol#L227)

```
            assert(false);
```

### Recommendation

Use require() with informative error strings instead of assert().

# Non-critical Issues

## NC-01 Unspecific Compiler Version Pragma

### Vulnerability Details

Check While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different evm compilation that is ultimately deployed on the blockchain.

From [CrossDomainOwnable#L2]():
`pragma solidity ^0.8.0;`

### Recommendation

Consider locking the pragma verison.

## NC-02 Event is emitted before the important operation

### Vulnerability Details

Multiple instances in code have events being emitted before the important operations are executed This could easily cause some issues on the interface

### Recommendation

Try to integrate events at the end

## NC-03 Owner can renounce ownership

### Vulnerability Details

The non-fungible OwnableUpgradeable and Ownable used in several project contracts inherit/implement renounceOwnership(). This can represent a certain risk if the ownership is renounced for any other reason than by design.

Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

[From CrossDomainOwnable2.sol:](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L6)
`import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol"; `

[From CrossDomainOwnable.sol:](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L6)
`import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol"; `

[From SystemConfig.sol](https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL4C1-L6C76)

```
import {
    OwnableUpgradeable
} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
```

### Recommendation

Consider re-implementing the function to disable it or clearly specifying if it is part of the contract design.

## NC-04 Uninitialized implementation contracts in proxy pattern

### Vulnerability Details

Almost all of the contract in the protocol are based on proxy pattern to be upgradable but some of the implementation contract are not initialized. attacker can become owner of this contract and perform malicious actions. even if there are any clear malicious action right now with every update or change the risk of the bad action by owner of the implementation contract would be there. contracts SystemDictator is not initialized.

### Recommendation

Constructors should include the implementation of `intialize()`

## NC-05 Use a more recent version of solidity

### Proof of Concept

Multiple instances within codes both in/out of scope

### Recommendation

Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)

## NC-06 uint256 can be used instead of uint

### Vulnerability Details

Multiple instances within code in/out of scope, especially in loops

### Recommendation

For explicitness and consistency, uint256 can be used instead uint.

## NC-07 Finally, best practices of source file layout should be followed

### Vulnerability Details

Multiple instances within code in/out of scope

### Recommendation

I recommend following best practices of solidity source file layout for readability.
Reference: https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout

This best practices is to layout a contract elements in following order:
Pragma statements, Import statements, Interfaces, Libraries, Contracts

Inside each contract, library or interface, use the following order:
Type declarations, State variables, Events, Modifiers, Functions
