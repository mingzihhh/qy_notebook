## WebSocket协议

http://www.ruanyifeng.com/blog/2017/05/websocket.html

https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001472780997905c8f293615c5a42eab058b6dc29936a5c000

**持久化，基于http，服务端可以主动push**

**http协议缺陷**：

请求-响应协议，单项请求，通信只能由客户端发起，不能由服务端主动推送，聊天室这种功能无法实现

WebSocket是HTML5新增的协议，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

WebSocket连接必须由浏览器发起，利用http协议建立连接，因为请求协议是一个标准的HTTP Upgrade请求，在请求头申请将连接转换为WebSocket协议

其他特点包括：

（1）建立在 TCP 协议之上，服务器端的实现比较容易。

（2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

（3）数据格式比较轻量，性能开销小，通信高效。

（4）可以发送文本，也可以发送二进制数据。

（5）没有同源限制，客户端可以与任意服务器通信。

（6）协议标识符是`ws`（如果加密，则为`wss`），服务器网址就是 URL。

```

var ws = new WebSocket("wss://echo.websocket.org");
//新建 WebSocket 实例，执行上面语句之后，客户端就会与服务器进行连接。

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};   
```

