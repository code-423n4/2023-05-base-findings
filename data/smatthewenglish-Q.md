In `OptimismPortal.sol` the function `donateETH()` could log an event of the `msg.value` like so:
```
event ReceivedDonateETH(address from, uint256 amount);

function donateETH() external payable {
    // Intentionally empty.
    emit ReceivedDonateETH(msg.sender, msg.value);
}
```