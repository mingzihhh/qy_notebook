# SSO

SSO全称 **Single sign-on**，即**单点登录**。对于许多**相互关联**，但是又是**各自独立**的软件系统，我们往往希望**一次登录就可以实现登录所有相互关联其他系统**，而不用在切换过程中反复登录。

https://juejin.cn/post/6844903509272297480

### **单点登录的行业标准协议**

**CAS（ Central Authentication Service ）** 

 Yale 大学发起的一个企业级的、开源的项目，旨在为 Web 应用系统提供一种可靠的单点登录解决方法。

基于在**客户端**Web浏览器、Web**应用**和**CAS服务器**之间的票据验证。当客户端访问访问应用程序，请求身份验证时，应用程序重定向到CAS。CAS验证客户端是否被授权，如果身份验证成功，将客户端自动返回到应用程序，并向应用传递身份验证票（Service ticket）。然后，应用程序通过安全连接连接CAS，并提供自己的服务标识和验证票。

主要流程：

1. 用户在浏览器内打开 Web 应用主页
2. Web 应用后端发现用户未登录，返回 resp 302，Location 地址为 https://cas.server.com/cas/login?service=urlencode<<https://app.example.com>>
3. 浏览器处理 302 返回，跳转至 Location 地址上（cas 服务器）
4. 用户在 cas 服务器上完成登录
5. 登录完成后 cas 服务器返回 302，Location 地址为 service 参数对应的地址上，并携带 ticket query 参数
6. 浏览器处理 302 返回，跳转至 Location 地址上（应用主页）
7. Web 应用处理请求，获取链接中的 ticket 参数，构造验证请求至 cas 服务器验证 ticket 有效性，https://cas.server.com/cas/serviceValidate?ticket=xxx&service=xxx
8. cas 服务器验证完 ticket，返回用户信息
9. Web 应用登录成功，继续处理用户请求

**OAuth2**

一种**授权标准**，允许用户在一个站点向其他站点授予对其资源的有限访问权限，而无需获得其凭证（通常是账号密码）。比较直接的例子就是第三方App使用微信或QQ来登录，这些授权登录采用的就是OAuth。

https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html



### CAS 对比 OAUTH

1. **设计初衷**

- **CAS** 专门为中心化鉴权（**authentication**）而生，即 SSO，并且 3.5 版本后也支持通过 OAuth 协议进行登录鉴权

- **OAuth** 是用来处理授权（**authorization**）而生，实现 SSO 并不是它的初衷，它只关注如何让第三方通过让用户无需登录的方式获得私有资源
  - 只不过它也定义了完整的鉴权流程，如果你把用户信息当做私有资源，你也可以使用它定义的规范来实现 SSO，但相比 CAS，它是没有 **SLO**（single logout）功能的。例如 github、微信的第三方登录都是基于 OAuth 实现

2. **安全性**

- CAS
  - **Service** **ticket** **只能使用一次**，并且有效期越短越好，而 OAuth 的 access_token 是有有效期的，有效期内可以使用多次
  - ticket 生成需要足够随机，如果被攻击者猜出规律，则可以计算出下一个 ticket 值
- OAuth
  - 通过 state 参数(放在query中)可以有效防止 csrf 攻击
  - OAuth更加复杂，多了一步通过 **code 换取 access_token，code只能使用一次，但access_token可以多次获得用户信息**
  - OAuth协议约定了 **refresh_token/刷新访问令牌** 机制，因此可以通过 refresh_token 在后端更新 token，无需再走一遍完整流程，CAS 协议则没有提供这种方案，如果**CAS的登录状态过期了就必须重新登录**
  - 对于 web 应用，access_token 通过后端方式接口获取和接口使用，secret 不会展示在前端，暴露可能性低
  - 对于前端应用，可以通过 PKCE extension 有效防止授权码拦截攻击
  - 提供了 scope 机制来限制获取到的资源范围

3. **资源存储**

- CAS：Client端(应用系统)是资源存储端，用户是资源消费者(应用使用者)。
- OAuth2：Client端(第三方服务)是资源消费端，通过用户授权，允许用户不提供自己账号密码的情况下，使Client端有权访问用户资源(如个人信息、通讯录等)。
- 简单来说：
  - 需要统一的账号密码进行身份认证，用CAS
  - 需要授权第三方服务使用我方资源，使用OAuth。




各类SSO服务往往都不会进行用户登录状态的维护，而是只负责用户登录状态的扩散（任何接入服务的应用网站都可以来我这里请求用户信息），登录状态的维护需要接入方自行处理（cookie-session存储/token）。

例如：server通过token获取到了用户的授权信息（user_id之类的），如果业务方不把这个“授权信息（登录态）” 维护起来（session/JWT），那么每次访问都需要再走到SSO Server，走一次完整流程。那么就会导致以下两个问题：

1、对SSO server而言流量、请求会被放大

2、对用户而言流程变长、响应时间变慢。



### 登录态维护

https://juejin.cn/post/6844904034181070861#heading-17

##### Token

- **访问资源接口（API）时所需要的资源凭证**
- **简单 token 的组成：** uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign（签名，token 的前几位以哈希算法压缩成的一定长度的十六进制字符串）
- **特点**：
  - **服务端无状态化、可扩展性好**
  - **支持移动端设备**
  - 安全
  - 支持跨程序调用
