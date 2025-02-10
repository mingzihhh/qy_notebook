## Async/Await，generator，Iterator

<https://juejin.im/post/5d2c814c6fb9a07ecd3d8e43>

Async/Await就是一个**自执行的generator函数**。利用generator函数的特性把异步的代码写成“同步”的形式。

### Iterator 迭代器

##### 思想

单向链表

##### 迭代过程

- 创建一个指针对象，指向当前数据结构的起始位置
- 第一次调用指针对象的 next 方法，将指针指向数据结构的第一个成员
- 第二次调用指针对象的 next 方法，将指针指向数据结构的第二个成员
- 不断的调用指针对象的 next 方法，直到它指向数据结构的结束位置

##### 可迭代对象

对象（或它原型链上的某个对象）必须有一个名字是 `Symbol.iterator` 的属性

原生具有该属性的有：**字符串、数组、类数组的对象、Set 和 Map**



##### 当一个对象只有满足下述条件才会被认为是一个迭代器

1. 实现了一个 `next()` 的方法
2. 该方法**必须返回一个对象**,对象有两个必要的属性：

- `done`（bool）
  - true：迭代器已经超过了可迭代次数。这种情况下,value 的值可以被省略
  - 如果迭代器可以产生序列中的下一个值，则为 false。这等效于没有指定 done 这个属性
- `value` 迭代器返回的任何 JavaScript 值。done 为 true 时可省略



### Generator函数

调用一个生成器函数并不会马上执行它里面的语句，而是返回一个这个生成器的**迭代器对象**

- 当这个迭代器的 `next()`方法被首次（后续）调用时，其内的语句会执行到第一个（后续）出现 `yield` 的位置为止（让执行处于**暂停状**），`yield` 后紧跟迭代器要返回的值。

- 如果用的是 `yield*`（多了个星号），则表示将执行权移交给另一个生成器函数（当前生成器**暂停执行**）

- 调用 `next()` （再启动）方法时，如果传入了参数，那么这个参数会作为**上一条执行的 yield 语句的返回值**

  

**Generator 的本质**

暂停，它会让程序执行到指定位置先暂停（`yield`），然后再启动（`next`），再暂停（`yield`），再启动（`next`）



**Generator 的缺陷**

必须手动执行，手动添加then执行回调，流程管理不方便

>或者使用Thunk函数或者co模块，自动执行 Generator 函数，自动控制 Generator 函数的流程，接收和交还程序的执行权。

```
const promisify = require('util').promisify;
const path = require('path');
const fs = require('fs');
const readFile = promisify(fs.readFile);

function run(gen) {
  const g = gen();
  function next(data) {
    const res = g.next(data);
    // 深度递归，只要 `Generator` 函数还没执行到最后一步，`next` 函数就调用自身
    if (res.done) return res.value;
    res.value.then(function(data) {
      next(data);
    });
  }
  next();
}


run(function*() {
  const res1 = yield readFile(path.resolve(__dirname, '../data/a.json'), { encoding: 'utf8' });
  console.log(res1);
  // {
  //   "a": 1
  // }
  const res2 = yield readFile(path.resolve(__dirname, '../data/b.json'), { encoding: 'utf8' });
  console.log(res2);
  // {
  //   "b": 2
  // }
});
```



### Async/Await

**async 函数的实现原理是将 Generator 函数和自动执行器，包装在一个函数里**。`async`函数对 Generator 函数的改进，体现在以下四点:

- `async function` 代替了 `function*`，`await` 代替了 `yield`，语义化更好
- 无需自己手写一个自动执行器 `run`，内置执行器
- 适用性更广：`co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象，而`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。
- 返回值是 promise，方便后续处理



进一步说，`async`函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖。

处理机制：

- `async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

- `async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。
- `async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。



**实际上await是一个让出线程的标志。await后面的表达式会先执行一遍，将await后面的代码加入到microtask中，然后就会跳出整个async函数来执行后面的代码。**

由于因为async await 本身就是 promise+generator 的语法糖。所以await后面的代码是microtask。

```
async function async1() {
	console.log('async1 start');
	await async2();
	console.log('async1 end');
}
等价于
async function async1() {
	console.log('async1 start');
	Promise.resolve(async2()).then(() => {
    	console.log('async1 end');
    })
}
```

**缺点**：因为 `await` 将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了 `await` 会导致性能上的降低。



### Promise、Generator 、async 函数的比较

promise：代码完全都是 Promise 的 API（`then`、`catch`等等），操作本身的语义反而不容易看出来。

Generator 函数：必须有一个任务运行器，自动执行 Generator 函数

 async 函数：

- 内置执行器，执行只需要一行

- 更好的语义

- 更广的适用性

- **返回值是 Promise 对象**，Generator 函数的返回值是 Iterator 对象