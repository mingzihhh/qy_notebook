React scheduler 的核心职责只有 1 个, 就是**执行回调**

- 把`react-reconciler`提供的回调函数, 包装到一个任务对象中.
- 在内部维护一个任务队列, 优先级高的排在最前面.
- 循环消费任务队列, 直到队列清空.



scheduler 内部包括一个**任务调度循环**， 源码位于[`Scheduler.js`](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/Scheduler.js), 它是`react`应用得以运行的保证, 它需要循环调用, 控制所有任务(`task`)的调度.

https://juejin.cn/post/7331135154209308687



#### **内核**

```
// 调度相关：请求或取消调度
export let requestHostCallback; // 请求及时回调: port.postMessage
export let cancelHostCallback; // 取消及时回调: scheduledHostCallback = null
export let requestHostTimeout; // 请求延时回调: setTimeout
export let cancelHostTimeout; // 取消延时回调: cancelTimeout

// 时间切片相关：执行时间分割, 让出主线程
export let shouldYieldToHost; // 是否让出主线程(currentTime >= deadline && needsPaint): 让浏览器能够执行更高优先级的任务(如ui绘制, 用户输入等)
export let requestPaint; // 请求绘制: 设置 needsPaint = true
export let getCurrentTime; // 获取当前时间
export let forceFrameRate; // 强制设置 yieldInterval (让出主线程的周期),这个函数虽然存在, 但是从源码来看, 几乎没有用到
```



#### scheduler 优先级和任务队列

```
NoPriority = 0; 
ImmediatePriority = 1; // 离散的事件，如 click，keydown，input
UserBlockingPriority = 2; // 连续的事件，如 scroll, drag, mouseover
NormalPriority = 3;
LowPriority = 4;
IdlePriority = 5; // 空间优先级
```

在`scheduler`接收一个任务后，会为这个任务创建一个调度对象，对象中保存这个任务的优先级，开始时间，过期时间以及真实的执行函数。过期时间的计算，则是**任务开始时间加上优先级代表的时间间隔**。

比如：`ImmediatePriority` 的执行间隔为-1，在计算过期时间时当前时间与-1相加，也就是说`ImmediatePriority`优先级的任务过期时间肯定会小于任务开始时间。

当调度对象被确定后，会把任务分成了两种：

**开始时间大于当前时间，说明未过期；开始时间小于等于当前时间，说明已过期。任务队列的核心就是保证最紧急的任务优先被调度执行。**

- **timerQueue 队列**：
  - 存放未过期任务
  - 按照开始时间排序，开始时间越小越靠前。因为开始时间越早，说明会越早开始。
  - 里面的任务都不会立即执行。等待排在第一位的任务间隔时间到了之后，将第一个任务加入到 taskQueue 队列中。然后重复执行这个动作，直到 timerQueue 队列中的任务被清空。
- **taskQueue 队列**：
  - 存放过期任务
  - 按照过期时间排序，过期时间小的排在前面，因为过期时间越早，说明越紧急。taskQueue 队列里的人物
  - 立即调度一个函数去循环 taskQueue，挨个执行里面的任务

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/48ee9850b770419aac642d07b31fb3c1~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1483&h=1029&s=85735&e=png&b=ffffff" alt="image.png" style="zoom:33%;" />



#### **如何触发调度**

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c52d2920bc724af38dffc7be0141a482~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2396&h=2803&s=276540&e=png&b=ffffff" alt="image.png" style="zoom: 33%;" />

https://juejin.cn/post/7345746216150417446

https://juejin.cn/post/7331135154209308687

**scheduleCallback 函数是整个调度的入口函数**。主要负责生成调度任务、根据任务是否过期将任务放入`timerQueue`或`taskQueue`，然后触发调度行为，让任务开始进入调度。从 requestHostCallback 开始正式进入一个任务调度执行的过程。

以请求回调和取消回调为例， scheduledHostCallback = callback , 然后通过 **MessageChannel** 发消息的方式触发 performWorkUntilDeadline 函数, 最后执行回调 scheduledHostCallback。**performWorkUntilDeadline 函数的职责是按照时间片的限制去中断任务，并通知调度者再次调度一个新的执行者去继续任务。直到任务被完全执行完。 ‍**



