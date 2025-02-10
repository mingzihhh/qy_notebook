## get和post的区别

GET和POST的实质上是没有区别的，只是报文格式不同，我们常说的是区别是指在浏览器实现上的区别

#### 浏览器实现的区别：

| GET                         | POST                       |
| --------------------------- | -------------------------- |
| 从服务器获取资源            | 提交资源到服务器           |
| 在url上传参，长度受限制     | 在body里传参，长度不受限制 |
| 能被缓存                    | 不能被缓存                 |
| 安全性较差，因为url可见参数 | 比较安全                   |

注意

- GET 方法的长度限制大多是浏览器和服务器的原因，HTTP 协议没有 Body 和 URL 的长度限制，
- 从传输的角度来说，POST 和 GET 都是不安全的，因为 HTTP在网络上是明文传输的，要想安全传输，可以加密使用HTTPS

#### 报文上的区别：

- 不带参数时，最大区别就是第一行的方法名不同

  ```
  GET /uri HTTP/1.1
  POST /uri HTTP/1.1
  ```

- 带参数时报文的区别

  ```
  GET /index.html?name=fanmy&age=24 HTTP/1.1
  Host: localhost
  
  POST /index.html HTTP/1.1
  Host: localhost
  Content-Type: application/x-www-form-urlencoded
  
  name=fanmy&age=24
  ```

  