## ES6

##### ES 6 语法知道哪些，分别怎么用

1. let,const =>引申到区别
2. class => 引申到继承
3. promise =>引申到异步



#### var ,let const

- 函数提升优先于变量提升，函数提升会把整个函数挪到作用域顶部，变量提升只会把声明挪到作用域顶部
- `var` 存在提升，我们能在声明之前使用。`let`、`const` 因为暂时性死区的原因，不能在声明前使用
- `var` 在全局作用域下声明变量会导致变量挂载在 `window` 上，其他两者不会
- `let` 和 `const` 作用基本一致，但是后者声明的变量不能再次赋值

#### 原型继承和 Class 继承

背代码

#### 模块化

逻辑上相关的代码组织到同一个包内，隔离、组织复杂的JavaScript代码，我们称为模块化。

- 解决命名冲突
- 提供复用性
- 提高代码可维护性

立即执行函数，通过函数作用域解决了命名冲突、污染全局作用域的问题

ES Module：通过`export`命令显式指定输出的代码，再通过`import`命令输入。对外接口必须与模块内部的变量建立一一对应的动态关系，实时同步

NodeJS：CommonJS，在 Node.js 中, 一个文件就是一个模块. 创建模块的方法就是创建一个文件.Node.js 提供了 `exports` 和 `require` 两个对象，其中 `exports` 用于导出模块, `require` 用于从外部引入另一个模块, 即获取模块的 `exports` 对象.核心模块，文件模块，第三方模块（npm)

#### Proxy

 Proxy 是 ES6 中新增的功能，它可以用来自定义对象中的操作，改变默认行为，提供了一种拦截机制对外界的访问进行过滤和改写。。

```javascript
var proxy = new Proxy(target, handler);
```

（在 Vue3.0 中将会通过 `Proxy` 来替换原本的`Object.defineProperty` 来实现数据响应式。

=> `Proxy` 无需一层层递归为每个属性添加代理，一次即可完成以上操作，性能上更好，并且原本的实现有一些数据更新不能监听到）

#### map,fliter,reduce

`map` 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后放入到新的数组中。`map` 的回调函数接受三个参数，分别是当前索引元素，索引，原数组

`filter` 的作用也是生成一个新数组，在遍历数组的时候将返回值为 `true` 的元素放入新数组，我们可以利用这个函数删除一些不需要的元素

 `reduce` 接受两个参数，分别是回调函数和初始值；回调函数接受四个参数，分别为累计值、当前元素、当前索引、原数组

#### 为什么箭头函数不能当构造函数

因为箭头函数中没有this的绑定机制，构造函数中的 this 需要指向新创建的对象，js 会抛出异常

https://zhuanlan.zhihu.com/p/26540168

#### 箭头函数与普通函数的区别

- 普通function的声明在变量提升中是最高的，箭头函数没有函数提升
- 箭头函数没有属于自己的this，arguments
- 箭头函数不能作为构造函数，不能被new，没有property
- call和apply方法只有参数，没有作用域。如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例。

#### Promise

Promise 是一个异步操作返回的对象，用来传递异步操作的消息。

可以解决回调地狱，合并异步请求，节省时间。

Promise.all()：接收一个数组，数组内是 Promise 实例，必须都成功才表示成功。

Promise.race()：接收一个数组，数组内是 Promise 实例，最早返回的对象成功了，就变为成功态，如果失败了，就改变状态为失败态。

```
function Promise(executor){
   let self = this
   self.status = 'pending' //等待态
   self.value = undefined //成功的返回值
   self.reason = undefined //出错原因
   
   function resolve(value){
   	    if(self.status === 'pending'){
            self.status = 'resolved'
            self.value = value
   	    }  
   }
   
   function reject(value){
       if(self.status === 'pending'){
           self.status = 'rejected'
           self.reason = value
       }
   }
   
   try{
       executor(resolve,reject)
   }catch(e){
       reject(e)//捕获失败返回错误
   }  
}
//onFufiled 成功的回调
//onRejected 失败的回调
Promise.prototype.then = function(onFufilled,onRejected){
    let self = this
    if(self.status === 'resolved'){
        onFufilled(self.value)
    }
    if(self.status === 'rejected'){
        onRejected(self.reason)
    }
}
```





