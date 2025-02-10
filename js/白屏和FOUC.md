如何异步加载脚本？

异步加载即非阻塞加载，浏览器在加载JS的同时，还会继续进行后续页面的处理。
异步加载脚本的方案：
1 .defer
异步加载，但要等到dom文档全部解析完才会被执行。只有IE能用。

<script type="text/javascript" defer="defer">
//代码区
</script>

2.async：

异步加载，加载完就执行，async只能加载外部脚本，不能把js写在script 标签里。

<script type="text/javascript" src="demo.js" async="async"></script>

3.创建script，插入到DOM中，加载完毕后callBack

defer是有顺序的，等文档读取和解析完成之后，再开始解析脚本。
async是不保证顺序的，读取和解析文档的过程会和解析脚本的过程同时进行。



解释浏览器的渲染机制

浏览器解析
1、浏览器通过请求的 URL 进行域名解析，向服务器发起请求，接收文件（HTML、CSS、JS、Images等等）。
2、HTML 文件加载后，开始构建 DOM Tree
3、CSS 样式文件加载后，开始解析和构建 CSS Rule Tree
4、Javascript 脚本文件加载后， 通过 DOM API 和 CSSOM API 来操作 DOM Tree 和 CSS Rule Tree

浏览器渲染
1、浏览器引擎通过 DOM Tree 和 CSS Rule Tree 构建 Rendering Tree
2、Rendering Tree 并不与 DOM Tree 对应，比如像 <head> 标签内容或带有 display: none; 的元素节点并不包括在 Rendering Tree 中 。
3、通过 CSS Rule Tree 匹配 DOM Tree 进行定位坐标和大小，是否换行，以及 position、overflow、z-index 等等属性，这个过程称为 Flow 或 Layout 。
4、最终通过调用Native GUI 的 API 绘制网页画面的过程称为 Paint 。



repaint 和 reflow 分别指什么

repaint 是重绘
不涉及任何DOM元素的排版问题的变动为，例如元素的color/text-align/text-decoration等等属性的变动。

reflow 是回流
除上面所提到的DOM元素style的修改基本为。例如元素的任何涉及长、宽、行高、边框、display等style的修改

repaint和reflow的目的是：展示一个新的页面样貌。



白屏：
就是在加载一个网页时，屏幕是白色的，之后全部样式才呈现。 如果把**样式放在文档底部**,浏览器会等HTML和CSS完全加载完成之后再绘制到屏幕上去，譬如我们打开某些国外的网站可能出现加载时间过长，页面会出现白屏,而不是内容逐步展现。
使用 @import 标签，即使 CSS 放入 link, 并且放在头部,也可能出现白屏。
对于图片和CSS, 在加载时会并发加载(如一个域名下同时加载两个文件)。 但在加载 JavaScript 时，会禁用并发，并且阻止其他内容的下载. 所以**把 JavaScript 放入页面顶部**也会导致白屏现象.



FOUC： 浏览器样式闪烁

就是当加载一个网页时，首先会出现一些内容但是样式并没有完全加载好，过一段时间后样式才发生变换。或者样式时一个个加载并呈现的。
如果把样式放在底部,对于FireFox浏览器，会逐步加载无样式的内容，等CSS加载完成之后突然展现样式。
这是由于FireFox的渲染逻辑是解析HTML就会直接画到页面上，这时你会看到没有样式的内容，CSS再通过不断的解析将页面重绘一遍，也就是闪烁一下突然展现样式，这就是FOUC。