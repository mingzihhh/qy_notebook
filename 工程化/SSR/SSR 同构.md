同构 SSR 就是采用一套代码，构建双端逻辑，既能在服务端渲染完成页面结构，又能在客户端渲染，绑定各客户端交互事件

同构的前提

1. Node.js 存在：JavaScript 可以在 Node 环境执行
2. 虚拟 DOM：Node.js 环境下没有 DOM 概念，虚拟 DOM 为 Node.js  操作 DOM 提供可能



https://github.com/TimorCookie/react-ssr

### 路由同构

#### 服务器

浏览器输入 url 后，服务器接收到请求，进行路由分析找到匹配的路由组件

- **使用 StaticRouter**：用于在 node 中渲染 React Router Web 应用程序

  - 不依赖浏览器的 `history` 或 URL 变化，而是依赖手动传入的 `location` 和 `context`。
  - 通过`location`属性提供当前路径，可以在服务器端渲染时传递请求的路径
  - 通过`context`属性保存路由信息，比如是否发生了重定向，服务端可根据 `context` 的值做进一步处理。

  

### 数据同构

通常在查找到要渲染的组件后，需要预先得到此组件所需要的数据，将数据传递给组件后，再进行组件的渲染。**数据预取同构，就是解决双端如何使用同一套数据请求方法来进行数据请求。**

#### 服务器

- 给组件定义静态方法来处理，在 server 端和组件内都可以直接通过组件来进行访问。
- 路由改造，导出路由配置供服务端获取，服务端可以根据 path map 到对应 component
- 服务端判断组件上是否存在 `getInitialData` 方法，如有则执行该函数获取组件初始数据并存入 store 中，最后再拼接 html 返回渲染

```
  const promiseArray = routesConfig?.map((route) => {
    const { component = null, path = "" } = route
    if (req?.url === path && component?.getInitialData) {
      return component.getInitialData(store)
    } else {
      return null
    }
  })
```



#### 客户端

- 如果只有服务器有预取数据，当客户端进行首次组件渲染的时候没有初始化的数据，渲染出的节点肯定和服务端直出的节点不同，导致组件重新渲染，页面抖动

- 利用 `configureStore` 创建时可以传入 `preloadedState` 参数的特性，比如

  - 服务器获取数据后，在返回 html 时将数据挂在 window 上，如 `window.__PRELOADED_STATE__=${JSON.stringify(preloadedState)}`
  - 客户端初始化时，获取 window 上的数据并初始化 store

  

### 渲染同构

#### 服务器

- 使用 **renderToString** 实现 react 组件渲染，将 React 元素转化为 HTML，并在首次请求时将标记下发，以加快页面加载速度，并允许搜索引擎爬取你的页面以达到 `SEO` 优化的目的。这个过程会过滤掉事件

  ```
      const content = renderToString(
        <Provider store={store}>
          <StaticRouter location={req.url}><App /></StaticRouter>
        </Provider>
       )
  ```


- 流式 SSR：**renderToReadableStream**，通过二进制流替代字符串类型返回。



#### 客户端

浏览器渲染接收到页面内容（此时页面不会白屏，因为已经有内容），当读取到 script 标签时再次向服务器发送请求，服务器接收请求并返回对应的 js 文件，浏览器执行 js 中的代码，渲染页面（js 中的代码会接管页面交互）

- 调用 **hydrateRoot** 使服务器生成的 HTML 具有交互性，注入state props context 等：react 提供的 hydrateRoot 函数将 React 组件逻辑连接到由 React 在服务端环境中渲染的现有 HTML 中。使用 hydrateRoot 后，react 将会连接到内部有 domNode 的 HTML 上，然后接管其中的 domNode。

**为什么服务端加载了一次，客户端还需要再次加载呢？**

- 原因很简单，服务端使用 `renderToString` 渲染页面，而 `react-dom/server` 下的 `renderToString` 并没有做事件相关的处理，因此返回给浏览器的内容不会有事件绑定，渲染出来的页面只是一个静态的 `HTML` 页面。只有在客户端渲染 `React` 组件并初始化 `React` 实例后，才能更新组件的 `state` 和 `props` ，初始化 `React` 的事件系统，让 `React` 组件真正“ 动” 起来。
- React 组件不会加载两次，如果在已有服务端渲染标记的节点上调用 `ReactDOM.hydrate()` 方法， `React` 将会保留该节点且只进行事件处理绑定，提供一个非常高性能的首次加载体验



**createRoot 和 hydrateRoot 的区别**

- createRoot 在浏览器的 DOM节点中创建根节点显示 React 组件
- hydrateRoot 使用 react-dom/server 生成的 html 在根节点中展示 React 组件



### 打包设置

#### 服务器

- 引入 babel：在服务端引入 React 时用了 `import` 语法，而在服务端环境（Node.js）中默认使用的是 CommonJS 规范，只能使用 `require` 语句进行导入。（import 是 ES6 中模块化写法），所以我们得用一些打包工具对 `server.js` 进行编译打包操作来处理成能够在服务端环境运行的代码
- 使用 webpack-node-externals： `node_modules` 中的模块不会被打包，作为外部依赖处理，因为这些代码直接运行在 Node.js 环境中，可以通过 `require` 动态加载依赖。

