## token和session id

![](https://upload-images.jianshu.io/upload_images/4951281-a1fe6c0e6c060bc1.png?imageMogr2/auto-orient/)

https://segmentfault.com/a/1190000017831088

https://www.jianshu.com/p/c33f5777c2eb

https://ninghao.net/blog/2834

<https://zhuanlan.zhihu.com/p/62336927>

**共同点：都是保存了用户身份信息，都有过期时间。**

通过解析token的计算时间换取了session的存储空间



### session + sessionID

浏览器第一次访问服务器时，服务器创建一个session，同时生成一个唯一的会话key，即sessionID。接着sessionID及session分别作为key和value保存到缓存中，也可以保存到数据库中，然后服务器把sessionID以cookie的形式发送给浏览器，浏览器下次访问服务器时直接携带上cookie中的sessionID，服务器再根据sessionID找到对应的session进行匹配。

1. 使用cookie时，sessionID会**自动由浏览器带上**

2. ，一般是随机字符串，要到服务器检索id的有效性

3. session是在服务器占据存储空间的，并且**有状态**的

   > 所谓有状态就是服务端会保存身份认证相关的数据。



**缺点**：

1. session方式由于会在服务器端维护session信息，单机还好说，如果是多机的话，服务器之间需要同步session信息，服务横向扩展不方便。
2. session数量随着登录用户的增多而增多，存储会增加很多。
3. session+cookie里面存sessionId的方式可能会有csrf攻击



### token

浏览器第一次访问服务器时，服务器根据传过来的唯一标识userId，通过一些算法，加一个密钥，生成一个token，接着通过base64编码将token返回给客户端。客户端将token保存起来，下次请求时需要带着token，服务器收到请求后，用相同的算法和密钥去验证token

token 也称作令牌，由uid+time+sign[+固定参数]。

- uid: 用户唯一身份标识
- time: 当前时间的时间戳
- sign: 签名, 使用 hash/encrypt 压缩成定长的十六进制字符串，以防止第三方恶意拼接
- 固定参数(可选): 将一些常用的固定参数加入到 token 中是为了避免重复查库



![](https://segmentfault.com/img/bVbh18N?w=688&h=454)

1. token需要代码添加到请求中，每次都是通过**解密来验证是否合法**。
2. token在服务器可以不需要存储空间，是**无状态**的；大多数通常在 local storage，但是也可以存储在 session storage 或者 cookie 中。
3. token可以避免csrf

> csrf主要是基于cookie，在post请求的瞬间，cookie会被浏览器自动添加到请求头中；但token不同，浏览器不会自动添加到headers里，攻击者也无法访问用户的token，所以提交的表单无法通过服务器过滤，也就无法形成攻击。

4. token通过cpu解密，很好的解决负载均衡多服务器的问题

5. JWT是一种实施token验证的方法



**缺点**：jwt的过期时间需要结合业务做设置，而且jwt一旦派发出去，后端无法强行使其作废







### JWT

主要由3部分组成：

```js
header 头部
{
  "alg": "HS256",
  "typ": "JWT"
}
payload 负载
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022,
  "exp": 1555341649998
}
signature 签名
```

header：描述加密算法和token的类型，类型一般都是JWT；

payload：用户的信息，也就是使用session登录时需要维护在服务器端session中的信息；

signature：对前两部分的签名，也可以理解为加密；实现需要一个密钥（secret），这个secret只有服务器才知道，然后使用header里面的算法按照如下方法来加密

最后的 jwt = base64url(header) + "." + base64url(payload) + "." + signature

jwt可以放在response中返回，也可以放在cookie中返回，这都是具体的返回方式，并不重要。客户端发起请求时，官方推荐放在HTTP header中