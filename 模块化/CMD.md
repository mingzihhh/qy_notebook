## CMD：Common Module Definition

CMD规范专门用于浏览器端，模块的加载是异步的，模块使用时才会加载执行。CMD规范整合了CommonJS和AMD规范的特点。在 Sea.js 中，所有 JavaScript 模块都遵循 CMD模块定义规范。

#### 1)CMD规范基本语法

**定义暴露模块：**

```
//定义没有依赖的模块
define(function(require, exports, module){
  exports.xxx = value
  module.exports = value
})

//定义有依赖的模块
define(function(require, exports, module){
  //引入依赖模块(同步)
  var module2 = require('./module2')
  //引入依赖模块(异步)
    require.async('./module3', function (m3) {
    })
  //暴露模块
  exports.xxx = value
})

```

**引入使用模块：**

```
define(function (require) {
  var m1 = require('./module1')
  var m4 = require('./module4')
  m1.show()
  m4.show()
})
```





#### AMD和CMD

1. AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。
   CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。
2. **AMD是依赖关系前置，CMD是按需加载。**
3. `SeaJS`与`RequireJS`最大的区别:`SeaJS`对模块的态度是懒执行, 而`RequireJS`对模块的态度是预执行



#### AMD与CMD区别

AMD和CMD最大的区别是**对依赖模块的执行时机处理不同**，注意不是加载的时机或者方式不同。

AMD在**加载模块完成后就会执行**该模块，所有模块都加载执行完后会进入require的回调函数，执行主逻辑，这样的效果就是依赖模块的执行顺序和书写顺序不一定一致，看网络速度，哪个先下载下来，哪个先执行，但是主逻辑一定在所有依赖加载完成后才执行

CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，**遇到require语句的时候执行**对应的模块，这样模块的执行顺序和书写顺序是完全一致的。