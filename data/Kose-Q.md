# QA Report
## Issue List
| Number |Issues Details|Instances|
|:--:|:-------|:--:|
|[L-01]|abi.encodePacked() should not be used with dynamic types when passing the result toa hash function such as keccak256()| 1 |
|[NC-01]|Floating Pragma | 3 |
|[NC-02]|Use a More Recent Version of Solidity | 19 |
|[NC-03]|Function Orders | 3 |



Total 4 issues 26 instances

## Findings

### [L-01] Add Event-Emit for State Changes

Using ```abi.encodePacked()``` with variable length arguments can lead to a hash collision. Since ```xDomainCalldata``` is type ```bytes``` , it is recommended to use ```abi.encode``` instead.
For more information:
[SWC-133](https://swcregistry.io/docs/SWC-133)

Files and Codes:

[L2CrossDomainMessenger.sol](https://github.com/ethereum-optimism/optimism/blob/daaf917b201aae021fb10da03ef1262a13e00353/packages/contracts/contracts/L2/messaging/L2CrossDomainMessenger.sol#L152)
```solidity
bytes32 relayId = keccak256(abi.encodePacked(xDomainCalldata, msg.sender, block.number));
```
1 file 1 instance


### [NC-01] Missing Event for initialize

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively. For more detail:
[SWC-103](https://swcregistry.io/docs/SWC-103)

Files and Codes:

[CrossDomainOwnable.sol](https://github.com/ethereum-optimism/optimism/blob/efea631cf19d57d3a64374cb81ce545f958bd38f/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable.sol#L2)
[CrossDomainOwnable2.sol](https://github.com/ethereum-optimism/optimism/blob/efea631cf19d57d3a64374cb81ce545f958bd38f/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable2.sol#L4)
[CrossDomainOwnable3.sol](https://github.com/ethereum-optimism/optimism/blob/efea631cf19d57d3a64374cb81ce545f958bd38f/packages/contracts-bedrock/contracts/L2/CrossDomainOwnable3.sol#L2)
```solidity
pragma solidity ^0.8.0;
```
3 file 3 instances

### [NC-02] Use a More Recent Version of Solidity


For security, it is best practice to use the latest Solidity version.

For the security fix list in the versions: https://github.com/ethereum/solidity/blob/develop/Changelog.md
Recommendation

Old version of Solidity is used. Newer version can be used (0.8.20).
Currently all contracts uses 0.8.15 except the ones mentioned in NC-01 (Which they also will be compiled to 0.8.15)

### [NC-03] Function Orders

Functions should be grouped according to their visibility and ordered:

    constructor
    receive function (if exists)
    fallback function (if exists)
    external
    public
    internal
    private
    within a grouping, place the view and pure functions last

In [OptimisimPortal.sol](https://github.com/ethereum-optimism/optimism/blob/efea631cf19d57d3a64374cb81ce545f958bd38f/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol) this order is not preserved.

1 file 1 instance