## react-router-dom

<https://juejin.im/post/5be2993df265da611e4d220c>

<https://juejin.im/post/5a7e9ee7f265da4e7832949c>

与react-router的区别：react-router-dom在react-router的基础上扩展了可操作dom的api。

>### SPA的原理
>
>单页应用的原理用两种，一种是通过hash的变化，改变页面，另一种是通过url的变化改变页面。
>
>- hash 
>  - window.location.hash='xxx' 改变hash
>  - window.addEventListener('hashchange',fun) 监听hash的改变
>- url 
>  - history.pushState(obj,title,'/url') 改变url
>  - window.addEventListener('popstate',fun) 当浏览器向前向后时，触发该事件。

### router组件

- Router是一个外层，最后render的是它的子组件，不渲染具体业务组件。

```
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  holder
);
```

- 分为HashRouter(通过改变hash)、BrowserRouter(通过改变url)、MemoryRouter

  `<BrowserRouter>` 应该用在服务器处理动态请求的项目中（知道如何处理任意的URI），`<HashRouter>` 用来处理静态页面（只能响应请求已知文件的请求）

- 每个router都会创建一个history的对象，用来保持对当前location序列的追踪还有在页面发生变化的时候重新渲染页面。

  ```
  //location与现在页面的url相关，history可以管理location序列（添加/移除）
  //https://medium.com/@pshrmn/a-little-bit-of-history-f245306f48dd
  {
    pathname: '/here',
    search: '?key=value',
    hash: '#extra-information',
    state: { modal: true },
    key: 'abc123'
  }
  ```

- React Router 提供的其他组件依赖在 `context` 上储存的 `history` 对象，所以他们必须在 `router` 对象的内部渲染。**一个没有 router 祖先元素的 React Router 对象将无法正常工作**



### 路由匹配组件

#### 路由匹配

- 将<Route>组件的path属性与当前的location的pathname进行比较来完成的。

- 当一个<Route>匹配了，它所对应的组件内容将被渲染出来；不匹配，则渲染null；

- 如果一个<Route>没有path属性，他的组件对应内容将一直被渲染出来。

- 当路由与路径匹配的时候会传入一系列的props, 有 `match` 对象，当前的 `location` 对象 ，还有 `history` 对象（由 router 创建）

  **match对象：**

  - `url` - 当前路径与路由相匹配的部分
  - `path` - 路由的`path`
  - `isExact` - `path === pathname`
  - `params` - 一个包含着 `pathname` 被 `path-to-regexp` 捕获的对象。

  **location对象：**

  `<Route>` 和 `<Switch>` 组件都可以接受一个 `location` prop，这可以让他们被一个不同的 location 匹配到，而不仅仅是他们实际的 location（当前的 URL）。

#### route

- 负责渲染具体的业务组件，负责匹配url和对应的组件

- 接受三种 prop 来决定路径匹配时渲染的元素：

  - component- 一个 React 组件
  - render-一个返回 React 元素的函数，写成内联形式渲染和传递参数的时候非常方便。
  - children-一个返回 React 元素的函数，无论哪种路由都会渲染
  - 区别：component使用 `React.createElement` 来创建一个元素，render将组件视作一个函数

  ```
  <Route path='/page' component={Page} />
  
  const extraProps = { color: 'red' }
  <Route path='/page' render={(props) => (
    <Page {...props} data={extraProps}/>
  )}/>
  
  <Route path='/page' children={(props) => (
    props.match
      ? <Page {...props}/>
      : <EmptyPage {...props}/>
  )}/>
  ```

#### switch

- 将多个<Route>“包裹”在一起

- 迭代它下面的所有<Route>子组件，并只渲染第一个路径匹配的<Route>

  

### navigation组件

#### link

- `<Link>` 以适当的 href 去渲染一个可访问的`<a>`标签，属性to是字符串或对象。

- 如果我们使用 `<a>` 标签导航的话，将会载入一整个新的页面。React Router 提供了一个 `<Link>` 组件来避免这种情况，当点击 `<Link>` 时，URL 将会更新，页面也会在不载入整个新页面的情况下渲染内容。

- Link的跳转是用history.push完成的，a的href属性是history.createHref的返回值。

- 最后渲染的时候其实是创建了<a>标签，同时添加了一个onClick的监听事件，onClick事件处理函数中做了两件事：

  - 如果Link上定义了onClick方法，则执行该方法

  - 判定是否应该阻止a标签的默认跳转

    <https://juejin.im/post/5b064ebc51882538934ef28d>

#### NavLink

- 特殊的`<Link>`，当它的path与当前location匹配时，可以自定义其样式来表示当前页面位置。

#### Redirect

- Redirect跳转到某个路由，不渲染组件
- 通过history.createHref获得path，history.push跳转过去