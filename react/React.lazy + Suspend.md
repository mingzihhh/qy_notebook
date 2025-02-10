## React.lazy + Suspend

#### **动态加载**

基于 SPA 的特性，通过**按需加载**的方式，等到用户需要进入某些页面/组件时再加载对应的模块代码。

#### **原理：动态 import** + code-splitting

code-splitting 通过重新将模块进行组合，使用一些策略将其分割生成若干 chunk，实现按需加载或被浏览器缓存，进而实现前端应用代码量加载体积减少，最终达到更快速的浏览器加载，更高的 CDN 缓存命中率

ES 标准中的 import() 函数提供了原生动态加载支持，当 webpack 检测到 import() 存在时，将会自动进行 code-splitting，将动态 import 的模块打到一个新 bundle 中。

```
// 假设我们动态导入一个模块
import('path/module').then((module) => {
  // 使用模块
}).catch(console.error);
// 若加载模块异常，可在catch中处理。
```

当我们需要去 指定每个 异步组件 打包的`js`文件名时，我们只需要在 `import()` 函数中 添加 `/* webpackChunkName: "title" */` 这段注释，webpack 在打包时，自动会将我们指定的名字作为文件名。

#### React.lazy + Suspend

从 16.6.0 版本开始，React 原生提供了 code-splitting 与组件动态加载的方案，引入了 lazy 函数，同时也支持使用 Suspense 与 ErrorBoundary 来进行异常处理与 fallback 展示。

##### LazyComponent

react.lazy 是 `react` 提供的组件懒加载的能力，返回一种特殊的虚拟DOM结构——LazyComponent

`React.lazy` 接受一个 load 函数，这个函数内部调用 `import()` 动态导入。它必须返回一个 `Promise`，该 `Promise` 需要 `resolve` 一个 `defalut export` 的 `React` 组件。React 会将 .default 渲染为 React 组件，返回的 Promise 和 Promise 的解析值都会被缓存，所以 React 不会多次调用 load 函数。如果 Promise 被拒绝，则 React 将抛出拒绝原因给最近的错误边界处理。

LazyComponent 的加载逻辑中，核心原则就是：

- 加载完成后直接返回组件模块本身
- 加载失败抛出错误
- **首次加载或加载中的组件将 promise 对象以throw Error的方式抛出 （throw promise）** 

```
function lazy(fn) {
	const fetcher = {
		status: 'pending',
		result: null,
		promise: null,
	}
	return function MyComponent() {
		const getDataPromise = fn();
		fetcher.promise =  getDataPromise;
		
		getDataPromise.then(res => {
			fetcher.status = 'resolved';
			fetcher.result = res.default;
		})
		
		useEffect(() => {
			if(fetcher.status === 'pending') {
				throw fetcher;
			}
		},[])
		
		if(fetcher.status === 'resolved') {
			return fetcher.result;
		}
		return null
	}
}
```



#### Suspense

Suspense 组件主要处理了抛出的 promise 与传入的 fallback，首先渲染 fallback，promise resolve 之后加载动态组件

```
import React, { Suspense } from 'react';

const LazyAaa = React.lazy(() => import('./Aaa'));

export default function App() {
  return <div>
    <Suspense fallback={'loading...'}>
      <LazyAaa></LazyAaa>
    </Suspense>
  </div>
}
```

https://juejin.cn/post/7315231440777527334?searchId=202404092118261E1C1F2D5E48432E9468

简单实现：

```
export default class Suspense extends React.PureComponent {
	state = {
		isRender: true // 异步组件是否就绪，可以渲染
	}
	
	// 处理 lazy 中的 throw fetcher
	componentDidCatch(e) {
		this.setState({isRender: false});
		e.promise.then(() => {
			this.setState({isRender: true})
		})
	}
	
	render() {
		const {fallback, children} = this.props;
		const {isRender} = this.state;
		return isRender ? children : fallback;
	}
}
```



**触发条件：throw promise**

只要 throw 一个 promise，就会被最近的 Suspense 捕获

- 一般配合 React.lazy 使用，React.lazy 包裹之后，也会 throw 一个 promise 来触发 Suspense

- 单独使用时

  - 支持 Suspense 的框架，比如 jotai、next.js 等，原理是框架内部做了 throw promise（实现了下面的 use hook）

  - react 19：use hook，参数是 promise

    - 当传递给 use 的 promise 在 pending 的时候，展示 Suspense 的 fallback

    - 当传递给 use 的 promise 是 resolve 的时候，展示 Suspense 的子组件

    - 当传递给 use 的 promise 是 reject 的时候，展示 ErrorBoundary 的 fallback

      



#### ErrorBoundary

如果当一个组件异步加载下载 `js` 文件时，网络错误，无法下载 `js` 文件，那么 `react` 会表现出什么情况呢

```
Error: Loading chunk 3 failed.
  (error: http://192.168.4.183:3000/static/js/title.chunk.js)
```

很明显，`Suspense` 无法处理这种错误情况， 在 `react` 中有一个 错误边界 `（Error Boundaries）`的概念，用来解决这种问题

**自 React 16 起，任何未被错误边界捕获的错误将会导致整个 React 组件树被卸载。**

 ErrorBoundary 是 class 组件才有的特性，一般直接用 react-error-boundary 这个包

**触发条件：throw error**

组件抛错（throw error）的时候，会向上寻找最近的 ErrorBoundary 组件

```
import { Component } from "react";

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = {
      hasError: false
    };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, message: error.message };
  }

  componentDidCatch(error, errorInfo) {
  	// 可以将错误日志上报给服务器
    console.log(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <div>出错了： {this.state.message}</div>;
    }
    return this.props.children;
  }
}
```

当子组件报错的时候，会把错误传递给它的 getDerivedStateFromError 和 componentDidCatch 方法。

如果一个 class 组件中定义了 [`static getDerivedStateFromError()`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#static-getderivedstatefromerror) 或 [`componentDidCatch()`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#componentdidcatch) 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。

- **getDerivedStateFromError** 接收 error，返回一个新的 state，会触发重新渲染来显示错误对应的 UI。

- **componentDidCatch** 接收 error 和堆栈 info，可以用来打印错误日志。

错误边界**无法**捕获事件处理器内部的错误

- 使用错误边界捕获render 方法，生命周期方法以及构造函数中出现的错误

- 使用 try / catch 捕获事件处理器内部的错误。



#### 实际使用

react-lazy-with-preload

包装React.lazy（）API，添加在首次呈现组件之前预加载组件的能力。
