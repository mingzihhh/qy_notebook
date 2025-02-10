# dns-prefetch，prerender，prefetch，preload

<https://blog.csdn.net/luofeng457/article/details/88409903>

<https://blog.csdn.net/jyb123/article/details/86630677>

<https://zhuanlan.zhihu.com/p/48521680>

### dns-prefetch

DNS prefetch，即DNS预获取。

前端优化中与DNS有关的两点：一个是减少DNS的请求次数，另一个是进行DNS预获取。

DNS预获取可以加快页面渲染速度，无需用户点击链接就能在后台解析，所以能减少用户的等待时间，提升用户体验；

默认情况下，浏览器会对当前页面中和当前域名（正在浏览网页的域名）不在同一个域的域名进行预获取，并且缓存结果，这就是隐式的DNS Prefetch。目前大多数浏览器已经支持此属性（IE9+

### preload

浏览器会在遇到如下link标签时，立刻开始下载main.js(不阻塞 non-render-blocking )，并放在内存中，但不会执行其中的JS语句。

只有当遇到script标签加载的也是main.js的时候，浏览器才会直接将预先加载的JS执行掉。

使用 `as` 来指定将要预加载的内容的类型，将使得浏览器能够：

- 更精确地优化资源加载优先级。
- 匹配未来的加载需求，在适当的情况下，重复利用同一资源。
- 为资源应用正确的[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)。
- 为资源设置正确的 [`Accept`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept) 请求头。

> as的取值如下：
> audio: 音频文件。
> document: 一个将要被嵌入到<frame>或<iframe>内部的HTML文档。
> embed: 一个将要被嵌入到<embed>元素内部的资源。
> fetch: 那些将要通过fetch和XHR请求来获取的资源，比如一个ArrayBuffer或JSON文件。
> font: 字体文件。
> image: 图片文件。
> object: 一个将会被嵌入到<embed>元素内的文件。
> script: JavaScript文件。
> style: 样式表。
> track: WebVTT文件。
> worker: 一个JavaScript的web worker或shared worker。
> video: 视频文件。
>

```
<!-- 跨域请求文件需要设置crossorigin, 如果是字体文件即使不跨域也需要设置 -->
<link rel="preload" href="fonts/cicle_fina-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
```



### preconnect

浏览器要建立一个连接，需要经过DNS查找，TCP三次握手和TLS协商（https需要)，这些过程需要相当的耗时，所以preconnect，就是一项使浏览器预先建立一个连接（包含DNS查找，TCP三次握手和TLS协商)，等真正需要加载资源的时候能直接请求； 

```
<link rel="preconnect" href="//example.com">
<link rel="preconnect" href="//cdn.example.com" crossorigin>
```

浏览器会进行以下步骤： 

* 解释href的属性值，如果是合法的URL，然后继续判断URL的协议是否是http或者https否则就结束处理 
* 如果当前页面host不同于href属性中的host,crossorigin其实被设置为anonymous(就是不带cookie了)，如果希望带上cookie等信息可以加上crossorign属性,corssorign就等同于设置为use-credentials

### prefetch

prefetch是一种利用浏览器的空闲时间加载页面将来可能用到的资源的一种机制；通常可以用于加载非首页的其他页面所需要的资源，以便加快后续页面的首屏速度；

prefetch加载的资源可以获取非当前页面所需要的资源，并且将其放入缓存至少5分钟（无论资源是否可以缓存）；并且，当页面跳转时，未完成的prefetch请求不会被中断；

设置此值能让浏览器预加载一个资源（html，js，css或图片），可以让用户**跳转到其他页面**时，响应速度更快。例如：

```
<link rel="prefetch" href="//example.com/next-page.html" as="html" crossorigin="use-credentials">
<link rel="prefetch" href="/library.js" as="script">
```

注意： 虽然是预加载了，但是页面不会解析或者JS是不会直接执行的。

### prerender

prerender不仅会加载资源，还会解执行页面，进行预渲染，但是这都是根据浏览器自身进行判断。 

浏览器可能会

- 分配少量资源对页面进行预渲染
- 挂起部分请求直至页面可见时
- 可能会放弃预渲染，如果消耗资源过多等等情况。。。

```
<link rel="prerender" href="//example.com/next-page.html">
```



### preload和prefetch的区别

- preload预加载优先级高，当前页面需要的资源；prefetch预加载优先级低，将来页面可能需要的资源

- 当一个资源被 preload 或者 prefetch 获取后，它将被放在内存缓存中等待被使用，如果资源位存在有效的缓存极致（如 cache-control 或 max-age），它将被存储在 HTTP 缓存中可以被不同页面所使用。

  正确使用 preload/prefetch 不会造成二次下载，也就说：**当页面上使用到这个资源时候 preload 资源还没下载完，这时候不会造成二次下载，会等待第一次下载并执行脚本**。

  **对于 preload 来说，一旦页面关闭了，它就会立即停止 preload 获取资源，而对于 prefetch 资源，即使页面关闭，prefetch 发起的请求仍会进行不会中断。**

