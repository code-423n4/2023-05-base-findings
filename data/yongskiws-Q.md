## `initialize()` function can be called by anybody or possible DOS

The `initialize()` function is a common function used in smart contracts to initialize the state variables of the contract. It is typically called only once during the deployment of the contract and is meant to set the initial values for the contract's state variables.

However, if the `initialize()` function is not properly secured, anyone can call it even after the contract has already been initialized. This can cause unexpected behavior or even create security vulnerabilities in the contract.

Furthermore, if the `initialize()` function modifies the state variable owner, then anyone who calls the function will become the new owner of the contract, which can give them full control over the contract. This is because the owner state variable typically has special permissions or privileges within the contract.

Therefore, it is important to ensure that the `initialize()` function is properly secured and only callable by authorized parties, such as the contract owner or a specific set of authorized addresses. This can help prevent unauthorized modifications to the contract's state variables and protect against potential security vulnerabilities.

``` solidity
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#LL38C1-L40C6

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L120-L131

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#L165-L169

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/SystemConfig.sol#LL125C1-L143C6

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/OptimismPortal.sol#LL165C1-L169C6

https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L1CrossDomainMessenger.sol#LL38C1-L40C6
```

## Recommended Mitigation Steps
Add a control that makes initialize() only call the Deployer Contract or EOA
EG.
``` solidity
if (msg.sender != DEPLOYER_ADDRESS) {
        revert NotDeployer();
}
```


## relayMessage calculation of the gas includes L1CrossDomainMessenger 

references : https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/specs/messengers.md

consumption of messages sent via CrossDomainMessenger, which includes L1CrossDomainMessenger and L2CrossDomainMessenger. If the "relayMessage" wrapper is not counted in the gas calculation, then the actual gas consumption for sending the message will be higher than expected. This can cause users to pay less for fuel on L1, and L2 blocks can be filled earlier than expected.

In addition, this will also have an impact on gas meters through ResourceMetering, where the gas meter will be lower than the gas actually consumed. Gas pricing mechanisms such as EIP-1559 also do not reflect actual gas demand.




``` solidity
L1CrossDomainMessenger.sol
29:         CrossDomainMessenger(Predeploys.L2_CROSS_DOMAIN_MESSENGER)

L1CrossDomainMessenger.sol
16: contract L1CrossDomainMessenger is CrossDomainMessenger, Semver {

CrossDomainMessenger.sol
259:  function sendMessage(
260:         address _target,
261:         bytes calldata _message,
262:         uint32 _minGasLimit
263:     ) external payable {
264:         // Triggers a message to the other messenger. Note that the amount of gas provided to the
265:         // message is the amount of gas requested by the user PLUS the base gas value. We want to
266:         // guarantee the property that the call to the target contract will always have at least
267:         // the minimum gas limit specified by the user.
268:         _sendMessage(
269:             OTHER_MESSENGER,
270:             baseGas(_message, _minGasLimit),
271:             msg.value,

@audit cross-chain messages

If there is a problem with the calculation of the gas consumption, an improvement is needed to ensure that the "relayMessage" wrapper is also accounted for in the proper gas calculation. Thus, the fuel consumption required to send messages will be as expected, and users can pay for fuel according to actual consumption.

consider to address these issues so that the gas mechanism and gas pricing can function accurately and according to actual gas demand.

272:             abi.encodeWithSelector(
273:                 this.relayMessage.selector,
274:                 messageNonce(),
275:                 msg.sender,
276:                 _target,
277:                 msg.value,
278:                 _minGasLimit,
279:                 _message
280:             )

281:         );
```

``` solidity

453:     function baseGas(bytes calldata _message, uint32 _minGasLimit) public pure returns (uint64) {
454:         return
455:             // Constant overhead
456:             RELAY_CONSTANT_OVERHEAD +
457:             // Calldata overhead
458:             (uint64(_message.length) * MIN_GAS_CALLDATA_OVERHEAD) +
459:             // Dynamic overhead (EIP-150)
460:             ((_minGasLimit * MIN_GAS_DYNAMIC_OVERHEAD_NUMERATOR) /
461:                 MIN_GAS_DYNAMIC_OVERHEAD_DENOMINATOR) +
462:             // Gas reserved for the worst-case cost of 3/5 of the `CALL` opcode's dynamic gas
463:             // factors. (Conservative)
464:             RELAY_CALL_OVERHEAD +
465:             // Relay reserved gas (to ensure execution of `relayMessage` completes after the
466:             // subcontext finishes executing) (Conservative)
467:             RELAY_RESERVED_GAS +
468:             // Gas reserved for the execution between the `hasMinGas` check and the `CALL`
469:             // opcode. (Conservative)
470:             RELAY_GAS_CHECK_BUFFER;
471:     }
472: 

```

There are several other constants used in gas calculations, such as RELAY_CALL_OVERHEAD, RELAY_RESERVED_GAS, and RELAY_GAS_CHECK_BUFFER. If an attacker can change these values, they can affect the amount of gas returned by the baseGas() function and cause system instability.

Consider Excessive Gas Usage: If the amount of gas supplied by the user exceeds the amount of gas required by this function, it may result in gas wastage.

it is important to ensure that constant values ​​used in gas calculations cannot be manipulated by non-providers. This can be done by securing the contract and implementing mechanisms that ensure the integrity of those constants, such as using a variable that can only be set once during contract execution or using a digital signature mechanism to verify that values ​​have not changed.


