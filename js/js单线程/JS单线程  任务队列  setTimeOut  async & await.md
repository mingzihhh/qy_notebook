### JS单线程 / 任务队列 / setTimeOut / async & await

#### JS单线程

- JavaScript引擎是基于事件驱动单线程执行的，也就是同一时间只能运行一个线程，浏览器无论什么时候都只有一个js线程在运行js程序。

- 所有的任务可以分为两种，一种是同步任务（synchronous），另外一种是异步任务(asynchronous)。

  - **同步任务**指的是，在主线程上，排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务;

  - **异步任务**指的是，不进入主线程，而进入“任务队列”（task queue）的任务，只有“任务队列”通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

- 1. 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
  2.  主线程之外，还存在一个“任务队列”（task queue），只要异步任务有了运行结果，就在“任务队列”中放置一个事件。 
  3.  一旦“执行栈”中的所有同步任务执行完毕，系统就会读取“任务队列”，看看里边有哪些事件。哪些对应的异步任务，于是结束等待状态，进入“执行栈”开始执行。
  4.   主线程不断重复上边的第三步。

#### 任务队列

- "任务队列"是一个事件的队列（也可以理解成消息的队列），IO设备完成一项任务，就在"任务队列"中添加一个事件，表示相关的异步任务可以进入"执行栈"了。主线程读取"任务队列"，就是读取里面有哪些事件。 
- "任务队列"中的事件，除了IO设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）。只要指定过回调函数，这些事件发生时就会进入"任务队列"，等待主线程读取。 
- 所谓“回调函数”(callback)，就是那些会被主线程挂起来的代码。异步任务必须执行回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。
- “任务队列”是一个先进先出的数据结构，排在前边的事件，优先被主线程读取。主线程的读取过程，基本上是自动的，只要“执行栈”一清空，“任务队列”上第一位的事件，就会自动进入主线程。但是由于存在“定时器”功能，主线程首先要检查一下执行时间，某些事件，只有到了规定时间，才能返回主线程。



### async & await

- async 会将其后的函数（函数表达式或 Lambda）的返回值封装成一个 Promise 对象，而 await 会等待这个 Promise 完成，并将其 resolve 的结果返回出来。

- 当遇到 await 关键字时，async 函数执行会被暂停， JS 引擎跳出异步函数，并且在异步函数被调用的执行上下文中继续执行代码，`async`函数中剩余的代码会在微任务中运行

  https://mp.weixin.qq.com/s?__biz=MzA4MDU1MDExMg%3D%3D&idx=1&mid=2247484670&scene=21&sn=9b9011db1684500932d5f4386959d919#wechat_redirect

```
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2");
}

console.log("script start");

setTimeout(function () {
  console.log("setTimeout");
}, 0);

async1();

new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("promise2");
});

console.log("script end");

answer: 
"script start";
"async1 start";
"async2";
"promise1";
"script end";
"async1 end";
"promise2";
"setTimeout";
```



#### 定时器功能

- 定时器功能主要由 **setTimeout()和setInterval()**这两个函数来完成，它们的内部运行机制完全一样，区别在于前者指定的代码是一次性执行，后者则为反复执行

- setTimeout()接受3个参数，第一个是回调函数，第二个是推迟执行的毫秒数，第三个给回调函数传参。

- setTimeout(fn,0)的含义是，指定某个任务，在主线程最早可得的空闲时间执行，也就是说，**尽可能早的执行**。它在“任务队列”的**尾部**添加一个事件，因此要等到同步任务和“任务队列”中的现有事件，都处理完，才会得到执行。

- HTML5标准规定了setTimeout()的第二个参数的最小值（最短间隔），不得低于4毫秒，如果低于这个值，就会自动增加。在此之前，老版本的浏览器都将最短间隔设为10毫秒。另外，对于那些DOM的变动（尤其是涉及页面重新渲染的部分），通常不会立即执行，而是每16毫秒执行一次。这时使用requestAnimationFrame()的效果要好于setTimeout()。

- 需要注意的是，setTimeout()只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行。

- 注意异步的作用域问题

  
  
  
  
  
  
  





