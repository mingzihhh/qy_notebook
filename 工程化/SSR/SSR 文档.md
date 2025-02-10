## 业务场景

- 客户第一次访问帮助中心时 SSR 加载，后续路由 CSR，由 BFF 层对接知识中心
- 页面文章信息相关部分 SSR，登录态下的用户信息展示和通知 CSR
- 当用户有登录态时，从浏览器里取 lang 信息
- 当用户没有登录态时，默认 en



**数据获取**

- data loader：在浏览器/服务端可以同构的获取数据
-  client loader：在 data loader 报错降级或者浏览器切换路由时，像 CSR 应用一样执行 loader，而不是向 SSR 服务发起请求



**流式 SSR**

- 更好的性能控制（页面加载的优先级和顺序），用户体验（更快看到内容），更好的适应性（不同网络速度/设备性能）
- 为爬虫等待所有内容加载完毕：当一个爬虫访问页面时，可能需要先加载所有内容，而不是渐进式的加载它
  - 使用 is-robot 对请求的 user-agent 判断是否是爬虫



**SSR 页面缓存**

- stale-while-revalidate

- 默认将 pathname 作为 key

  

**SSR 降级**

- data loader 报错：在服务端直接渲染 <ErrorBoundary> 组件；如果定义了  client loader，优先使用  client loader 发起数据请求，如果重新渲染仍然出错，再展示  <ErrorBoundary> 组件
- react 组件在服务端渲染出错：页面降级为 CSR，并利用 data loader 中已有的数据进行渲染，如果重新渲染仍然出错，再展示  <ErrorBoundary> 组件



**运行环境差异**

如果 SSR Bundle 里存在 Web API，或者在 CSR 里存在 Node API 都会导致运行出错

- 应用自身代码问题：判断当前环境，分别处理，在构建时删除无用代码
  - **如何判断 node 和 browser 环境**
    - 根据全局变量区分：browser 有 window / document，node 有 global / process
    - 通过 webpack 打包时在 browser 端注入环境变量，比如 process.browser 为 true

- 应用依赖的包中存在副作用：因为社区中的包并不都支持在两个运行环境中运行，有些包也无需在两个环境中运行
  - 创建同名的 a.ts / a.server.ts 做一层代理，在文件中直接引入 ./a，SSR 打包时会优先使用 server.ts 文件，CSR 打包下会使用.ts 文件



**常见问题**

- 渲染不一致：
  - 说明某些部分内容不需要在 SSR 中渲染，包裹 No SSR 组合
  - 有些应用的 UI 展示会和设备相关，比如 user-agent，在组件中获得请求信息，进行判断
- 内存泄露：
  - SSR 时，浏览器的每次请求都会触发服务器重新执行一次组件渲染逻辑，所以避免在全局定义可能会不断增长的数据结构，或在全局进行事件订阅，或创建不会被销毁的流