此处需要注意:  **MessageChannel 在浏览器事件循环中属于宏任务, 所以调度中心永远是异步执行回调函数**

- **为什么需要是宏任务**：
  - **任务中断的需求**：微任务不能被中断，而 React 的 Fiber 架构依赖于可中断任务来处理优先级调度。
  - **复杂任务的分片执行**：React 的任务切片机制需要一个可以暂停和恢复的上下文，宏任务更适合这种需求。

- **为什不使用其他方式**
  - requestIdleCallback
    - 主要用来处理低优先级任务
    - 浏览器兼容问题(目前不支持Safari浏览器)
    - 执行不稳定：正常情况下渲染一帧时长控制在16.67ms，通常会延迟到当前帧渲染完成后的空闲时间，但并不严格限制在每一帧调用一次，所以帧率（FPS）会比 60 FPS 低（大约 20ms 触发一次，约等于 50 FPS），并且有可能出现不规则的帧间隔
  - setTimeout(fn，0）：HTML5标准规定了setTimeout()的第二个参数的最小值（最短间隔），不得低于4 ms，因为 60 FPS 要求每帧间隔不超过 16.66 ms，所以 4ms 是不容忽视的浪费。
  - requestAnimationFrame：在重绘之前执行，仍然可能会阻塞渲染，并且执行不稳定，如果浏览器间隔了 10ms 才更新页面，那么这 10ms 就浪费了。

- **在不同的宿主环境调用方式是不同的**：

  - 非浏览器环境，使用定时器执行，因为在非浏览器环境中，用户是看不到界面的，也不存在用户交互，所以卡顿与否也就不那么重要了。

  - 浏览器中使用`MessageChannel`实现

    

```
const channel = new MessageChannel();
const port = channel.port2;
// 每次 port.postMessage() 调用就会添加一个宏任务
// 该宏任务为调用 performWorkUntilDeadline
channel.port1.onmessage = performWorkUntilDeadline;

// 接收 MessageChannel 消息
const performWorkUntilDeadline = () => {
  if (scheduledHostCallback !== null) {
    const currentTime = getCurrentTime(); // 1. 获取当前时间
    deadline = currentTime + yieldInterval; // 2. 设置deadline
    const hasTimeRemaining = true;
    try {
      // 3. 执行回调, 返回是否有还有剩余任务
      const hasMoreWork = scheduledHostCallback(hasTimeRemaining, currentTime);
      if (!hasMoreWork) {
        // 没有剩余任务, 退出
        isMessageLoopRunning = false;
        scheduledHostCallback = null;
      } else {
        port.postMessage(null); // 有剩余任务, 发起新的调度
      }
    } catch (error) {
      port.postMessage(null); // 如有异常, 重新发起调度，在下个宏任务继续执行
      throw error;
    }
  } else {
    isMessageLoopRunning = false;
  }
  needsPaint = false; // 重置开关
};

// 请求回调
requestHostCallback = function (callback) {
  // 1. 保存callback
  scheduledHostCallback = callback;
  if (!isMessageLoopRunning) {
    isMessageLoopRunning = true;
    // 2. 通过 MessageChannel 发送消息
    port.postMessage(null);
  }
};

// 取消回调
cancelHostCallback = function () {
  scheduledHostCallback = null;
};
```

![img](https://7km.top/static/core.e65ca659.png)



#### 任务调度循环

```
// 省略部分无关代码
function workLoop(hasTimeRemaining, initialTime) {
  let currentTime = initialTime; // 保存当前时间, 用于判断任务是否过期
  currentTask = peek(taskQueue); // 获取队列中的第一个任务
  while (currentTask !== null) {
    if (
      currentTask.expirationTime > currentTime &&
      (!hasTimeRemaining || shouldYieldToHost())
    ) {
      // 虽然currentTask没有过期, 但是执行时间超过了限制(毕竟只有5ms, shouldYieldToHost()返回true). 停止继续执行, 让出主线程
      break;
    }
    const callback = currentTask.callback;
    if (typeof callback === 'function') {
      currentTask.callback = null;
      currentPriorityLevel = currentTask.priorityLevel;
      const didUserCallbackTimeout = currentTask.expirationTime <= currentTime;
      // 执行回调
      const continuationCallback = callback(didUserCallbackTimeout);
      currentTime = getCurrentTime();
      // 回调完成, 判断是否还有连续(派生)回调
      if (typeof continuationCallback === 'function') {
        // 产生了连续回调(如fiber树太大, 出现了中断渲染), 保留currentTask
        currentTask.callback = continuationCallback;
        return ture;
      } else {
        // 把currentTask移出队列
        if (currentTask === peek(taskQueue)) {
          pop(taskQueue);
        }
      }
    } else {
      // 如果任务被取消(这时currentTask.callback = null), 将其移出队列
      pop(taskQueue);
    }
    // 更新currentTask
    currentTask = peek(taskQueue);
  }
  if (currentTask !== null) {
    return true; // 如果task队列没有清空, 返回true，等待调度中心下一次回调
  } else {
  	// 去timerQueue中查看是否有需要加入到taskQueue的任务
  	const firstTimer = peek(timerQueue);
    if (firstTimer !== null) {
      requestHostTimeout(handleTimeout, firstTimer.startTime - currentTime);
    }
    return false; // task队列已经清空, 返回false，让外部终止此次调度
  }
}
```

workLoop 就是一个大循环, 在此处实现了**时间切片(time slicing)**和 **fiber 树的可中断渲染**. 这 2 大特性的实现, 都集中于这个 while 循环

**每一次`while`循环的退出就是一个时间切片**, 深入分析`while`循环的退出条件:

1. 队列被完全清空: 这种情况就是很正常的情况, 一气呵成, 没有遇到任何阻碍

2. 执行超时: 在消费 taskQueue 时, 在执行 task.callback 之前, 都会检测是否超时, 所以**超时检测是以 task 为单位**
   - 如果某个`task.callback`执行时间太长(如: `fiber树`很大, 或逻辑很重)也会造成超时，所以在执行`task.callback`过程中, 也需要一种机制检测是否超时, 如果超时了就立刻暂停`task.callback`的执行
   
     

##### 如何实现时间切片

消费任务队列的过程中, 可以消费 1～n 个 task, 甚至清空整个 queue，但是在每一次具体执行  task.callback 之前都要进行超时检测, 如果超时可以立即退出循环并等待下一次调用，每次退出就是一个时间切片。



##### 如何实现可中断渲染

在时间切片的基础之上, 如果单个  task.callback 执行时间就很长(假设 200ms)，就需要  task.callback 自己能够检测是否超时, 所以**在 fiber 树构造过程中, 每构造完成一个单元, 都会检测一次超时([源码链接](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L1637-L1639)), 如遇超时就退出 fiber 树构造循环, 并返回一个新的回调函数**(就是此处的`continuationCallback`)并等待下一次回调继续未完成的`fiber树构造`.

由于任务被中断只是循环被`break`，当前被中断的任务还依然保存在`taskQueue`队列中的第一个并未被弹出，所以下次调度执行的任务依然是上次被中断的那一个。



##### 如何取消任务

取消任务的场景是如果当前正在执行一个比较低优先级的任务，这是有一个高优先级的任务进来了，所以就需要先将正在执行的低优先级的任务取消掉，然后调度一个任务去优先执行高优先级的任务。

在`workLoop`中获取当前调度对象的`callback`属性，如果是函数就开始执行，如果不是的话直接将这个任务弹出`taskQueue`队列。所以**取消任务直接将`callback`属性赋值为`null`就可以了。**



##### 优化调度性能：节流和防抖

比如连续 2 次 setState , 第 2 次  setState 同样会触发`reconciler运作流程`中的调度阶段, 如果发现处于调度中, 则需要一些节流和防抖措施, 进而保证调度性能 => task 注册完成后会设置 fiberRoot 上的属性表示调度中

- **节流**
  - 判断条件:  existingCallbackPriority === newCallbackPriority
  -  新旧更新的优先级相同, 如连续多次执行`setState`, 则无需注册新`task`(继续沿用上一个优先级相同的`task`), 它们合并为一个 task，在同一次渲染中更新两个状态，从而避免不必要的重复渲染

- **防抖**
  - 判断条件:  existingCallbackPriority !== newCallbackPriority
  -  新旧更新的优先级不同 则取消旧`task`, 重新注册新`task`

