### State Hook

#### useState()

为函数组件引入状态（state）

```
const [state, setState] = useState(initialState)
```

- 返回一个数组，数组的第一个成员是一个变量，指向状态的当前值；第二个成员是一个函数，用来更新**下一次渲染的状态变量**，约定是`set`前缀加上状态的变量名

- setState 使用：

  - 要设置的状态是根据先前的状态计算得出时，需要传入**更新函数**

  - 更新状态中的对象和数组应该直接**替换而不是改变现有对象**

- 使用 `useState` API 时，**传入一个函数，避免重新创建忽略的初始状态**。

  普通的方式：

  ```
  // 直接传入一个值，在每次 render 时都会执行 createRows 函数获取返回值
  const [rows, setRows] = useState(createRows(props.count));
  ```

  优化后的方式（推荐）：

  ```
  // 传入函数本身，createRows 只会在初始化时执行一次
  const [rows, setRows] = useState(() => createRows(props.count));
  ```



**setState 到底是同步还是异步**

https://7km.top/interview/01-setstate

所谓同步还是异步指的是调用 setState 之后是否马上能得到最新的 state，最终决定`setState`是同步渲染还是异步渲染的关键因素是`ReactFiberWorkLoop`工作空间的执行上下文，执行上下文为空时才是同步的

在 react 可调度范围内的 setState 是异步的，反之是同步的

- < v18
  - react 合成事件内同步执行的 setState 的是可调度范围，比如点击事件；setTimeout，.then 或直接在 DOM 上绑定的事件都是不可调度范围，上下文为空
  - 可调度范围会开启事件批处理，触发 schedule 调度，表现为异步刷新；不在可调度范围内会进行同步刷新
  - 批处理的关键在于对比任务优先级，优先级相同则进行批处理

- v18：concurrent 模式下都为异步；legacy 模式下和 < v18 相同
  - 可以通过 flushSync API 实现同步效果

```
state = {
  count: 0,
};
  
handleClick = () => {
  this.setState({ count: 1 });
  console.log('count: ', this.state.count);

  this.setState({ count: 2 });
  console.log('count: ', this.state.count);

  setTimeout(() => {
    this.setState({ count: 3 });
    console.log('count: ', this.state.count);

    this.setState({ count: 4 });
    console.log('count: ', this.state.count);
  }, 0);
};

<v18: 输出 0 0 3 4
v18: 输出 0 0 2 2
```



#### useReducer()

与 setState 比较，useReducer 更适合于 state 是个数组或对象，更新逻辑复杂，比如一个操作需要修改很多 state的场景

useReducer() 方法使得组件只需要发出action，而无需知道如何更新状态，解耦了操作逻辑(action)和后续的行为(一般是 UI 的更新）

```
const initialState = {count: 0}

function reducer(state, action) {
	switch (action.type) {
		case 'increment': 
			return { count: state.count + 1 };
		case 'decrement': 
			return { count: state.count - 1 };
		default: 
			throw new Error();
	}
}

function counter() {
	const [state, dispatch] = useReducer(reducer, initialState);
	return (
		<>
			// useReducer会根据dispatch的action，返回最终的state，并触发rerender
			<button onclick={() => dispatch({type: 'increment'}}>+</button>
			<button onclick={() => dispatch({type: 'decrement'})}>-</button>
		</>
	)
}
```



**useState 是 useReducer的一种特殊情况, 也是最简单的情况**

- useState 使用的是内置的 basicStateReducer，useReducer 使用的是外部自定义的 reducer

```
const [state, dispatch] = useState({ count: 0 });

// 等价于
const [state, dispatch] = useReducer(
  function basicStateReducer(state, action) {
    return typeof action === 'function' ? action(state) : action;
  },
  { count: 0 },
);

// 当需要更新state时, 有2种方式
dispatch({ count: 1 }); // 1.直接设置
dispatch((state) => ({ count: state.count + 1 })); // 2.通过回调函数设置
```

