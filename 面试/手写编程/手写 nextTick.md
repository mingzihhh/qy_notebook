## 手写nextTick

`nextTick` 的基本原理涉及到 Vue 中的**异步更新**队列、JavaScript 的**事件循环**机制，实质上是 **vue 使用微任务（Promise 或 MutationObserver）任务来模拟异步操作**，将传入的回调函数推入到微任务队列中，确保它在当前事件循环完成后执行。如果不支持微任务，则回退到使用宏任务（setImmediate 或 setTimeout）。

**Promise 模拟微任务：**

- 如果当前环境支持原生的 Promise，并且 Promise 是原生实现的，Vue 会使用 `Promise.resolve().then(flushCallbacks)` 来将 `flushCallbacks` 包装成微任务。

**MutationObserver 的备选方案：**

- 如果不支持 Promise，Vue 判断是否支持原生的 MutationObserver。如果支持，Vue 会创建一个 MutationObserver 实例，通过监听文本节点的变化来触发 `flushCallbacks` 的执行。
- 当使用 `MutationObserver` 并调用其 `observe` 方法时，这个观察者会被安排在下一个**微任务**队列中运行。

**setImmediate 和 setTimeout 的选择：**

- 如果前两者都不可用，Vue 会尝试使用 `setImmediate`，如果也不支持，则会回退到使用 `setTimeout(flushCallbacks, 0)`。这两者都是宏任务，会在当前任务队列的末尾执行。

**isUsingMicroTask 标志：**

- 在这个实现中，还有一个 `isUsingMicroTask` 标志，用于表示最终是否以微任务的方式执行 `nextTick`。当使用了 Promise 或 MutationObserver 时，`isUsingMicroTask` 会被设置为 true。

**Promise.then 中的 setTimeout：**

- 在使用 Promise 模拟微任务时，为了确保微任务队列得到刷新，还会通过 `setTimeout(noop)` 强制刷新微任务队列。这主要是为了解决在某些环境（比如 iOS）下，Promise.then 后面没有宏任务的情况导致微任务队列不会刷新的问题。



```
 // 使用 promise 模拟
 export function myNextTick(fn) {
 	return fn ? Promise.resolve().then(fn) : Promise.resolve();
 }
 
 // 使用 MutationObserver  模拟
export function myNextTick(fn) {
  // 创建一个DOM监听器
  const observer = new MutationObserver(() => {
    // 当 DOM 变动被观察到，立即触发回调
    fn();
    // 在回调执行后，停止观察（防止内存泄漏）
    observer.disconnect();
  });

  const app = document.getElementById('app');
  const observerOptions =  {
      childList: true, // 观察目标子节点的变化，是否有添加或者删除
      attributes: true, // 观察属性变动
      subtree: true, // 观察后代节点，默认为 false
  };
  observer.observe(app, observerOptions); // 监听上某个dom节点及子节点
}

```


