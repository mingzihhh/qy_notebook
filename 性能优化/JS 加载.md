### 正常模式

```xml
<script src="index.js"></script>
```

这种情况下 JS 会阻塞 dom 渲染，浏览器必须等待 index.js 加载和执行完成后才能去做其它事情

### async 

```xml
<script async src="index.js"></script>
```

async 模式下，它的加载是异步的，JS 不会阻塞 DOM 的渲染，async 加载是无顺序的，当它加载结束，JS 会立即执行

**使用场景**：若该 JS 资源与 DOM 元素没有依赖关系，也不会产生其他资源所需要的数据时，可以使用async 模式，比如埋点统计

### defer 模式

```xml
<script defer src="index.js"></script>
```

defer 模式下，JS 的加载也是异步的，defer 资源会在 `DOMContentLoaded` 执行之前，并且 defer 是有顺序的加载

如果有多个设置了 defer 的 script 标签存在，则会**按照引入的前后顺序执行**，即便是后面的 script 资源先返回

所以 defer 可以用来控制 JS 文件的执行顺序，比如 element-ui.js 和 vue.js，因为 element-ui.js 依赖于 vue，所以必须先引入 vue.js，再引入 element-ui.js

```xml
<script defer src="vue.js"></script>
<script defer src="element-ui.js"></script>
```

**使用场景**：一般情况下都可以使用 defer，特别是需要控制资源加载顺序时

### module 模式

```xml
<script type="module">import { a } from './a.js'</script>
```

在主流的现代浏览器中，script 标签的属性可以加上 `type="module"`，浏览器会对其内部的 import 引用发起 HTTP 请求，获取模块内容。这时 script 的行为会像是 defer 一样，在后台下载，并且等待 DOM 解析

**使用场景**：Vite 就是利用浏览器支持原生的 `es module` 模块，开发时跳过打包的过程，提升编译效率

### preload

```ini
<link rel="preload" as="script" href="index.js">
```

link 标签的 preload 属性：用于提前加载一些需要的依赖，这些资源会优先加载（如下图红框）

![preload.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c4b39cd07844848b3900bd944790af6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

vue2 项目打包生成的 index.html 文件，会自动给首页所需要的资源，全部添加 preload，实现关键资源的提前加载

![htmlPreload.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5f3ac75c68f940e99c7825b958d25684~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

preload 特点：

1. preload 加载的资源是在浏览器渲染机制之前进行处理的，并且不会阻塞 onload 事件；
2. preload 加载的 JS 脚本其加载和执行的过程是分离的，即 preload 会预加载相应的脚本代码，待到需要时自行调用；

### prefetch

```ini
<link rel="prefetch" as="script" href="index.js">
```

prefetch 是利用浏览器的空闲时间，加载页面将来可能用到的资源的一种机制；通常可以用于加载其他页面（非首页）所需要的资源，以便加快后续页面的打开速度

![prefetch.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5ea8026dd2e42e1bd5c709178f0235f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

prefetch 特点：

pretch 加载的资源可以获取非当前页面所需要的资源，并且将其放入缓存至少5分钟（无论资源是否可以缓存）

2. 当页面跳转时，未完成的 prefetch 请求不会被中断



**最佳实践**

- 大部分场景下无需特意使用preload
- 类似字体文件这种隐藏在脚本、样式中的首屏关键资源，建议使用preload
- 异步加载的模块（典型的如单页系统中的非首页）建议使用prefetch
- 大概率即将被访问到的资源可以使用prefetch提升性能和体验

