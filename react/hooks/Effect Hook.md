### useEffect()

引入副作用，包括处理网络、浏览器、DOM、动画、使用不同 UI 库编写的小部件以及其他非 React 代码。最常见的就是数据获取、订阅或者手动修改 DOM。

```
useEffect(setup, dependencies?);
```

- 跟 class 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途，只不过被合并成了一个 API。

- **执行时机**：浏览器已经完成页面渲染后，**异步执行**，不会阻塞浏览器渲染过程。

- 默认情况下，React 会在每次渲染后调用副作用函数 —— **包括**第一次渲染的时候。某种意义上讲，effect 更像是渲染结果的一部分 —— 每个 effect “属于”一次特定的渲染。
- setup 函数选择性返回一个 **清理（cleanup）** 函数，setup 代码用来连接到系统，cleanup 函数用来与系统断开链接
- `useEffect` 的第二个参数是一个数组，只有当数组中 state 值发生变化时，才会执行这个 `useEffect`；当依赖是空数组时，这个  `useEffect` 不会在组件任何的 props 或 state 发生改变时重新运行，只会在组件第一次渲染时执行一次
- 避免使用渲染期间创建的对象或函数作为依赖项，因为这些对象每次渲染都不同



**React 在必要时会调用 setup 和 cleanup，这可能会发生多次**：

1. 将组件挂载到页面时，将运行 setup 代码。

2. 重新渲染依赖项变更的组件后：

   - 首先，使用旧的 props 和 state 运行 cleanup 代码。

   - 然后，使用新的 props 和 state 运行 setup 代码。

3. 当组件从页面卸载后，cleanup 代码 将运行最后一次。



#### 使用场景

- **连接到外部系统**：比如 setInterval / clearInterval ，事件订阅，三方动画库等
- 当代码中有很多需要用 useEffect 的逻辑时，通常可以抽离成一个自定义 hook
- **请求数据**




### useLayoutEffect

```
useLayoutEffect(setup, dependencies?)
```

- 在浏览器重新绘制屏幕之前触发
- **执行时机**：React 完成 DOM 更新后，浏览器执行渲染之前，**同步执行**
- `useLayoutEffect` 内部的代码和所有计划的状态更新**阻塞了浏览器重新绘制屏幕**。两次渲染并阻塞浏览器绘制会影响性能，如果过度使用会使你的应用程序变慢。如果可能的话，尽量选择 useEffect



#### 使用场景

如果 Effect 一定要阻止浏览器绘制屏幕，使用 useLayoutEffect 替换 useEffect。只有当在浏览器绘制之前运行 Effect 非常重要的时候才需要如此，例如，在用户看到 tooltip 之前测量并定位它。

在 effect 里做了一些视觉相关的事情使页面发生了一个闪烁 => 使用 useLayoutEffect



### useInsertionEffect

- 可以在布局副作用触发之前将元素插入到 DOM 中

- 为 CSS-in-JS 打造，除非你正在使用 CSS-in-JS 库并且需要注入样式，否则你应该使用 useEffect 或者 useLayoutEffect



#### 使用场景

在运行时注入 style <= 不建议，因为

- 运行时注入会使浏览器频繁地重新计算样式
- 如果在 React 生命周期中某个错误的时机进行运行时注入，它可能会非常慢 <= useInsertionEffect 解决了这个问题，它会确保 `<style>` 标签在其它 Effect 运行前被注入。否则，正常的 Effect 中的布局计算将由于过时的样式而出错。

```
// 在你的 CSS-in-JS 库中
let isInserted = new Set();
function useCSS(rule) {
  useInsertionEffect(() => {
    // 不建议在运行时注入 <style> 标签。
    // 如果你必须这样做，那么应当在 useInsertionEffect 中进行。
    if (!isInserted.has(rule)) {
      isInserted.add(rule);
      document.head.appendChild(getStyleForRule(rule));
    }
  });
  return rule;
}

function Button() {
  const className = useCSS('...');
  return <div className={className} />;
}
```

