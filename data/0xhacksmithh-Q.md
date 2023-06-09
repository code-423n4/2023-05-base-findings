### [Low-01] Absence of Zero-address check while setting Critical `state variable`
In Some cases `state variable` are `immutables`. So there should be definitely checks for Zero-addresses.
```
File:: L1/L1CrossDomainMessenger.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L31
```
```
File:: L1/L2OutputOracle.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L105
```
```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L156
```

### [Low-02] Absence of `_disableInitializers()` in `constructor` which inheriting Openzeppelin `Initializable`

```
File:: L1/L1CrossDomainMessenger.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L32
```
```
File:: L1/L2OutputOracle.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L111
```
```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L159
```
```
File:: L1/SystemConfig.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L93
```

### [Low-03] Some Functions should be `payable` which interacting with `ETH` like Sending `ETH` to others

```
File:: L1/L1CrossDomainMessenger.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#L45-L52
```

### [Low-04] Absence of `_minGasLimit` value check on first place before passing this as parameter to other function.

```solidity
  function depositETH(uint32 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {
        _initiateETHDeposit(msg.sender, msg.sender, _minGasLimit, _extraData); 
    }
```
There should be a check which ensure that entered `_minGasLimit` should be equal or Greater than gas consumption according to `_extraData` passed.
```
File:: L1/L1StandardBridge.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L119-L121
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L142
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L265
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L294
```

### [Low-05] No Upper limit present while setting Uints (like `timestamp`, `fee` etc)
While setting timestamp if no upper limit present then time could set to Very far or very past due to human error, if this state variable `immutable` then will cost to redeploy contracts again.

```
File:: L1/L2OutputOracle.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L99
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L105
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L125
```

### [Low-06] `getL2Output()` will give Default value result in some cases. 
There is a function `deleteL2Outputs()` which deletes `_l2OutputIndex` from `l2Outputs[]`
If a perticular index deleted then its result will return defaults value, same with the case if index exceed the array length.

So when querying via `getL2Output()` its should revert for deleted `_l2OutputIndex`
```solidity
    function getL2Output(uint256 _l2OutputIndex)
        external
        view
        returns (Types.OutputProposal memory)
    {
        return l2Outputs[_l2OutputIndex]; 
    }
```

```
File:: L1/L2OutputOracle.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L239-L245
```

### [Low-07] `ETH` May stuck in some contract files
Some contracts implemented `payable` or `receive()` which helps contracts to accept `eth` but doesn't implemented any rescue functions in that contract

```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L215-L217
```

### [Low-08] Some Parameter checks could be done some step above to detect contract failures
```solidity
    function initialize(
        address _owner,
        uint256 _overhead,
        uint256 _scalar,
        bytes32 _batcherHash,
        uint64 _gasLimit,
        address _unsafeBlockSigner,
        ResourceMetering.ResourceConfig memory _config
    ) public initializer {
        __Ownable_init(); // @audit owner can re-nounce ownership
        transferOwnership(_owner); // @audit Ownership transfer 2 step
        overhead = _overhead;
        scalar = _scalar;
        batcherHash = _batcherHash;
+       require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low");
        gasLimit = _gasLimit;
        _setUnsafeBlockSigner(_unsafeBlockSigner);
        _setResourceConfig(_config);
-       require(_gasLimit >= minimumGasLimit(), "SystemConfig: gas limit too low"); 
    }
```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L142

and similar like

