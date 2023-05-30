in the function `step3()` in `SystemDictator()` on line 310, instead of iterating like so: 
```
for (uint256 i = 0; i < deprecated.length; i++) {
    config.globalConfig.addressManager.setAddress(deprecated[i], address(0));
}
```
we can iterate like this:
```
for(uint256 i = 0; i < deprecated.length;) {
    config.globalConfig.addressManager.setAddress(deprecated[i], address(0));

    unchecked {
        ++i;
    }
}
```

since there's no chance that `i` will overflow, as it's max value is bounded by `deprecated.length`
