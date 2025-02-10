优化重新渲染性能的一种常见方法是跳过不必要的工作。例如，可以告诉 React 重用缓存的计算结果，或者如果数据自上次渲染以来没有更改，则跳过重新渲染。

**使用以下 Hook 跳过计算和不必要的重新渲染：**

- 使用 [`useMemo`](https://zh-hans.react.dev/reference/react/useMemo) 缓存计算代价昂贵的计算结果。
- 使用 [`useCallback`](https://zh-hans.react.dev/reference/react/useCallback) 将函数传递给优化组件之前缓存函数定义。



`useCallback` 和 `useMemo` 底层是通过**闭包和依赖数组的变化**判断来决定是否更新缓存。如果依赖数组没有变化，React 会直接返回缓存的值或函数，而不再重新计算，减少 Fiber 节点更新的次数，避免不必要的重新渲染，提高性能。



### useMomo

```
const cachedValue = useMemo(calculateValue, dependencies)
```

在初次渲染时，`useMemo` 返回不带参数调用 `calculateValue` 的结果。

在接下来的渲染中，如果依赖项没有发生改变，它将直接返回上次缓存的值；否则将再次调用 `calculateValue`并返回最新结果，然后缓存该结果以便下次重复使用。

如果你忘记了依赖数组，`useMemo` 将每次重新运行计算。



注意：**不应该在所有地方添加 useMemo/useCallback**，避免适得其反的优化，带来无意义的计算和维护负担

- **缓存维护成本**：每次渲染时，React 都需要检查依赖数组是否变化，依赖检查本身也有成本。如果依赖频繁变化，`useCallback` 和 `useMemo` 可能适得其反，增加开销。

- **缓存带来的内存占用**：缓存的值或函数会占用内存，如果没有显著的性能问题，可能得不偿失。

  



#### 使用场景：

- **跳过代价昂贵的重新计算**：比如当正在过滤转换一个大型数组，或者进行一些复杂计算，而数据没有改变，那么可能希望跳过这些重复计算
  - 可以通过测量计算的开销时间，如果记录的时间加起来很长（`1ms` 或者更多），那么记忆此计算结果是有意义的
- **优化重新渲染子组件的性能**
  - 使用 memo 包裹渲染很慢的子组件：当组件的 props 跟上一次渲染相同的时候它就会跳过本次渲染
    - 用 memo 包裹的组件，当 props / state / context 变化时会重新渲染
  - 当传入子组件的 props 是个每次渲染都会被重新创建的数组或对象时，memo 优化将不起作用，应该使用 useMemo 将 props 的计算函数进行包裹，**可以确保它在重新渲染之间具有相同值**，直到依赖项发生变化



### useCallback

`useCallback` 的唯一好处是它可以让**你避免在内部编写额外的嵌套函数**。它没有做任何其他事情

```
const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails
    });
}, [productId, referrer])

等价于

const handleSubmit = useMemo(() => {
	return (orderDetails) => {
		  post('/product/' + productId + '/buy', {
        referrer,
        orderDetails
      });
	}
}, [productId, referrer])
```



#### **使用场景：**

- 函数被频繁传递给子组件
- 函数被用作依赖项（如在 `useEffect` 中）
- 函数较为复杂，且在多次渲染中内容不变





有时由于屏幕确实需要更新，无法跳过重新渲染。在这种情况下，可以通过将必须同步的阻塞更新（比如使用输入法输入内容）与不需要阻塞用户界面的非阻塞更新（比如更新图表）分离以提高性能。

**使用以下 Hook 处理渲染优先级：**

- [`useTransition`](https://zh-hans.react.dev/reference/react/useTransition) 允许将状态转换标记为非阻塞，并允许其他更新中断它。

- [`useDeferredValue`](https://zh-hans.react.dev/reference/react/useDeferredValue) 允许延迟更新 UI 的非关键部分，以让其他部分先更新。

  

### useTransition

```
const [isPending, startTransition] = useTransition()
```

- isPending：是否存在待处理的 transition
- startTranstion 函数：使用此方法将状态更新标记为 transition。



**startTransition 本质：改变渲染事件优先级，延迟渲染**

在调用回调函数之前设置了更新的优先级为 ContinuousEvent 的优先级，也就是连续事件优先级，比 DiscreteEvent 离散事件优先级更低，所以会比另一个 setState 触发的渲染的优先级低，在调度的时候排在后面。延迟渲染并不是延迟reconcile，而是延迟reconcile的结果(workInProgress)渲染到屏幕上

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b057d5bd19ea4b529080cb0951f2d3af~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?" alt="img" style="zoom:50%;" />



#### 使用场景：

- 通过 transition，UI 仍将在重新渲染过程中保持响应性。例如用户点击一个选项卡，但改变了主意并点击另一个选项卡，他们可以在不等待第一个重新渲染完成的情况下完成操作



##### 和 startTransition 的区别

`startTransition` 与 [`useTransition`](https://zh-hans.react.dev/reference/react/useTransition) 非常相似，但它不提供 `isPending` 标志来跟踪一个 Transition 是否正在进行。你可以在 `useTransition` 不可用时调用 `startTransition`。例如，在组件外部（如从数据库中）使用 `startTransition`。



### useDeferredValue

```
const deferredValue = useDeferredValue(value)
```

- 对 state 用 useDeferredValue 包裹之后，新的 state 就会放到下一次更新。
- React 17 里就是通过 useEffect 把这个值的更新时机延后了，React 18 里虽然功能一样但实现变了，现在是基于并发模式的，通过 Lane 的优先级实现的延后更新 => **降低优先级**



#### 使用场景

- 配合 Suspense 使用，在新内容加载时显示旧内容：比如在搜索时延迟更新结果列表，并继续显示之前的结果，直到新的结果准备好（一般会显示 loading

```
export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
      	// deferredQuery 在数据加载完成前会保留以前的值，因此 SearchResults 将暂时显示旧的结果。
        <SearchResults query={deferredQuery} />
      </Suspense>
    </>
  );
}
```

- 延迟渲染 UI 的某些部分：比如有一个文本框和一个组件（例如图表或长列表），在每次按键时都会重新渲染，输入框的输入会感觉非常卡顿；使用 `useDeferredValue` 能够优先更新输入框（必须快速更新），而不是更新结果列表（可以更新慢一些），使输入变得灵敏，而列表的更新会稍有延迟（React 会尽快更新列表，但不会阻塞用户输入



#### 延迟一个值与防抖和节流之间有什么不同？

useDeferredValue 更适合优化渲染，而防抖节流更适合优化非渲染期间的工作，比如减少网络请求

- useDeferredValue 不需要选择固定延迟时间，滞后的程度与设备的速度有关

- useDeferredValue 执行的延迟重新渲染默认是可中断的：如果 React 正在重新渲染一个大型列表，但用户进行了另一次键盘输入，React 会放弃该重新渲染，先处理键盘输入，然后再次开始在后台渲染。

  相比之下，防抖和节流仍会产生不顺畅的体验，因为它们是阻塞的：它们仅仅是将渲染阻塞键盘输入的时刻推迟了。