```solidity
        // Set the l2Sender so contracts know who triggered this withdrawal on L2.
        l2Sender = _tx.sender;

        // Trigger the call to the target contract. We use a custom low level method
        // SafeCall.callWithMinGas to ensure two key properties
        //   1. Target contracts cannot force this call to run out of gas by returning a very large
        //      amount of data (and this is OK because we don't care about the returndata here).
        //   2. The amount of gas provided to the execution context of the target is at least the
        //      gas limit specified by the user. If there is not enough gas in the current context
        //      to accomplish this, `callWithMinGas` will revert.
        bool success = SafeCall.callWithMinGas(_tx.target, _tx.gasLimit, _tx.value, _tx.data);
+        if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) {
+            revert("OptimismPortal: withdrawal failed");
+        }
        // Reset the l2Sender back to the default value.
        l2Sender = Constants.DEFAULT_L2_SENDER;

        // All withdrawals are immediately finalized. Replayability can
        // be achieved through contracts built on top of this contract
        emit WithdrawalFinalized(withdrawalHash, success);

        // Reverting here is useful for determining the exact gas cost to successfully execute the
        // sub call to the target contract if the minimum gas limit specified by the user would not
        // be sufficient to execute the sub call.
-        if (success == false && tx.origin == Constants.ESTIMATION_ADDRESS) { // @audit could be done above 
-            revert("OptimismPortal: withdrawal failed");
-        }
```
```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L403-L419
```
```
File:: L1/SystemConfig.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L142
```

### [Low-09] `abi.encodePacked()` Should not used with dynamic inputed parameters
This code lead to signature mallability

```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L472-L478
```

### [Low-10] `_gasLimit` Should check again `msg.value` as well
Should be a check which ensure `_gasLimit` passed in function parameter also match with user `msg.value` send.
```solidity
        require(
            _gasLimit >= minimumGasLimit(uint64(_data.length)), 
            "OptimismPortal: gas limit too small"
        );
```

```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L452-L455
```

### [Low-11] `Zero Address` check should be preform with `assembly`

```
File:: L1/OptimismPortal.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L445
```

### [Low-12] Instead of `OwnableUpgradeable.sol`, `Ownable2Upgradeable.sol` Should be used 

```
File: L1/SystemConfig.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L6
```

### [Low-13] Owner can `renounceOwnerShip()`, So It should be `disabled` in `constructor`

```
File: L1/SystemConfig.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L134
```

### [Low-14] Transfer of `ownership` should be a 2-step process

```
File: L1/SystemConfig.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L135
```

### [Low-15] Precision loss due to Division before Multiplication
```solidity
        require(
            ((_config.maxResourceLimit / _config.elasticityMultiplier) *
                _config.elasticityMultiplier) == _config.maxResourceLimit,// @audit precision loss
            "SystemConfig: precision loss with target resource limit"
        );
```
```
File: L1/SystemConfig.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L289-L293
```

### [Low-16] `L1ERC721Bridge.sol` Can't able to handel coming `NFT`
No Nft receiver function implemented in that contract.

```
File: L1/L1ERC721Bridge.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol
```

### [Low-17] Before depositing token to `L1ERC721Bridge.sol`, the function `_initiateBridgeERC721()` should check Nft previously deposited or not
```solidity
    function _initiateBridgeERC721(
        address _localToken,
        address _remoteToken,
        address _from,
        address _to,
        uint256 _tokenId,
        uint32 _minGasLimit,
        bytes calldata _extraData
    ) internal override {
        require(_remoteToken != address(0), "L1ERC721Bridge: remote token cannot be address(0)");

        // Construct calldata for _l2Token.finalizeBridgeERC721(_to, _tokenId)
        bytes memory message = abi.encodeWithSelector(
            L2ERC721Bridge.finalizeBridgeERC721.selector,
            _remoteToken,
            _localToken,
            _from,
            _to,
            _tokenId,
            _extraData
        );

        // Lock token into bridge
+       require(!deposits[_localToken][_remoteToken][_tokenId], `Error`);
        deposits[_localToken][_remoteToken][_tokenId] = true;
        IERC721(_localToken).transferFrom(_from, address(this), _tokenId); 

        // Send calldata into L2
        MESSENGER.sendMessage(OTHER_BRIDGE, message, _minGasLimit);
        emit ERC721BridgeInitiated(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);
    }
```
```
File: L1/L1ERC721Bridge.sol
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L100
```

### [Low-]

```
File: 
```

### [Low-]

```
File: 
```

### [Low-]

```
File: 
```

### [Low-]

```
File: 
```