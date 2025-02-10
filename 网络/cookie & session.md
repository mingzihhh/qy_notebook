### cookie & session

![](https://segmentfault.com/img/bVbmYbQ?w=400&h=200)

#### cookie:客户端

- cookie只是实现session的其中一种方案。虽然是最常用的，但并不是唯一的方法。禁用cookie后还有其他方法存储，比如放在url中

- cookie 是浏览器访问服务器后，服务器传给浏览器的一段数据。 浏览器需要保存这段数据，不得轻易删除。此后每次浏览器访问该服务器，都必须带上这段数据。

- Cookie含有访问的方式、时间、用户名、密码等认证信息等，可以用https加密。

- 解决http是无状态协议的问题，即每次用户请求到达服务器时，HTTP服务器并不知道这个用户是谁、是否登录过等。

- Cookie的主要内容包括，名字，值，过期时间，路径和域。key /value 形式的。

- 过期时间可以设置，如不设，则浏览器关掉就消失了，存储在内存当中，否则就按照设置的时间才存储在硬盘上的，过期后自动清除，比方说开关机关闭再打开浏览器后他都还会存在，前者称之为Session cookie ,又叫transient cookie,后者称之Persistent cookie 又叫 permenent cookie。路径和域就是对应的域名，a网站的cookie自然不能给b用。

- 一般浏览器存储cookie 最大容量为4k，所以大量数据不要存到cookie。

- session文件是某个用户整个会话过程中数据。

- 使用场合：

  会话状态管理：保存登录、购物车等需要记录的信息

  个性化设置：保存用户的偏好，比如网页的字体大小、背景色等等

  浏览器行为追踪：记录和分析用户行为

- cookie可以在客户端删改，存放信息，**不安全**

  - 获得cookie: document.cookie

- cookie的安全性

  - 在cookie中设置了HttpOnly属性，那么通过js脚本将无法读取到cookie信息，这样能有效的防止XSS攻击
  
  - 用https传递，防止中间劫持
  
  - cookie数据加密
  
    

#### session:服务器端

- session是在无状态的HTTP协议下，服务端记录用户状态时用于标识具体用户的机制。

- 它是在服务端保存的用来跟踪用户的状态的类Hash Table结构，可以保存在**文件、数据库或者集群**中。

- **在HTTP协议之上，通过Cookie实现了持久的会话。这个会话便称为Session。**

- 创建session后，会把关联的session_id 通过setCookie 添加到http响应头部中。浏览器在加载页面时发现响应头部有 set-cookie字段，就把这个cookie 种到浏览器指定域名下。当下次刷新页面时，发送的请求会带上这条cookie， 服务端在接收到后根据这个session_id来识别用户。

- 一般这个值会有个时间限制，超时后毁掉这个值，默认30分钟。

- 当用户在应用程序的 Web页间跳转时，存储在 Session 对象中的变量不会丢失而是在整个用户会话中一直存在下去。

- Session的实现方式和Cookie有一定关系。建立一个连接就生成一个session id，打开几个页面就好几个了，这里就用到了Cookie，把session id存在Cookie中，每次访问的时候将Session id带过去就可以识别了.

  



#### cookie和session的区别

- 存储数据量方面：session能够存储任意JAVA对象，Cookie只能存储String类型的对象。
- 一个在客户端一个在服务端，因为Cookie在客户端，所以可以编辑伪造，不是十分安全。
- Session过多时会消耗服务器资源，大型网站会有专门的Session服务器，Cookie存在客户端没问题。
- 域的支持范围不一样，比方说[a.com](http://a.com)的cookie在[a.com](http://a.com)下都能用，而[www.a.com](http://www.a.com)的session在[api.a.com](http://api.a.com)下都不能用，解决这个问题的办法是JSONP或跨域资源共享。

#### session多服务器间共享

1. 服务器实现的 session 复制或 session 共享，如 webSphere或 JBOSS 在搭集群时配置实现 session 复制或 session 共享.致命缺点:不好扩展和移植。
2. 利用成熟技术做session复制，如12306使用的gemfire，如常见内存数据库redis或memorycache，虽较普适但依赖第三方.
3. 将 session维护在客户端，利用 cookie，但客户端存在风险数据不安全，且可以存放的数据量较小，所以将session 维护在客户端还要对 session 中的信息加密。
4. 第二种方案和第三种方案的合体，可用gemfire实现 session 复制共享，还可将session 维护在 redis中实现 session 共享，同时可将 session 维护在客户端的cookie 中，但前提是数据要加密。

这三种方式可迅速切换，而不影响应用正常执行。在实践中，首选 gemfire 或者 redis 作为 session 共享的载体，一旦 session 不稳定出现问题的时候，可以紧急切换 cookie 维护 session 作为备用，不影响应用提供服务

#### 单点登录中，cookie 被禁用了怎么办？（一点登陆，子网站其他系统不用再登陆）

- 单点登录的原理是后端生成一个 session ID，设置到 cookie，后面所有请求浏览器都会带上cookie，然后服务端从cookie获取 session ID，查询到用户信息。
- 所以，保持登录的关键不是cookie，而是通过cookie 保存和传输的 session ID，本质是能获取用户信息的数据。
- 除了cookie，还常用 HTTP 请求头来传输。但这个请求头浏览器不会像cookie一样自动携带，需手工处理



https://harttle.land/2015/08/10/cookie-session.html