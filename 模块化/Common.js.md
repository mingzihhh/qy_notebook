## Common.js: 同步的require

#### 1. 概述

`CommonJS` 是 `Node.js` 原生支持的模块系统，起初为了满足服务端模块化的需求而被引入。`CJS` 使用 `require` 函数来加载模块，用 `module.exports` 或 `exports` 对象将代码暴露为模块。`CommonJS` 模块的特点是同步加载，这意味着代码会在模块被加载完成后立即执行。

每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。**在服务器端，模块的加载是运行时同步加载的；在浏览器端，模块需要提前编译打包处理。**

加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。

#### 2. 特点

- 所有代码都运行在模块作用域，不会污染全局作用域。
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
- 模块加载的顺序，按照其在代码中出现的顺序。

#### 3. 基本语法

- 暴露模块：`module.exports = value`或`exports.xxx = value`
- 引入模块：`require(xxx)`,如果是第三方模块，xxx为模块名；如果是自定义模块，xxx为模块文件路径



```
//模块定义 myModel.js

var name = 'Byron';

function printName(){
    console.log(name);
}

function printFullName(firstName){
    console.log(firstName + name);
}

module.exports = {
    printName: printName,
    printFullName: printFullName
}

//加载模块

var nameModule = require('./myModel.js');

nameModule.printName();
```



**exports 和module.exports 区别和关系**

在使用 `exports` 的时候只能往这个对象里**添加新的属性和方法, 而不能对其直接赋值**. 如果想直接导出一个对象, 或者基础类型值要使用 `module.exports` 对象. 

一个文件被另一个模块引入时, 会被做一些处理.  文件中代码并不被 Node 执行, 而是被打包进一个函数load()中, 然后 Node 执行这个函数。在执行这个`load()`函数前，Node准备好了module变量。

打包函数会被传入 `exports`，`require`，`module`，`__filename`，`__dirname` 这五个参数.  所有的这些参数都在 Node.js 执行函数时赋值, 并且只在当前的函数作用域中有效.  打包函数执行到最后, 返回 `module.exports` 对象.

```\
// 准备module对象:
var module = {
    id: 'hello',
    exports: {}
};
var load = function (exports, module) {
    // 读取的hello.js代码:
    function greet(name) {
        console.log('Hello, ' + name + '!');
    }
    
    module.exports = greet;
    // hello.js代码结束
    return module.exports;
};
var exported = load(module.exports, module);
// 保存module:
save(module, exported);
```

其中：

 **exports 是 module.exports 的引用**，也就是说Node默认给你准备了一个空对象`{}`，这样你可以直接往里面加东西。

 `module` 对象代表被打包进去的代码本身，是 Node在加载js文件前准备的一个变量，并将其传入加载函数，我们在`hello.js`中可以直接使用变量`module`原因就在于它实际上是函数的一个参数

 **`module` 的 `exports` 对象用于指定一个模块的导出内容**。两者指向同一块内存，但是使用并不是完全等价的。

```// 这是可以的:
//这是可以的
exports.name = 'Garrik';
exports.gender = 'Male';

// 这是不可以的: 最后被导出的是module.exports，所以不能直接赋值给exports。
exports = {name: 'Garrik', gender: 'Male'};

// 应该用 module.exports:
module.exports = {name: 'Garrik', gender: 'Male'}

// 只要最后直接给module.exports赋值了，之前绑定的属性都会被覆盖掉。
exports.propA = 'A';
module.exports.propB = 'B';
module.exports = {propC:'C'}; // 只会导出 propC
```

如果我们要输出的是一个函数或数组，那么，只能给`module.exports`赋值。给`exports`赋值是无效的，因为赋值后，`module.exports`仍然是空对象`{}`。

总结：

如果要输出一个键值对象`{}`，可以利用`exports`这个已存在的空对象`{}`，并继续在上面添加新的键值；

如果要输出一个函数或数组，必须直接对`module.exports`对象赋值。



#### CommonJS暴露的模块到底是什么?

 CommonJS规范规定，每个模块内部，module变量代表当前模块。**这个变量是一个对象**，它的exports属性（即module.exports）是对外的接口。**加载某个模块，其实是生成一个对象，加载该对象的module.exports属性**。这种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

require命令用于加载模块文件。**require命令的基本功能是，读入并执行一个JavaScript文件，然后返回该模块的exports对象。如果没有发现指定模块，会报错**。



#### 4. 模块的加载机制

**CommonJS模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值**。



#### 5. CommonJs规范的实现

- node.js 		-server端

- browserify    - 浏览器端

- modules-webmake - 编译成一个 bundle

- wreq -client端

  

**优点：**

- 服务端代码可以被重用
- npm中有大量模块
- 用起来简单方便

**缺点：**

- 同步调用无法在网络环境应用，网络请求是异步的
- 不能并行requrie多个模块



