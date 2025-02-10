### 使用node.js建立静态服务器 

#### 静态服务器

不支持动态页面和数据库的服务器，一般存放**不会被服务器的动态运行所改变或者生成的文件**。

- 分析url，针对不同的情况做不同的事。 如果这个url对应的是一个文件，那么服务器就会返回这个文件。 如果这个url对应的是一个文件夹，那么服务器会返回这个文件夹下包含的所有子文件/子文件夹的列表
- 针对这些特殊情况进行一些不同的返回和提示。我们所拿到的url可能是错误的，它所对应的文件或则文件夹或许根本不存在， 又或则有些文件和文件夹是被系统保护起来的是隐藏的，我们并不想让客户端知道。 
- 在返回一个文件前和客户端进行一些协商。 我们需要知道客户端能够接受的语言类型、编码方式等等以便针对不同浏览器进行不同的返回处理。 我们需要告诉客户端一些关于返回文件的额外信息，以便客户端能更好的接收数据： 文件是否需要缓存，该怎样缓存？ 文件是否进行了压缩处理，该以怎样的方式解压？ 等等...

#### http协议

**HTTP 协议** (Hyper Text Transfer Protocol 超文本传输协议）就是 服务器 和 客户端 之间的**通信规则**.

使用 HTTP 协议传输的信息叫做 **HTTP 报文**. 请求端（客户端）的 HTTP 报文叫做**请求报文**，响应端（服务器端）的叫做**响应报文**。 HTTP 报文可分为 **报文首部** 和 **报文主体** 两块, 中间由一个空行分开.

HTTP 协议使用 **URI 定位互联网上的资源**。

#### URL说明

```
http://user:pass@host.com:8080/p/a/t/h?query=string#hash
```

解析后对象字段如下：

- `href`: 解析前的完整原始 URL，协议名和主机名已转为小写

  例如: `'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'`

- `protocol`: 请求协议，小写

  例如: `'http:'`

- `slashes`: 协议的“：”号后是否有“/”

  例如: true or false

- `host`: URL主机名，包括端口信息，小写

  例如: `'host.com:8080'`

- `auth`: URL中的认证信息

  例如: `'user:pass'`

- `hostname`: 主机名，小写

  例如: `'host.com'`

- `port`: 主机的端口号

  例如: `'8080'`

- `pathname`: URL中路径

  例如: `'/p/a/t/h'`

- `search`: 查询对象，即：queryString，包括之前的问号“?”

  例如: `'?query=string'`

- `path`: `pathname` 和 `search`的合集

  例如: `'/p/a/t/h?query=string'`

- `query`: 查询字符串中的参数部分（问号后面部分字符串），或者使用 querystring.parse() 解析后返回的对象

  例如: `'query=string'` or `{'query':'string'}`

- `hash`: 锚点部分（即：“#”及其后的部分）

  例如: `'#hash'`

#### 主要功能和步骤

1. 引用 `http` 模块创建一个 HTTP 服务器.

2. 客户端通过 URL 来指明想要的服务器上资源，引用 `url` 模块处理和解析 URL，`path` 模块处理文件与目录的路径

   - `url.parse()` ：URL 字符串可以被解析为一个 URL 对象，其属性对应于字符串的各组成部分。其中`url.parse()`方法的第二个参数是`true`，可以将所有的查询变为对象形式。
   - 从解析后的 URL 对象 `urlObj` 里取得请求 URL 中的路径名(pathname)。
   - `path.join()`：把目标文件所在目录的目录名和请求 URL 中的路径名合并起来获取资源.

3. 通过`fs`模块读取静态资源

4. 设置routes对象存储动态路由,根据 URL 中的 pathname 来匹配对应路由

5. 增加面对post请求方式mock数据

   


request：请求底层封装成的对象，存放用户请求的信息（ip，域名，浏览器信息...

response：返回给用户的信息

.listen（）：启动服务器，监听端口

localhost：通过域名搜索ip地址，对应127.0.0.1，本机ip地址

设置响应头：res.setHeader()

content-type：告诉浏览器响应信息的解析方式和解码方式，比html中的meta优先级高

`writeHead`相较于`setHead`能同时设置多个头，并且连状态码一起设置。

它和`setHeader`最大的不同在于:`writeHeader`一旦调用会立刻发送。而`setHeader`设置的头是在调用write方法之后才会发送。所以`writeHead`设置完后不能再调用`res.setHeader`，因为调用`writeHead`会直接把头发送出去

另外需要注意的一点是头必须在write之前设置。



1、在node.js里面通过require加载一些需要用到的模块，比如：

```
var http = require('http')  //http模块创建服务器
var path = require('path')  //path模块根据系统内部自动识别url类型  
var fs = require('fs')  //fs模块用于读写文件 
var url = require('url')  //url模块自动解析用户的url得到一些有用的信息 
```



2、理清楚请求到达浏览器后，进入 createServer() 函数内部，函数内部是如何处理请求的，也就是清楚函数内部的逻辑关系。
 （1）函数内部解析用户请求的url，如果url中没有扩展名，则添加默认的访问页面
 （2）根据url中的信息获取资源文件的相对路径和绝对路径
 （3）根据资源文件的绝对路径同步或异步读取资源文件内容，如果是异步还需考虑文件名存在和不存在的情况
 （4）文件读取成功后，向浏览器返回文件内容并呈现到用户页面



**注意：一定要将.js文件建在整个文件夹的根目录下！**
补充：dirname与filename的区别
使用dirname变量可以 获取当前文件所在目录的完整目录名
使用filename可以获取当前文件的完整文件名****



https://www.jianshu.com/p/ab0a84040821

https://juejin.im/post/5bd7915ee51d457aba5e98f5

https://juejin.im/post/5bd792576fb9a05d0261402b

https://juejin.im/post/5a9660fe6fb9a0634b4da9ae

