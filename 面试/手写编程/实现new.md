## 实现new

```
function new(fn, ...args){
    var obj = Object.create(fn.prototype)
    var res = fn.apply(obj, args)
    return typeof res === 'Object'? res：obj
}
```

