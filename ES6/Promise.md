## Promise

#### promise的含义

所谓`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。

从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。对象里存储一个状态，这个状态是可以随着内部的执行转化的，为以下三种状态之一：等待态(Pending)、完成态(Fulfilled)、拒绝态(Rejected)。

一开始，我们先设置好等状态从 pending 变成 fulfilled 和 rejected 的预案（当成功后我们做什么，失败时我们做什么）。

Promise 启动之后，当满足成功的条件时我们让状态从 pending 变成 fullfilled （执行 resolve）；当满足失败的条件，我们让状态从 pending 变成 rejected（执行 reject）

**特点：**

- 对象的状态不受外界影响。
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。

**缺点：**

- 无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。
- 当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

**链式调用**

pending 状态的 Promise 对象可能会变为fulfilled 状态并传递一个值给相应的状态处理方法，也可能变为失败状态（rejected）并传递失败信息。当其中任一种情况出现时，Promise 对象的 `then` 方法绑定的处理方法（handlers ）就会被调用（then方法包含两个参数：onfulfilled 和 onrejected，它们都是 Function 类型。当Promise状态为*fulfilled*时，调用 then 的 onfulfilled 方法，当Promise状态为*rejected*时，调用 then 的 onrejected 方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争）。

因为 `Promise.prototype.then` 和  `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被链式调用。

![img](https://mdn.mozillademos.org/files/8633/promises.png)

**方法**

[`Promise.all(iterable)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

[`Promise.race(iterable)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)

