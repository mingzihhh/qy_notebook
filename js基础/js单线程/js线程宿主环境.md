## js线程宿主环境

浏览器或node（宿主环境）是多线程的，JS引擎是单线程的

浏览器有很多线程，例如：

1、GUI 渲染线程

2、JS 引擎线程

3、定时器触发线程 (setTimeout)

4、浏览器事件线程 (onclick)

5、http 异步线程

6、EventLoop轮询处理线程

7、...

其中，1、2、4为常驻线程

**进程**：运行一个程序，资源分配的基本单位，比如打开一个网页

**线程**：一个进程包括多个线程互相配合，cpu调度的最小单位，比如打开网页的时候获取数据，渲染页面等

#### GUI渲染线程

js引擎与GUI引擎是互斥的。因为如果在GUI渲染的时候，js改变了dom，那么就会造成渲染不同步。

#### JS 引擎线程（处理同步代码）

主线程，运行JS代码的那个线程（不包括异步的那些代码)

执行栈，任务队列

####  定时器触发线程 (setTimeout)、http 异步线程、浏览器事件线程 (onclick)等等（处理异步代码）

setTimeout(func()) ->定时器触发线程

ajax -> http异步线程

dom.onclick -> 浏览器事件线程

1、执行**主线程**扔过来的异步代码，并执行代码

2、保存着回调函数，异步代码执行成功后，通知 **EventLoop轮询处理线程** 过来取相应的回调函数

#### EventLoop轮询处理线程（中介）

![img](https://upload-images.jianshu.io/upload_images/13253432-d8e6803e58425c10?imageMogr2/auto-orient/strip%7CimageView2/2/w/550/format/webp)



