### useRef()

- 在组件的整个生命周期中保持一个可变的值，而不会因为组件重新渲染而丢失或重置。（普通对象存储的值每次渲染都会重置）
- 不同于状态（`useState`），`useRef` 的值更新**不会触发组件重新渲染**，所以 ref 不适合用于存储期望显示在屏幕上的信息
- **不要在渲染期间写入或者读取 `ref.current`**，可以在 **事件处理程序或者 Effect** 中读取和写入 ref，否则会破坏 React 的幂等性



####  使用场景

- **使用 ref 引用一个值**：存储一些不影响组件视图输出信息

```
export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    // 在这里使用 ref 而不是 state 是可以的，因为点击次数只在事件处理程序中被读取和写入。
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      点击！
    </button>
  );
}

```



- **使用 ref 引用 DOM**：
  - 当 React 创建 DOM 节点并将其渲染到屏幕时，React 将会把 DOM 节点设置为 ref 对象的 `current` 属性，当节点从屏幕上移除时，React 将把 `current` 属性设置回 `null`。
  - 自定义组件无法直接获取 ref，需要将其包装在 **forwardRef** 里，使父组件可以获得它的 ref
    - `forwardRef` 返回一个可以在 JSX 中渲染的 React 组件。
    - 与作为纯函数定义的 React 组件不同，`forwardRef` 返回的组件还能够接收 `ref` 属性，可以用来在多个组件中转发 ref
  - **不要滥用 ref。** 你应当仅在你没法通过 prop 来表达 *命令式* 行为的时候才使用 ref：例如，滚动到指定节点、聚焦某个节点、触发一次动画，以及选择文本等等。**如果可以通过 prop 实现，那就不应该使用 ref**。


```
function myComponent() {
	const inputRef = useRef();
	
	function handleClick() {
		inputRef.current.focus();
	}
	return  <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        聚焦输入框
      </button>
    </>
}
```



### useImperativeHandle

可以自定义由 [ref](https://zh-hans.react.dev/learn/manipulating-the-dom-with-refs) 暴露出来的句柄，指定需要暴露的方法

```
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});
```

如果你的父组件获得了 `MyInput` 的 ref，就能通过该 ref 来调用 `focus` 和 `scrollIntoView` 方法。然而，它的访问是受限的，无法读取或调用下方 `<input>` DOM 节点的其他所有属性和方法。
