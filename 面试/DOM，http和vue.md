# DOM，http和vue

1. 必考：事件委托

   判断子元素：会出错

   ![1552827958123](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552827958123.png)

   循环判断父元素：

   ![1552828056529](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552828056529.png)

2. 曾考：用 mouse 事件写一个可拖曳的 div

   ![1552833004800](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552833004800.png)

```
.circle {
  display: block;
  background: black;
  border-radius: 50%;
  height: 300px;
  width: 300px;
  margin: 0;
  background: radial-gradient(circle at 100px 100px, #5cabff, #000);
  //加渐变，圆变球
}
```

# HTTP

1. 必考：HTTP 状态码知道哪些？分别什么意思？

   100

   200：OK

   201，202，204，

   301，302，303，304

   400： bad request

   401，403，404，405，414，422

   500，502

   2：没问题，成功

   3：进一步操作

   4：客户端出错

   5：服务器出错

   

2. 必考：HTTP 缓存有哪几种？

   Etag：MD5，有请求

   Expire：日期，注意本地时间的影响，不靠谱

   Cache-Control：max-age = 500，无请求

   pwa：http顺序

   

3. 必考：GET 和 POST 的区别

   

4. Cookie V.S. LocalStorage V.S. SessionStorage V.S. Session

   cookie:服务器发给浏览器的一段数据，浏览器每次访问服务器时都要带上这段数据

   session:服务器与浏览器一段时间内的会话

   区别：session在服务器上，cookie在浏览器上，session是基于cookie的，把session id放在cookie里就能实现session

   区别：cookie 4k,localStorage 5M；cookie存用户信息，LS存不重要的信息；cookie发送给浏览器，

   LS不发

   区别：LS不会过期，SS会在session结束的时候过期

# 框架 Vue

1. 必考：watch 和 computed 区别是什么？

   计算属性，监听数据；缓存

2. 必考：Vue 有哪些生命周期钩子函数？分别有什么用？

3. 必考：Vue 如何实现组件间通信？

   - 父子组件    $emit('xxx',data) $on('xxx',function())
   - 爷孙组件/兄弟 var  eventBus  = new Vue()
   - Vuex

4. 必考：Vue 数据响应式怎么做到的？

   （双向绑定：v-model例子，原理：源代码）看文档

5. 必考：Vue.set 是做什么用的？

   同上，文档

6. Vuex 你怎么用的？

   vuex是为vue开发的状态管理工具，5个核心概念，分别介绍

7. VueRouter 你怎么用的？

   vuerouter是vue官方的路由管理器，动态路由匹配，重定向和别名，模式，导航守卫，懒加载（import）

8. 路由守卫是什么？

   

   

   