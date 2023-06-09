* 1. [L - Lack of check for these values](#L-Lackofcheckforthesevalues)
* 2. [L - Deprecation warning](#L-Deprecationwarning)
* 3. [L - Round up / add 1](#L-Roundupadd1)
* 4. [L - Griefing in Gas Prediction for Bridge under certain conditions](#L-GriefinginGasPredictionforBridgeundercertainconditions)
* 5. [L - BASE GAS should round up by one](#L-BASEGASshouldroundupbyone)
* 6. [L - Comment says 41, code is 42](#L-Commentsays41codeis42)
* 7. [L - If the RECIPIENT performs an SSTORE on Mainnet, the call will fail](#L-IftheRECIPIENTperformsanSSTOREonMainnetthecallwillfail)
* 8. [R - Maybe best to rename to a more descriptive name](#R-Maybebesttorenametoamoredescriptivename)
* 9. [R - Same Error Message](#R-SameErrorMessage)
* 10. [R - Can use a constant](#R-Canuseaconstant)
* 11. [R - Unnecessary function](#R-Unnecessaryfunction)
* 12. [R - Precomputing this is cheaper](#R-Precomputingthisischeaper)
* 13. [R - Note on Spacers](#R-NoteonSpacers)

# Executive Summary

The code is well written and fully tested, the team has shown an exceptional commitment to security practices which should be commended

The below notes are fairly situational, although, due to the higher level of pressure that comes from launching such a protocol, should be considered thoroughly

The following criteria are used for suggested severity:

L - Low Severity -> Some risk
R - Refactoring -> Suggested changes for improvements


##  1. <a name='L-Lackofcheckforthesevalues'></a>L - Lack of check for these values
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#L205-L211

```solidity
    function setGasConfig(uint256 _overhead, uint256 _scalar) external onlyOwner {
        overhead = _overhead;
        scalar = _scalar;

        bytes memory data = abi.encode(_overhead, _scalar);
        emit ConfigUpdate(VERSION, UpdateType.GAS_CONFIG, data);
    }
```

These value can cause issues, esp if 0 or if they end up simplifying to 1, so it should be best to check them

## L - Gas Math looks off
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/SafeCall.sol#L77-L82

```solidity
     *      1.) The 40_000 base buffer is to account for the worst case of the dynamic cost of the
     *          `CALL` opcode's `address_access_cost`, `positive_value_cost`, and
     *          `value_to_empty_account_cost` factors with an added buffer of 5,700 gas. It is
     *          still possible to self-rekt by initiating a withdrawal with a minimum gas limit
     *          that does not account for the `memory_expansion_cost` & `code_execution_cost`
     *          factors of the dynamic cost of the `CALL` opcode.
```

The buffer is not 5.7k but 3.4k

Gas Costs:
address_access_cost = 2.6k
positive_value_cost = 9k
value_to_empty_account_cost = 25k

Source: https://www.evm.codes/

##  2. <a name='L-Deprecationwarning'></a>L - Deprecation warning
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/Burn.sol#L38-L42

```solidity
contract Burner {
    constructor() payable {
        selfdestruct(payable(address(this)));
    }
}
```

This is going away soon

##  3. <a name='L-Roundupadd1'></a>L - Round up / add 1

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L48-L49

```solidity
        uint256 scaled = unscaled / divisor;

```

This would avoid the fee being slightly higher

##  4. <a name='L-GriefinginGasPredictionforBridgeundercertainconditions'></a>L - Griefing in Gas Prediction for Bridge under certain conditions

Because the cost of a zero to non-zero SSTORE is higher than the cost of an SSTORE from non-zero to non-zero

It is possible to grief certain Bridge operations if the gas limit is set with the expected cost of a non-zero to non-zero SLOAD

This can be done in two ways
Grief in a simulation
-> During simulations, transfer a token before the simulation happens, so that values are compute for non-zero to non-zero changes

Order of operations grief:
-> Actually send a Token to ensure simulations are done with non-zero to non-zero changes
-> Have a withdrawal tx that allows to withdraw the tokens (do a full loop of the bridge)
-> Perform the withdrawal before the other person, causing the victim to pay more gas, and potentially bricking their withdrawal

I would say this is still self-rekt because gas costs should be estimated in a pessimistic way, however this type of griefing could cause to serious losses if done well enough.

##  5. <a name='L-BASEGASshouldroundupbyone'></a>L - BASE GAS should round up by one
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L458-L460

```solidity
            ((_minGasLimit * MIN_GAS_DYNAMIC_OVERHEAD_NUMERATOR) / // @audit Mathematically accurate, although could raise by 1(unless am wrong)
                MIN_GAS_DYNAMIC_OVERHEAD_DENOMINATOR) +
```

Should have an extra wei due to rounding

##  6. <a name='L-Commentsays41codeis42'></a>L - Comment says 41, code is 42
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L195-L200

```solidity
    /**
     * @notice Reserve extra slots in the storage layout for future upgrades.
     *         A gap size of 41 was chosen here, so that the first slot used in a child contract
     *         would be a multiple of 50.
     */
    uint256[42] private __gap;
```


##  7. <a name='L-IftheRECIPIENTperformsanSSTOREonMainnetthecallwillfail'></a>L - If the RECIPIENT performs an SSTORE on Mainnet, the call will fail
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/FeeVault.sol#L59-L75

```solidity
    function withdraw() external {
        require(
            address(this).balance >= MIN_WITHDRAWAL_AMOUNT,
            "FeeVault: withdrawal amount must be greater than minimum withdrawal amount"
        );

        uint256 value = address(this).balance;
        totalProcessed += value;

        emit Withdrawal(value, RECIPIENT, msg.sender);

        L2StandardBridge(payable(Predeploys.L2_STANDARD_BRIDGE)).bridgeETHTo{ value: value }(
            RECIPIENT,
            WITHDRAWAL_MIN_GAS,
            bytes("")
        );
    }
```

Because of the fact that the WITHDRAWAL_MIN_GAS is 35k, removing 21k leaves us with 14k, which wouldn't be sufficient for a zero to non-zero SSTORE

##  8. <a name='R-Maybebesttorenametoamoredescriptivename'></a>R - Maybe best to rename to a more descriptive name
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/L1Block.sol#L39-L40

```solidity
    bytes32 public hash;

```


##  9. <a name='R-SameErrorMessage'></a>R - Same Error Message
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/libraries/Bytes.sol#L27-L28

```solidity
            require(_length + 31 >= _length, "slice_overflow");
            require(_start + _length >= _start, "slice_overflow");
```

Consider renaming one error to help with debugging

##  10. <a name='R-Canuseaconstant'></a>R - Can use a constant
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L461-L462

```solidity
        require(_data.length <= 120_000, "OptimismPortal: data too large");

```
For example MAX_DATA_LENGTH

##  11. <a name='R-Unnecessaryfunction'></a>R - Unnecessary function
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L215-L217

```solidity
    function donateETH() external payable {
        // Intentionally empty.
    }
```

Since you will not migrate, you can remove this

##  12. <a name='R-Precomputingthisischeaper'></a>R - Precomputing this is cheaper
https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L2/GasPriceOracle.sol#L46-L47

```solidity
        uint256 divisor = 10**DECIMALS;

```




##  13. <a name='R-NoteonSpacers'></a>R - Note on Spacers

https://github.com/ethereum-optimism/optimism/tree/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L10-L103

```solidity
/**
 * @custom:legacy
 * @title CrossDomainMessengerLegacySpacer0
 * @notice Contract only exists to add a spacer to the CrossDomainMessenger where the
 *         libAddressManager variable used to exist. Must be the first contract in the inheritance
 *         tree of the CrossDomainMessenger.
 */
contract CrossDomainMessengerLegacySpacer0 {
    /**
     * @custom:legacy
     * @custom:spacer libAddressManager
     * @notice Spacer for backwards compatibility.
     */
    address private spacer_0_0_20;
}

/**
 * @custom:legacy
 * @title CrossDomainMessengerLegacySpacer1
 * @notice Contract only exists to add a spacer to the CrossDomainMessenger where the
 *         PausableUpgradable and OwnableUpgradeable variables used to exist. Must be
 *         the third contract in the inheritance tree of the CrossDomainMessenger.
 */
contract CrossDomainMessengerLegacySpacer1 {
    /**
     * @custom:legacy
     * @custom:spacer ContextUpgradable's __gap
     * @notice Spacer for backwards compatibility. Comes from OpenZeppelin
     *         ContextUpgradable.
     *
     */
    uint256[50] private spacer_1_0_1600;

    /**
     * @custom:legacy
     * @custom:spacer OwnableUpgradeable's _owner
     * @notice Spacer for backwards compatibility.
     *         Come from OpenZeppelin OwnableUpgradeable.
     */
    address private spacer_51_0_20;

    /**
     * @custom:legacy
     * @custom:spacer OwnableUpgradeable's __gap
     * @notice Spacer for backwards compatibility. Comes from OpenZeppelin
     *         OwnableUpgradeable.
     */
    uint256[49] private spacer_52_0_1568;

    /**
     * @custom:legacy
     * @custom:spacer PausableUpgradable's _paused
     * @notice Spacer for backwards compatibility. Comes from OpenZeppelin
     *         PausableUpgradable.
     */
    bool private spacer_101_0_1;

    /**
     * @custom:legacy
     * @custom:spacer PausableUpgradable's __gap
     * @notice Spacer for backwards compatibility. Comes from OpenZeppelin
     *         PausableUpgradable.
     */
    uint256[49] private spacer_102_0_1568;

    /**
     * @custom:legacy
     * @custom:spacer ReentrancyGuardUpgradeable's `_status` field.
     * @notice Spacer for backwards compatibility.
     */
    uint256 private spacer_151_0_32;

    /**
     * @custom:legacy
     * @custom:spacer ReentrancyGuardUpgradeable's __gap
     * @notice Spacer for backwards compatibility.
     */
    uint256[49] private spacer_152_0_1568;

    /**
     * @custom:legacy
     * @custom:spacer blockedMessages
     * @notice Spacer for backwards compatibility.
     */
    mapping(bytes32 => bool) private spacer_201_0_32;

    /**
     * @custom:legacy
     * @custom:spacer relayedMessages
     * @notice Spacer for backwards compatibility.
     */
    mapping(bytes32 => bool) private spacer_202_0_32;
}

```

Since you are deploying fresh, spacers are probably not necessary

That said, you've confirmed the approach of one-codebase for the OP Stack, so it may be best to keep them, although not used






