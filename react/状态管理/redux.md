![img](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGjjuK2ArCic0yglWtZQssLwBP1XWT0u6ricFTZbWwnnlHOIWNpfgDqct2vt9Qxic8L3sByCAdWaE2UTA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

1. 使用一个对象存储变量 （**state**）
2. 订阅模式实现对于该对象的变更响应处理 （**reducer**）
3. 定义更改对象变更的动作 （**action**）
4. 订阅该对象的变更，完成状态到视图的映射（**ui** **= fx(state)**）

```
function createStore(reducer, preloadedState) {
  let state = preloadedState
  const listeners = []

  function getState() {
    return state
  }

	// 保存一个监听回调数组并返回一个函数来移除新的回调
  function subscribe(listener) {
    listeners.push(listener)
    return function unsubscribe() {
      const index = listeners.indexOf(listener)
      listeners.splice(index, 1)
    }
  }

  function dispatch(action) {
    state = reducer(state, action)
    listeners.forEach(listener => listener())
  }


  return { dispatch, subscribe, getState }
}
```



**流程：**

- 初始启动：
  - 使用最顶层的 root reducer 函数创建 Redux store
  - store 调用一次 root reducer，并将返回值保存为它的初始 `state`
  - 当视图首次渲染时，视图组件访问 Redux store 的当前 state，并使用该数据来决定要呈现的内容。同时监听 store 的更新，以便他们可以知道 state 是否已更改。
- 更新环节：
  - 应用程序中发生了某些事情，例如用户单击按钮
  - dispatch 一个 action 到 Redux store，例如 `dispatch({type: 'counter/increment'})`
  - store 用之前的 `state` 和当前的 `action` 再次运行 reducer 函数，并将返回值保存为新的 `state`
  - store 通知所有订阅过的视图，通知它们 store 发生更新
  - 每个订阅过 store 数据的视图 组件都会检查它们需要的 state 部分是否被更新。
  - 发现数据被更新的每个组件都强制使用新数据重新渲染，紧接着更新网页



### Redux 对接到 React

- 使用 **useSyncExternalStore** ，直接用于订阅 Redux store 的状态变化。

```
const store = createStore(reducers);

fucntion App() {
	// ...
	const snapshot = useSyncExternalStore(store.subscribe, store.getState);
	// ...
}
```

- React-Redux 在 v8.0.0 版本中，已经内部实现了 `useSyncExternalStore`。如果使用 React-Redux 提供的 `useSelector` 和 `Provider`，不需要显式调用 `useSyncExternalStore`，React-Redux 的 `useSelector` 内部使用了 `useSyncExternalStore`



### combineReducer 

把管理每个 state slice 的逻辑组合起来，形成顶层的 reducer

```
// 不使用 switch，使用函数来创建一个 action 到 case 的查找表
function createReducer(initialState, handlers) {
	return function reducer(state = initialState, action) {
		if (handlers.hasOwnProperty(action.type)) {
			return handler[action.type](state, action);
		} else {
			return state;
		}
	}
}

// 处理整个 state 切片的 Handler ("slice reducer")
const visibilityReducer = createReducer('SHOW_ALL', {
  SET_VISIBILITY_FILTER: setVisibilityFilter
})

// Slice reducer
const todosReducer = createReducer([], {
  ADD_TODO: addTodo,
  TOGGLE_TODO: toggleTodo,
  EDIT_TODO: editTodo
})

// 顶层 reducer
const appReducer = combineReducers({
  visibilityFilter: visibilityReducer,
  todos: todosReducer
})
```

简单实现：

```
function combineReducer(reducers) {
	const reducerKeys = Object.keys(reducers);
	
	return function(state, action) {
		const nextState = {};
		for (let i = 0; i < reducerKeys.length; i++) {
			const key = reducerKeys[i];
			const reducer = reducers[key];
			// 先获取到之前 state 的值，这个是 createStore 中存储的值，该值是一个 state tree，包括了所有模块 state
			const previousState = state[key];
			nextState[key] =  reducer(previousState, action);
		}
		return nextState;
	}
}
```

 

### Middleware

Redux 使用一种称为 **middleware** 的特殊插件来让我们自定义 `dispatch` 函数。。**Redux middleware 在 dispatch action 和到达 reducer 之间提供第三方扩展点。** 人们使用 Redux middleware 进行日志记录、崩溃报告、异步 API 通信、路由等。

```
const store = createStore(todos, ['Use Redux'], applyMiddleware(logger))

const store = createStore(
  reducer,
  compose(
    applyMiddleware(thunk),
    DevTools.instrument()
  )
)
```

- 想要使用多个 store enhancer，可以使用 [`compose()`](https://cn.redux.js.org/api/compose) 方法。

``` 
// 从右到左来组合多个函数
function compose(...fns) {
	return function(args) {
		return fns.reduceRight((acc, fn) => fn(acc), arg);
	}
}
```

