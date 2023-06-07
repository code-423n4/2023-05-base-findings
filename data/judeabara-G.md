### Table of contents

- [Summary](#summary)
- [Gas Optimizations](#gas-optimizations)
  - [G-01 Emit events outside of the function body](#g-01-emit-events-outside-of-the-function-body)
  - [G-02 Use uint256 instead of uint32 for gas limit parameters](#g-02-use-uint256-instead-of-uint32-for-gas-limit-parameters)
  - [G-03 Avoid unnecessary type conversions](#g-03-avoid-unnecessary-type-conversions)
  - [G-04 Avoid unnecessary storage slot updates](#g-04-avoid-unnecessary-storage-slot-updates)
  - [G-05 Avoid Redundant Functions](#g-05-avoid-redundant-functions)
  - [G-06 Minimize unnecessary variable creation](#g-06-minimize-unnecessary-variable-creation)

# Summary

Coinbase’s Layer 2 network, known as Base, is a channel that sits on top of a Layer 1 (L1) blockchain network. L2 networks are designed to improve blockchain scalability by reducing the number of nodes or participants required to validate transactions within the L2 network, thereby reducing the time it takes to achieve consensus. It is a secure, low-cost, developer-friendly Ethereum L2 built to bring the next billion users on-chain.
It is built on the MIT-licensed OP Stack, in collaboration with Optimism. Coinbase is joining as the second Core Dev team working on the OP Stack to ensure it’s a public good available to everyone.

The contest focuses on key components such as L1 Contracts, L2 Contracts, op-node, and op-geth. However, my primary focus for gas optimization lies on the L1 Contracts and L2 Contracts.

NB: In some cases, only the updated version of the code is shown while link to the original code that is affected is provided.

# Gas Optimizations

### Gas Optimizations List

| Number | Optimization Details                                       | Instances |
| :----: | :--------------------------------------------------------- | :-------: |
| [G-01] | Emit events outside of the function body                   |     1     |
| [G-02] | Use `uint256` instead of `uint32` for gas limit parameters |     2     |
| [G-03] | Avoid unnecessary type conversions                         |     1     |
| [G-04] | Avoid unnecessary storage slot updates                     |     1     |
| [G-05] | Avoid Redundant Functions                                  |     1     |
| [G-06] | Minimize unnecessary variable creation                     |     1     |

## [G-01] Emit events outside of the function body

Emitting events can be expensive in terms of gas. To optimize gas usage, you can emit the event outside of the function body using a separate function. This allows the function to complete its execution without waiting for the event to be processed. You can create a separate internal function to emit the event and call it at the end of the `finalizeBridgeERC721` function.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol#L33-L72

```diff
diff --git a/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol
--- a/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol
+++ b/packages/contracts-bedrock/contracts/L1/L1ERC721Bridge.sol
@@ -33,11 +33,51 @@ /**
-    /**
-     * @notice Completes an ERC721 bridge from the other domain and sends the ERC721 token to the
-     *         recipient on this domain.
-     *
-     * @param _localToken  Address of the ERC721 token on this domain.
-     * @param _remoteToken Address of the ERC721 token on the other domain.
-     * @param _from        Address that triggered the bridge on the other domain.
-     * @param _to          Address to receive the token on this domain.
-     * @param _tokenId     ID of the token being deposited.
-     * @param _extraData   Optional data to forward to L2. Data supplied here will not be used to
-     *                     execute any code on L2 and is only emitted as extra data for the
+     function emitERC721BridgeFinalized(
+         address _localToken,
+         address _remoteToken,
+         address _from,
+         address _to,
+         uint256 _tokenId,
+         bytes calldata _extraData
+     ) internal {
+         emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);
+     }
+    /**
+     * @notice Completes an ERC721 bridge from the other domain and sends the ERC721 token to the
+     *         recipient on this domain.
+     *
+     * @param _localToken  Address of the ERC721 token on this domain.
+     * @param _remoteToken Address of the ERC721 token on the other domain.
+     * @param _from        Address that triggered the bridge on the other domain.
+     * @param _to          Address to receive the token on this domain.
+     * @param _tokenId     ID of the token being deposited.
+     * @param _extraData   Optional data to forward to L2. Data supplied here will not be used to
+     *                     execute any code on L2 and is only emitted as extra data for the
+     *                     convenience of off-chain tooling.
+     */
+    function finalizeBridgeERC721(
+        address _localToken,
+        address _remoteToken,
+        address _from,
+        address _to,
+        uint256 _tokenId,
+        bytes calldata _extraData
+    ) external onlyOtherBridge {
+        require(_localToken != address(this), "L1ERC721Bridge: local token cannot be self");
+
+        // Checks that the L1/L2 NFT pair has a token ID that is escrowed in the L1 Bridge.
+        require(
+            deposits[_localToken][_remoteToken][_tokenId] == true,
+            "L1ERC721Bridge: Token ID is not escrowed in the L1 Bridge"
+        );
+
+        // Mark that the token ID for this L1/L2 token pair is no longer escrowed in the L1
+        // Bridge.
+        deposits[_localToken][_remoteToken][_tokenId] = false;
+
+        // When a withdrawal is finalized on L1, the L1 Bridge transfers the NFT to the
+        // withdrawer.
+        IERC721(_localToken).safeTransferFrom(address(this), _to, _tokenId);
+
+        // slither-disable-next-line reentrancy-events
-        emit ERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);
+        emitERC721BridgeFinalized(_localToken, _remoteToken, _from, _to, _tokenId, _extraData);
+    }
```

## [G-02] Use uint256 instead of uint32 for gas limit parameters

In the contract, the `depositETH` and `depositETHTo` functions have a parameter named `_minGasLimit` of type `uint32`. Consider changing it to `uint256` to avoid potential conversion operations and optimize gas usage.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L119-L121

```solidity
File: /packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
119:    function depositETH(uint256 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {
120:        _initiateETHDeposit(msg.sender, msg.sender, _minGasLimit, _extraData);
121:    }
```

```diff
diff --git a/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
--- a/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
+++ b/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
@@ -119,1 +119,1 @@ function depositETH(uint32 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {
-    function depositETH(uint32 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {
+    function depositETH(uint256 _minGasLimit, bytes calldata _extraData) external payable onlyEOA {
        _initiateETHDeposit(msg.sender, msg.sender, _minGasLimit, _extraData);
    }
```

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L137-L143

```solidity
File: /packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
137:    function depositETHTo(
138:        address _to,
139:        uint32 _minGasLimit,
140:        bytes calldata _extraData
141:    ) external payable {
142:        _initiateETHDeposit(msg.sender, _to, _minGasLimit, _extraData);
143:    }
```

```diff
diff --git a/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
--- a/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
+++ b/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol
@@ -137,1 +137,1 @@ function depositETHTo( address _to, uint32 _minGasLimit, bytes calldata _extraData) external payable {
    function depositETHTo(
        address _to,
-        uint32 _minGasLimit,
+        uint256 _minGasLimit,
        bytes calldata _extraData
    ) external payable {
        _initiateETHDeposit(msg.sender, _to, _minGasLimit, _extraData);
    }
```

## [G-03] Avoid unnecessary type conversions

There are a few unnecessary type conversions in the code. For instance, in the require statement, you can directly compare `params.prevBoughtGas` and `config.maxResourceLimit` without converting them to `int256`. Similarly, you can directly use newBaseFee as a `uint128` without converting it to `uint256`. By implementing these optimizations, you can potentially reduce the gas consumption of the `_metered` function and improve its efficiency.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol#L92-L164

```diff
diff --git a/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol
--- a/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol
+++ b/packages/contracts-bedrock/contracts/L1/ResourceMetering.sol
@@ -92,14 +92,14 @@ function _metered(uint64 _amount, uint256 _initialGas) internal {
    function _metered(uint64 _amount, uint256 _initialGas) internal {
        // Update block number and base fee if necessary.
        uint256 blockDiff = block.number - params.prevBlockNum;

        ResourceConfig memory config = _resourceConfig();
-        int256 targetResourceLimit = int256(uint256(config.maxResourceLimit)) /
-            int256(uint256(config.elasticityMultiplier));
+        int256 targetResourceLimit = int256(config.maxResourceLimit) / int256(config.elasticityMultiplier);
+

        if (blockDiff > 0) {
            // Handle updating EIP-1559 style gas parameters. We use EIP-1559 to restrict the rate
            // at which deposits can be created and therefore limit the potential for deposits to
            // spam the L2 system. Fee scheme is very similar to EIP-1559 with minor changes.
-            int256 gasUsedDelta = int256(uint256(params.prevBoughtGas)) - targetResourceLimit;
+            int256 gasUsedDelta = int256(params.prevBoughtGas) - targetResourceLimit;
-            int256 baseFeeDelta = (int256(uint256(params.prevBaseFee)) * gasUsedDelta) /
-                (targetResourceLimit * int256(uint256(config.baseFeeMaxChangeDenominator)));
+            int256 baseFeeDelta = (int256(params.prevBaseFee) * gasUsedDelta) /
+                (targetResourceLimit * int256(config.baseFeeMaxChangeDenominator));

            // Update base fee by adding the base fee delta and clamp the resulting value between
            // min and max.
-            int256 newBaseFee = Arithmetic.clamp({
-                _value: int256(uint256(params.prevBaseFee)) + baseFeeDelta,
-                _min: int256(uint256(config.minimumBaseFee)),
-                _max: int256(uint256(config.maximumBaseFee))
-            });
+            int256 newBaseFee = Arithmetic.clamp({
+                _value: int256(params.prevBaseFee) + baseFeeDelta,
+                _min: int256(config.minimumBaseFee),
+                _max: int256(config.maximumBaseFee)
+            });

            // If we skipped more than one block, we also need to account for every empty block.
            // Empty block means there was no demand for deposits in that block, so we should
            // reflect this lack of demand in the fee.
            if (blockDiff > 1) {
                // Update the base fee by repeatedly applying the exponent 1-(1/change_denominator)
                // blockDiff - 1 times. Simulates multiple empty blocks. Clamp the resulting value
                // between min and max.
                newBaseFee = Arithmetic.clamp({
                    _value: Arithmetic.cdexp({
                        _coefficient: newBaseFee,
                        _denominator: int256(uint256(config.baseFeeMaxChangeDenominator)),
                        _exponent: int256(blockDiff - 1)
                    }),
-                    _min: int256(uint256(config.minimumBaseFee)),
-                    _max: int256(uint256(config.maximumBaseFee))
+                    _min: int256(config.minimumBaseFee),
+                    _max: int256(config.maximumBaseFee)
                });
            }

            // Update new base fee, reset bought gas, and update block number.
-            params.prevBaseFee = uint128(uint256(newBaseFee));
+            params.prevBaseFee = uint128(newBaseFee);
            params.prevBoughtGas = 0;
            params.prevBlockNum = uint64(block.number);
        }

        // Make sure we can actually buy the resource amount requested by the user.
        params.prevBoughtGas += _amount;
        require(
-            int256(uint256(params.prevBoughtGas)) <= int256(uint256(config.maxResourceLimit)),
+            params.prevBoughtGas <= config.maxResourceLimit,
            "ResourceMetering: cannot buy more gas than available gas limit"
        );

        // Determine the amount of ETH to be paid.
        uint256 resourceCost = uint256(_amount) * uint256(params.prevBaseFee);

        // We currently charge for this ETH amount as an L1 gas burn, so we convert the ETH amount
        // into gas by dividing by the L1 base fee. We assume a minimum base fee of 1 gwei to avoid
        // division by zero for L1s that don't support 1559 or to avoid excessive gas burns during
        // periods of extremely low L1 demand. One-day average gas fee hasn't dipped below 1 gwei
        // during any 1 day period in the last 5 years, so should be fine.
        uint256 gasCost = resourceCost / Math.max(block.basefee, 1 gwei);

        // Give the user a refund based on the amount of gas they used to do all of the work up to
        // this point. Since we're at the end of the modifier, this should be pretty accurate. Acts
        // effectively like a dynamic stipend (with a minimum value).
        uint256 usedGas = _initialGas - gasleft();
        if (gasCost > usedGas) {
            Burn.gas(gasCost - usedGas);
        }
    }
```

In this modified version, the unnecessary type conversions have been removed. The variables `targetResourceLimit`, `baseFeeDelta`, `newBaseFee`, and `resourceCost` now use their respective types directly without explicit type conversions.

## [G-04] Avoid unnecessary storage slot updates

In the `_setUnsafeBlockSigner` internal function, you can check if the new value is different from the current value before updating the storage slot. This way, you can avoid unnecessary storage writes if the value hasn't changed.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L232-L237

```solidity
File: /packages/contracts-bedrock/contracts/L1/SystemConfig.sol
232:    function _setUnsafeBlockSigner(address _unsafeBlockSigner) internal {
233:        bytes32 slot = UNSAFE_BLOCK_SIGNER_SLOT;
234:        assembly {
235:            sstore(slot, _unsafeBlockSigner)
236:        }
237:    }
```

```diff
diff --git a/packages/contracts-bedrock/contracts/L1/SystemConfig.sol
--- a/packages/contracts-bedrock/contracts/L1/SystemConfig.sol
+++ b/packages/contracts-bedrock/contracts/L1/SystemConfig.sol
@@ -232,3 +232,11 @@ function _setUnsafeBlockSigner(address _unsafeBlockSigner) internal {
    function _setUnsafeBlockSigner(address _unsafeBlockSigner) internal {
        bytes32 slot = UNSAFE_BLOCK_SIGNER_SLOT;
-        assembly {
-            sstore(slot, _unsafeBlockSigner)
-        }
+        address currentSigner;
+        assembly {
+            currentSigner := sload(slot)
+        }
+
+        if (currentSigner != _unsafeBlockSigner) {
+            assembly {
+                sstore(slot, _unsafeBlockSigner)
+            }
+        }
+    }
```

In this updated code, we first load the current signer address from the storage slot using assembly. Then, we compare it with the new `_unsafeBlockSigner` value. If they are different, we update the storage slot with the new value using assembly. This way, unnecessary storage writes are avoided if the value hasn't changed.

## [G-05] Avoid Redundant Functions

The `gasPrice()` and `baseFee()` functions return the same value (`block.basefee`). You can remove one of them to eliminate redundancy.

I scanned the contract GasPriceOracle.sol and found out that the function `gasPrice()` is not used therefore it should be removed.

Meanwhile the function `baseFee()` is used in line 94 of the function `l1BaseFee()`.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L93-L95

```solidity
93:    function l1BaseFee() public view returns (uint256) {
94:        return L1Block(Predeploys.L1_BLOCK_ATTRIBUTES).basefee();
95:    }
```

The function `gasPrice()` should be removed. This will reduce the size of the `GasPriceOracle` contract and save some gas during deployment.

## [G-06] Minimize unnecessary variable creation

Having a large number of local variables can impact gas consumption. Therefore it is beneficial to optimize the code to minimize unnecessary variable creation. The `getL1Fee` function is simplified to minimize unnecessary variable creation and reduce the number of multiplications and divisions.

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L43-L50

```solidity
File: /packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol
43:    function getL1Fee(bytes memory _data) external view returns (uint256) {
44:        uint256 l1GasUsed = getL1GasUsed(_data);
45:        uint256 l1Fee = l1GasUsed * l1BaseFee();
46:        uint256 divisor = 10**DECIMALS;
47:        uint256 unscaled = l1Fee * scalar();
48:        uint256 scaled = unscaled / divisor;
49:        return scaled;
50:    }
```

Here's an optimized version of the function:

```solidity
43:   function getL1Fee(bytes memory _data) external view returns (uint256) {
44:       uint256 l1GasUsed = getL1GasUsed(_data);
45:       uint256 l1Fee = (l1GasUsed * l1BaseFee() * scalar()) / (10**DECIMALS);
46:       return l1Fee;
47:   }
```

```diff
diff --git a/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol
--- a/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol
+++ b/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol
@@ -43,8 +43,5 @@ function getL1Fee(bytes memory _data) external view returns (uint256) {
-    function getL1Fee(bytes memory _data) external view returns (uint256) {
-        uint256 l1GasUsed = getL1GasUsed(_data);
-        uint256 l1Fee = l1GasUsed * l1BaseFee();
-        uint256 divisor = 10**DECIMALS;
-        uint256 unscaled = l1Fee * scalar();
-        uint256 scaled = unscaled / divisor;
-        return scaled;
-    }
+    function getL1Fee(bytes memory _data) external view returns (uint256) {
+        uint256 l1GasUsed = getL1GasUsed(_data);
+        uint256 l1Fee = (l1GasUsed * l1BaseFee() * scalar()) / (10**DECIMALS);
+        return l1Fee;
+    }
```

In this optimized version:

- Instead of multiplying l1GasUsed and l1BaseFee first and then multiplying the result by scalar, we perform a single multiplication: (l1GasUsed _ l1BaseFee() _ scalar()).
- We divide once at the end: (l1GasUsed _ l1BaseFee() _ scalar()) / (10\*\*DECIMALS).
- We directly return l1Fee instead of assigning it to a separate variable.
- In the end we were able to minimize the number of local variables that was created: divisor, unscaled and scaled were not created.

By reducing the number of operations, this optimized version reduces gas consumption.
