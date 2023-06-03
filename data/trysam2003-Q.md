### Incomplete "checks-effects-interactions" code practice
checks-effects-interactions code pattern is not completely followed in relayMessage() function. It can open CrossDomainMessenger contract to reentrancy vulnerabilities, though the chance is slim.


***Affected Code Section***
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L303-L413


While it is beneficial to restrict copying returndata in the external contract to prevent direct reentry and exploitation of reentrancy vulnerabilities through return data manipulation, it is essential to emphasize that this measure alone does not provide comprehensive protection against all reentrancy attack scenarios. Other vectors and attack possibilities should be taken into consideration.

In the specific context of the relayMessage() function, where low-level calls to an external address are made using the SafeCall library while restricting copying returndata, it is important to acknowledge that reentrancy attacks can still occur through alternative avenues.

It is crucial to note that during the gas estimation period, multiple transactions triggered by the estimation address can be successful, potentially leading to a reentrancy hack.

Therefore, it is advised that the validation of "ESTIMATION ADDRESS" is done before the low level call. 