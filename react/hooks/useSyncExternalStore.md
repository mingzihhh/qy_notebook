在组件顶层调用 `useSyncExternalStore` 以从外部 store 读取值。

```
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

它返回 store 中数据的快照。参数：

1. `subscribe` ：用于订阅外部存储变化的回调函数。它接收一个监听器函数，在外部存储发生变化时调用。
2. `getSnapshot` 函数：返回外部存储的当前状态快照。在 store 不变的情况下，重复调用 `getSnapshot` 必须返回同一个值。如果 store 改变，并且返回值也不同了（用 [`Object.is`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 比较），React 就会重新渲染组件
3. **可选** `getServerSnapshot`：在服务器渲染时使用，提供服务器端的初始状态。它只会在服务端渲染时，以及在客户端进行服务端渲染内容的激活时被用到。快照在服务端与客户端之间必须相同，它通常是从服务端序列化并传到客户端的。如果你忽略此参数，在服务端渲染这个组件会抛出一个错误。



#### 使用场景

- 在 React 之外持有状态的第三方状态管理库：Zustand，Redux  内部实现



**为什么需要 useSyncExternalStore？**

在 React 18 的并发模式中，渲染可能会中断、重新执行或延迟。如果直接使用自定义订阅逻辑（如 `useEffect`），可能会导致以下问题：

- **状态不一致**：React 渲染期间，外部状态可能更新，导致组件获取的状态与渲染的 UI 不匹配。
- **订阅管理复杂**：需要手动处理订阅和取消订阅逻辑。
- **难以处理并发模式**：手动实现逻辑可能在并发模式下产生意料之外的行为。

`useSyncExternalStore` 通过提供一个标准化接口，确保在并发渲染模式下，组件始终使用最新的外部状态，同时简化订阅管理。