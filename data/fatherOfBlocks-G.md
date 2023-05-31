**L1CrossDomainMessenger**
- L180/187/192/209/304/342 - Instead of checking if a bool is true or false like this: (bool == true), it is less expensive to do it like this: (bool) or (!bool).

- L84/181/188/193/198/266/343 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**L1StandardBridge.sol**
- L226 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**L2OutputOracle.sol**
- L99/102/126/144/150/156/187/192/197/202/216/259/265 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**OptimismPortal.sol**
- L138/388/417 - Instead of checking if a bool is true or false like this: (bool == true), it is less expensive to do it like this: (bool) or (!bool).

- L175/184/253/263/280/304/334/346/354/363/377/383/389/446/454 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**OptimismMintableERC20Factory.sol**
- L94 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**SystemConfig.sol** 
- L270/275/286/292 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**SystemDictator.sol**
- L154/340/384/467 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**GasPriceOracle.sol**
- L43 - The getL1Fee() function could be written more simply like this, generating less gas costs:
        uint256 l1Fee = getL1GasUsed(_data) * l1BaseFee();
        return (l1Fee * scalar()) / (10**DECIMALS);

- L57/103 - The functions gasPrice(), decimals() are public since they are not used in the contract, they should be external.
