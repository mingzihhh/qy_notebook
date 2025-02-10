## JS

#### 原型 / 构造函数 / 实例

原型：用于实现属性继承的对象。在 Firefox 和 Chrome 中，每个`JavaScript`对象中都包含一个`__proto__` (非标准)的属性指向该对象的原型)，可以用`obj.__proto__`进行访问。‘

我们创建的每个函数都有一个 prototype（原型）属性，这个属性是一个指针，指向一个对象，
而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。

构造函数：可以用new来创建一个对象的函数

实例： 通过构造函数和`new`创建出来的对象，便是实例。 

实例.`__proto__` === 原型
原型.constructor === 构造函数
构造函数.prototype === 原型

![img](https://user-gold-cdn.xitu.io/2019/2/14/168e9d9b940c4c6f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### 原型链

**原型链是由原型对象组成**，每个对象都有 `__proto__` 属性，指向了创建该对象的构造函数的原型，`__proto__` 将对象连接起来组成了原型链。

是一个用来**实现继承和共享属性**的有限的对象链。

属性查找：层层上查，直至Object.prototype

属性修改：修改实例对象本身的属性，如果不存在，则进行添加该属性，只有该实例对象能使用；修改原型的属性时，如`b.prototype.x = 2`，会造成所有继承于该对象的实例的属性发生改变。



#### 执行上下文

JS遇到可执行代码时做的准备工作，也是一个对象（全局代码，函数代码，eval代码，对应三种上下文）

执行顺序：

1. 创建 **全局上下文** (global EC)

2. 全局执行上下文 (caller) 逐行 **自上而下** 执行。遇到函数时，**函数执行上下文** (callee) 被`push`到执行栈顶层

3. 函数执行上下文被激活，成为 active EC, 开始执行函数中的代码，caller 被挂起

4. 函数执行完后，callee 被`pop`移除出执行栈，控制权交还全局上下文(caller)，继续执行

包含三部分：变量对象，作用域链，this指向

##### 变量对象

变量对象是与执行上下文相关的数据作用域，存储了在上下文中定义的变量和函数声明。

全局上下文的变量对象初始化是全局对象

变量对象在上下文为 active EC 时，也就是函数执行阶段，变成活动对象，属性值会发生变化

##### 作用域链

作用域是该上下文中声明的 **变量和声明的作用范围**。可分为 **块级作用域** 和 **函数作用域**

特性:

- **声明提前**: 一个声明在函数体内都是可见的, 函数优先于变量

- 非匿名自执行函数，函数变量为 **只读** 状态，无法修改

  ```
  let foo = function() { console.log(1) }
  (function foo() {
      foo = 10  // 由于foo在函数中只为可读，因此赋值无效
      console.log(foo)
  }()) 
  
  // 输出1
  ```

作用域链可以理解为一组对象列表，包含 **父级和自身的变量对象**，因此我们便能通过作用域链访问到父级里声明的变量或者函数。



#### 闭包

闭包属于一种特殊的作用域，称为 **静态作用域**。

它的定义可以理解为: **父函数被销毁** 的情况下，返回出的子函数的`[[scope]]`中仍然保留着父级的单变量对象和作用域链，因此可以继续访问到父级的变量对象，这样的函数称为闭包。

简单来说：**函数，子函数内部能访问到函数外部（父函数）的变量**

作用：间接访问函数内部的变量，保护变量不受外界干扰

经典问题：

```
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

解决：

- 变量可以通过 **函数参数的形式** 传入，避免使用默认的`[[scope]]`向上查找

  立即执行函数

- 使用`setTimeout`包裹，通过第三个参数传入

- 使用 **块级作用域**，让变量成为自己上下文的属性，避免共享

  let

#### 立即执行函数

定义的时候就立刻执行的匿名函数

作用：创建一个独立的作用域，避免全局变量污染（外面访问不到其中变量）

#### script引入方式

- html 静态`<script>`引入
- js 动态插入`<script>`
- `<script defer>`: 异步加载，html解析完成后执行
- `<script async>`: 异步加载，加载完成立刻执行，会阻塞元素渲染



#### 对象拷贝

##### 浅拷贝：

拷贝所有的属性值到新的对象中，属性是对象的话，拷贝的就是地址，**修改时原对象也会受到影响**

- **Object.assign()**

```
let obj1 = { a: 0 , b: { c: 0}}; 
let obj2 = Object.assign({}, obj1);
obj2.b.c = 3; 
console.log(JSON.stringify(obj1)); // { a: 1, b: { c: 3}} 
console.log(JSON.stringify(obj2)); // { a: 2, b: { c: 3}} 
```

- **对象展开运算符**

  取出参数对象的所有可遍历属性，拷贝到当前对象之中。

  ```
  let a = {
    age: 1
  }
  let b = { ...a }
  a.age = 2
  console.log(b.age) // 1
  ```

  

##### 深拷贝：

完全拷贝一个新对象，**修改时原对象不再受到任何影响**

- **JSON.parse(JSON.stringify(obj))**

   性能最快

  - 具有循环引用的对象时，报错
  - 当值为函数、`undefined`、或`symbol`时，无法拷贝

- **递归逐一赋值，判断类型，检查环，需要忽略原型**

  ```
      function deepCopy(oldObj) {
          var newObj = {};
          for(var key in oldObj) {
              if(typeof oldObj[key] === 'object') {
                  newObj[key] = deepCopy(oldObj[key]);
              }else{
                  newObj[key] = oldObj[key];
              }
          }
          return newObj;
      }
  ```

  

####    new的执行过程

1. 创建一个空对象obj
2. obj.__proto__ = con.prototype
3. this指向obj：apply
4. 返回新对象(如果构造函数有自己 return 对象时，则返回该值)



#### instance原理

是否能在实例的原型链中，找到该构造函数的prototype指向的原型对象

// __proto__: 代表原型链
instance.[__proto__...] === instance.constructor.prototype



#### 如何代码复用

- 封装函数
- 继承
- 借用apply/call



#### 继承

通过指定原型，并可以通过原型链继承原型上的属性或者方法。

1. js继承
2. ES6 class/extends



#### 类型转换





#### 类型判断

1. null：String(null)

2. number/string/boolean/undefined/function ：typeof

3. array/object/RegExp:

   Object.prototype.toString.call(obj)



#### 模块化

逻辑上相关的代码组织到同一个包内，隔离、组织复杂的JavaScript代码，我们称为模块化。

- 解决命名冲突
- 提供复用性
- 提高代码可维护性

**在浏览器中使用 ES6 的模块化支持，在 Node 中使用 commonjs 的模块化支持。**

- es6: `import / export`

  commonjs: `require / module.exports / exports`

- es6和commonjs的区别

  - CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
  - CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

- `require`与`import`的区别

  - `require`支持 **动态导入**，`import`不支持，正在提案 (babel 下可支持)

  - `require`是 **同步** 导入，`import`属于 **异步** 导入

  - `require`是 **值拷贝**，导出值变化不会影响导入值；`import`指向 **内存地址**，导入值会随导出值而变化

    

#### 防抖和节流

高频触发优化方式

##### 防抖：

将多次高频操作化为只在最后一次执行

场景：用户输入，只需要在输入完成后做一次输入校验即可

```
function debounce(fn,delay){
    let timer = null
    return function(){
        var args = auguments
        var contect = this
        if(timer) clearTimeout(timer)
        timer = setTimeout(() => {
            fn.apply(fn,args)
        },delay)
    }
}
```

##### 节流：

每隔一段时间后执行一次，也就是降低频率，将高频操作优化成低频操作

使用场景: 滚动条事件 或者 resize 事件，通常每隔 100~500 ms执行一次即可。

```
function throttle(fn, wait, immediate) {
    let timer = null
    let callNow = immediate
    
    return function() {
        let context = this,
            args = arguments

        if (callNow) {
            fn.apply(context, args)
            callNow = false
        }

        if (!timer) {
            timer = setTimeout(() => {
                fn.apply(context, args)
                timer = null
            }, wait)
        }
    }
}

```



#### this指向

通过this获取当前运行环境

bind/call/apply实现

##### call实现

思路

1. 将函数设为对象的属性
2. 执行该函数
3. 删除该函数

```
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window
  context.fn = this
  const args = [...arguments].slice(1)
  const result = context.fn(...args)
  delete context.fn
  return result
}

```

##### apply实现

```
Function.prototype.myApply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window
  context.fn = this
  let result
  // 处理参数和 call 有区别
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}
```

##### bind实现

```
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  const _this = this
  const args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```



















