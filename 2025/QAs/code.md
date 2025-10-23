## call, apply, bind
```
Function.prototype.newCall = function(ctx, ...args) {

}
```

## debounce
```
function debounce(fn, delay = 200) {
    let timer = null
    return function(...args) {
        let ctx = this
        clearTimeout(timer)
        timer = setTimeout(() => {
            fn.apply(this, args)
        }, delay)
    }
}
```

