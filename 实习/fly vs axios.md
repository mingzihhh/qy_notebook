

## fly vs axios

fly.js，一个支持所有JavaScript运行环境的基于Promise的、支持请求转发、强大的http请求库。

<https://juejin.im/post/59eee969f265da430e4e5853>

<https://wendux.github.io/dist/#/doc/flyio/engine>

### 共同点

1. 都支持Promise API
2. 都同时支持Node和Browser环境
3. 都支持请求／响应拦截器
4. 都支持自动转换 JSON

### 不同点

1. fly更轻量，集成成本更低
2. Node下 Fly 的功能要明显强于axios，Fly在node下不仅提供了文件下载、上传的API，而且还可以通过 `fly.$http` 直接调用 [request 库 ](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Frequest%2Frequest)的所有功能
3. fly支持混合app中请求转发
4. fly提出http engine，替换XMLHttpRequest，**Fly 正是通过在不同环境切换不同engine的方式实现支持不同的javascript运行环境的**。

