# Gas optimization 

# [G-01] Modifier can save gas

the use of modifier can save gas and make your code more clean and readeable, see the next lines in contract L2OutputOracle.sol
```
file: /L1/L2OutputOracle.sol
function deleteL2Outputs(uint256 _l2OutputIndex) external {
        require( 
            msg.sender == CHALLENGER,
            "L2OutputOracle: only the challenger address can delete outputs"
        );

```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L141-L145

```
function proposeL2Output(
        bytes32 _outputRoot,
        uint256 _l2BlockNumber,
        bytes32 _l1BlockHash,
        uint256 _l1BlockNumber
    ) external payable {
        require( //@audit modifier Onlyproposer
            msg.sender == PROPOSER,
            "L2OutputOracle: only the proposer address can propose new outputs"
        );
```
https://github.com/ethereum-optimism/optimism/blob/382d38b7d45bcbf73cb5e1e3f28cbd45d24e8a59/packages/contracts-bedrock/contracts/L1/L2OutputOracle.sol#L179-L188

You can create the next modifier:
```
modifier OnlyContract(string memory _actor){
require( 
            msg.sender == _actor,
            "L2OutputOracle: only the actor address can use this function"
        );
}
```
and you can implemented as the below lines:
```
function deleteL2Outputs(uint256 _l2OutputIndex) external OnlyContract(CHALLENGER);

function proposeL2Output(
        bytes32 _outputRoot,
        uint256 _l2BlockNumber,
        bytes32 _l1BlockHash,
        uint256 _l1BlockNumber
    ) external payable OnlyContract(PROPOSER);

```





