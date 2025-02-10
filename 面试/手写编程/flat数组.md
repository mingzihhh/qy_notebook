## flat数组

```
function flat(arr){
    let res = []
    arr.forEach(val => {
        if(Array.isArray(val)){
            res = res.concat(flat(val))
        }else{
            res.push(val)
        }
    })
}
```

