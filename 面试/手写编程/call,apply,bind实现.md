## call,apply,bind实现

#### call，apply实现

- 首先要先原型上即 Function.prototype上编程
- 需要拿到函数的引用， 在这里是 this
- 让 传入对象.fn = this
- 执行 传入对象.fn(传入参数)
- 返回执行结果

```
Functinon.prototype.call = function(context){
     if(typeof this !== 'function'){
         throw new TypeError('Error')
     }
     let context = context || window
     context.fn = this
     let args = [...arguments].slice(1)
     //let args = Array.prototype.slice.call(arguments,1)
     
     let result = context.fn(...args)
     delete context.fn
     return result
    
}
```



```
Functinon.prototype.apply = function(context){
     if(typeof this !== 'function'){
         throw new TypeError('Error')
     }
     let context = context || window
     context.fn = this
     let args = [...arguments].slice(1)
     
     let result
     //注意参数变化
     if(arguments[1]){
         result = context.fn(...arguments[1])
     }else{
         result = context.fn()
     }
     delete context.fn
     return result
    
}
```

#### bind实现

- 因为bind的使用方法是 某函数.bind(某对象，...剩余参数)
  - 所以需要在Function.prototype 上进行编程
- 将传递的参数中的某对象和剩余参数使用apply的方式在一个回调函数中执行即可
- 要在第一层获取到被绑定函数的this，因为要拿到那个函数用apply

```
Function.prototype.bind = function(context){
      if(typeof this !== 'function'){
         throw new TypeError('Error')
     }
     let _this = this
     let args = [...arguments].slice(1)
     
     return function F(){
         if(this instanceof F){
             return new _this(...args,...arguments)
         }
         return _this.apply(context,args.concat(...arguments))
     }
}
```

