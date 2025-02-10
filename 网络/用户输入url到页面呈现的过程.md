## 用户输入url到页面呈现的过程

1. 用户输入url
2. 浏览器查找域名的IP地址 [域名解析(DNS解析)](https://my729.github.io/blog/interview/%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3.html#%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90)
3. 找到IP地址后，建立[TCP三次握手](http://my729.github.io/blog/internetwork/TCP%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B.html) ,与目标服务器建立连接
4. 握手成功后，通过规定的协议（http），浏览器向目标主机发送http请求，请求数据包
5. 服务器处理收到的请求，将数据返回至浏览器
6. 浏览器收到HTTP响应报文
7. 四次挥手关闭连接 浏览器解析文档
8. 读取页面内容，[浏览器渲染](https://my729.github.io/blog/interview/%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3.html#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%A6%82%E4%BD%95%E6%B8%B2%E6%9F%93%E9%A1%B5%E9%9D%A2)，解析html源码
9. 生成Dom树、解析css样式、js交互

https://juejin.cn/post/7256582988490194981