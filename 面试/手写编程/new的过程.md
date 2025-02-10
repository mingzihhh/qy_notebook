## new的过程

`new`操作符做了这些事：

- 它创建了一个全新的对象。
- 它会被执行`[[Prototype]]`（也就是`__proto__`）链接。
- 它使`this`指向新创建的对象。。
- 通过`new`创建的每个对象将最终被`[[Prototype]]`链接到这个函数的`prototype`对象上。
- 如果函数没有返回对象类型`Object`(包含`Functoin, Array, Date, RegExg, Error`)，那么`new`表达式中的函数调用将返回该对象引用。

```
function new(func){
	let obj = {}
	if(func.prototype != null){
        obj.__proto__ = func.prototype 
	}
	//让func中的this指向obj，并执行func的函数体
	let ret = func.apply(obj, Array.prototype.slice.call(arguments,1))
	if((typeof ret === 'object'||typeof ret === 'function')&& ret!== null){
        return ret
	}
	return obj
}
```