- **token 的身份验证流程：**

1. 客户端使用用户名跟密码请求登录

2. 服务端收到请求，去验证用户名与密码

3. 验证成功后，服务端会签发一个 token 并把这个 token 发送给客户端

4. 客户端收到 token 以后，会把它存储起来，比如放在 cookie 里或者 localStorage 里

5. 客户端每次向服务端请求资源的时候需要带着服务端签发的 token

6. 服务端收到请求，然后去验证客户端请求里面带着的 token ，如果验证成功，就向客户端返回请求的数据



##### JWT（Json Web Token）

https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html

JWT是一个开放的 JSON 格式 token 存储标准。它定义了一种安全、紧凑的方式来保存数据，**通过签名的方式来校验 token 的合法性**，主要支持的签名算法如 HMAC、RSA、ECDSA。

通常使用它来保存登录信息，相比传统的 session 方案，它的优点在于服务端无需维护登录态，不再需要依赖第三方存储（如 redis、memcached），所以说 **JWT 是无状态的**。

JWT 的最大缺点是，由于**服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限**。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。相比传统的 session 方案，只需要将 session 清除即可。你可能会说，可以直接将这个 token 删除就算退出登录了。但实际上这只是一种假注销，若该用户再次拿到相同的 token 还是会被认为是登录的。



##### Session

Session 代表着服务器和客户端一次会话的过程。Session 对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。

**常见误区**：

Session 不是关闭浏览器就消失了。**对 Session 来说，除非程序通知服务器删除一个 Session，否则服务器会在Session失效前一直保留。**大多数情况下浏览器是不会在关闭网页之前通知服务器的，所以服务器根本不知道浏览器已经关闭。之所以会有这种错觉，是大部分 session 机制都使用会话 cookie 来保存 session id，而关闭浏览器后这个 session id 就消失了，再次连接服务器时也就无法找到原来的 session。如果服务器设置的 cookie 被保存在硬盘上，或者使用某种手段改写浏览器发出的 HTTP 请求头，把原来的 session id 发送给服务器，则再次打开浏览器仍然能够打开原来的 session。



**session和cookie关系**：**使用 cookie 存放 session id**

服务器第一次接收到请求时，开辟了一块 Session 空间（创建了Session对象），同时生成一个 session id ，并通过响应头的`Set-Cookie：JSESSIONID=XXXXXXX` 命令，向客户端发送要求设置 Cookie 的响应； 客户端收到响应后，在本机客户端设置了一个**`JSESSIONID=XXXXXXX`的 Cookie 信息**，该 Cookie 的过期时间为浏览器会话结束。接下来客户端每次向同一个网站发送请求时，请求头都会带上该 Cookie信息（包含 sessionId ）， 然后服务器通过读取请求头中的 Cookie 信息，获取名称为 JSESSIONID 的值，得到此次请求的 session id。



### **session和cookie区别**

1. **安全性：** 由于Session 是存储在服务器端的，Cookie 是存储在客户端的，所以Cookie被盗用的可能性相较于Session会更高一些。

2. **存取值的类型不同**：Cookie 只支持存字符串数据，想要设置其他类型的数据，需要将其转换成字符串，Session 可以存任意数据类型。

3. **存储大小不同：** 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie，但是当访问量过多，会占用过多的服务器资源。

   

### Token 和 Session 的区别

- Session 是一种**记录服务器和客户端会话状态的机制，使服务端有状态化，可以记录会话信息**。而 Token 是**令牌**，**访问资源接口（API）时所需要的资源凭证**。Token **使服务端无状态化，不会存储会话信息。**

- Session 和 Token 并不矛盾，作为身份认证 Token 安全性比 Session 好，因为每一个请求都有签名还能防止监听以及重放攻击，而 Session 就必须依赖链路层来保障通讯安全了。**如果你需要实现有状态的会话，仍然可以增加 Session 来在服务器端保存一些状态。**

- 所谓 Session 认证只是简单的把 User 信息存储到 Session 里，因为 SessionID 的不可预测性，暂且认为是安全的。而 Token ，如果指的是 OAuth Token 或类似的机制的话，提供的是 认证 和 授权 ，认证是针对用户，授权是针对 App 。其目的是让某 App 有权利访问某用户的信息。这里的 Token 是唯一的。不可以转移到其它 App上，也不可以转到其它用户上。Session 只提供一种简单的认证，即只要有此 SessionID ，即认为有此 User 的全部权利。是需要严格保密的，这个数据应该只保存在站方，不应该共享给其它网站或者第三方 App。所以简单来说：**如果你的用户数据可能需要和第三方共享，或者允许第三方调用 API 接口，用 Token 。如果永远只是自己的网站，自己的 App，用什么就无所谓了。**

  

### Token 和 JWT 的区别  

**相同：**

- 都是访问资源的令牌
- 都可以记录用户的信息
- 都是使服务端无状态化
- 都是只有验证成功后，客户端才能访问服务端上受保护的资源

**区别：**

- Token：服务端验证客户端发送过来的 Token 时，还需要查询数据库获取用户信息，然后验证 Token 是否有效。
- JWT： 将 Token 和 Payload 加密后存储于客户端，服务端只需要使用密钥解密进行校验（校验也是 JWT 自己实现的）即可，不需要查询或者减少查询数据库，因为 JWT 自包含了用户信息和加密的数据。

