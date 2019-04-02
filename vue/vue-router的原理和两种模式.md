## vue-router的原理和各种模式

https://zhuanlan.zhihu.com/p/27588422?hmsr=toutiao.io

https://juejin.im/post/5bc6eb875188255c9c755df2

https://www.cnblogs.com/yanze/p/7644631.html

前端路由：直接找到与地址匹配的一个组件或对象并将其渲染出来。=> 匹配不同的 url 路径，进行解析，然后动态的渲染出区域 html 内容。

单页应用（SPA）：更新视图而不重新请求页面。

大型单页应用最显著特点之一就是采用前端路由系统，通过改变URL，在不重新请求页面的情况下，更新页面视图。

#### vue-router是什么？

路由就是SPA（单页应用）的路径管理器。 换句话说，vue-router就是WebApp的链接路径管理系统用于构建单页面应用



vue-router 提供了三种运行模式：

● hash: 使用 URL hash 值来作路由。默认模式。

● history: 依赖 HTML5 History API 和服务器配置。查看 HTML5 History 模式。

● abstract: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。

默认Hash-->如果浏览器支持History新特性改用History-->如果不在浏览器环境则使用abstract

#### hash模式

#符号本身以及它后面的字符称之为hash，可通过window.location.hash属性读取。

通过 hash 来实现路由，url hash 就是类似于：

```bash
http://www.xxx.com/#/login
```

后面 hash 值的变化，并不会导致浏览器向服务器发出请求，浏览器不发出请求，也就不会刷新页面。

另外每次 hash 值的变化，还会触发`hashchange` 这个事件，通过这个事件我们就可以知道 hash 值发生了哪些变化。然后我们便可以监听`hashchange`来实现更新页面部分内容的操作。

hash（#）是URL 的锚点，代表的是网页中的一个位置，单单改变#后的部分（/#/..），浏览器只会加载相应位置的内容，不会重新加载网页，也就是说 #是用来指导浏览器动作的，对服务器端完全无用，HTTP请求中不包括#；同时每一次改变#后的部分，都会在浏览器的访问历史中增加一个记录，使用”后退”按钮，就可以回到上一个位置；**所以说Hash模式通过锚点值的改变，根据不同的值，渲染指定DOM位置的不同数据**。

`hash` 模式下，仅 `hash` 符号之前的内容会被包含在请求中，如 `http://www.abc.com`，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回 404 错误。

#### history模式

利用History interface在 HTML5中新增的方法，history.pushState API 来完成 URL 跳转而无须重新加载页面。

需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 http://oursite.com/user/id 就会返回 404，这就不好看了, 给个警告，后台因为这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，你应该在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面。



#### abstract模式

abstract模式是使用一个不依赖于浏览器的浏览历史虚拟管理后端。

通过数组模拟浏览器历史记录栈的功能。

