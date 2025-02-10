## Promise

#### promise的含义

Promise本身是**同步的立即执行函数**

所谓`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。

从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。对象里存储一个状态，这个状态是可以随着内部的执行转化的，为以下三种状态之一：等待态(Pending)、完成态(Fulfilled)、拒绝态(Rejected)。

一开始，我们先设置好等状态从 pending 变成 fulfilled 和 rejected 的预案（当成功后我们做什么，失败时我们做什么）。

**特点：**

- 对象的状态不受外界影响。
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。

**缺点：**

- 无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。

- 如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部 => 需要使用catch指定失败的回调函数，否则会“吃掉”错误

- 当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

  

**resolve函数**：从 pending 变为 resolved，在异步操作成功时调用，并将异步操作的结果，作为参数传递出去

**reject函数**：从 pending 变为 rejected，在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

**调用`resolve`或`reject`并不会终结 Promise 的参数函数的执行。**



**then方法**：为 Promise 实例添加状态改变时的回调函数。

- 可以接受两个回调函数作为参数。第一个回调函数是`Promise`对象的状态变为`resolved`时调用；第二个回调函数是`Promise`对象的状态变为`rejected`时调用，可选。

- 这两个函数都接受`Promise`对象传出的值作为参数。

- 返回一个新的promise实例

- 链式写法，`then`方法后面再调用另一个`then`方法。

  第一个`then`方法指定的回调函数，返回的是另一个`Promise`对象。这时，第二个`then`方法指定的回调函数，就会等待这个新的`Promise`对象状态发生变化。如果变为`resolved`，就调用第一个回调函数，如果状态变为`rejected`，就调用第二个回调函数。

**catch方法**：别名，用于指定发生错误时的回调函数

```
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```



**链式调用**

pending 状态的 Promise 对象可能会变为fulfilled 状态并传递一个值给相应的状态处理方法，也可能变为失败状态（rejected）并传递失败信息。

当其中任一种情况出现时，Promise 对象的 `then` 方法绑定的处理方法（handlers ）就会被调用（then方法包含两个参数：onfulfilled 和 onrejected，它们都是 Function 类型。当Promise状态为*fulfilled*时，调用 then 的 onfulfilled 方法，当Promise状态为*rejected*时，调用 then 的 onrejected 方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争）。

因为 `Promise.prototype.then` 和  `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被链式调用。

![img](https://mdn.mozillademos.org/files/8633/promises.png)



**方法**

**Promise.prototype.finally** 

不管 Promise 对象最后状态如何，都会执行的操作 



**Promise.all**

- 将多个 Promise 实例，包装成一个新的 Promise 实例。如果参数不是 promise 实例，就会先调用下面讲到的`Promise.resolve`方法，将参数转为 Promise 实例，再进一步处理
- 所有异步操作都成功才会变成 fufilled，有一个 rejected 就会 rejected
- 如果作为参数的 Promise 实例，自己定义了`catch`方法，那么它一旦被`rejected`，并不会触发`Promise.all()`的`catch`方法。



**Promise.race**

- 同样是将多个 Promise 实例，包装成一个新的 Promise 实例。
- 参数重率先改变的 Promise 实例的返回值会传递给`p`的回调函数。



**Promise.allSettled**

- 等到参数数组的所有 Promise 对象都发生状态变更（不管是`fulfilled`还是`rejected`），返回的 Promise 对象才会发生状态变更。
- 返回的 Promise 实例总是`fulfilled`，不会变成`rejected`。状态变成`fulfilled`后，它的回调函数会接收到一个数组作为参数，该数组的每个成员对应前面数组的每个 Promise 对象。

```
const resolved = Promise.resolve(42);
const rejected = Promise.reject(-1);

const allSettledPromise = Promise.allSettled([resolved, rejected]);

allSettledPromise.then(function (results) {
  console.log(results);
});
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]

```



**Promise.any**

- 只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态；如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态。



**Promise.resolve()**  

将现有对象转为 Promise 对象

- 参数是 promise 实例：不做更改返回这个实例
- 参数是 thenable 对象：将这个对象转为 Promise 对象，然后就立即执行`thenable`对象的`then()`方法。
- 参数不是具有`then()`方法的对象，或根本就不是对象：返回一个新的 Promise 对象，状态为`resolved`。
- 不带有任何参数：直接返回一个`resolved`状态的 Promise 对象。立即`resolve()`的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时。

```
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))

const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))
```



**Promise.reject()**  

返回一个`rejected`状态的 Promise 对象，参数会原封不动地作为`reject`的理由，变成后续方法的参数。

