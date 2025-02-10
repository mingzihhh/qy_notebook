**流就是分段的传输内容，比如从服务端像浏览器返回响应数据的流，读取文件的流等。**

**流和流之间可以通过管道 pipe 连接，上个流的输出作为下个流的输入。**



在 node 里，流一共有 4 种：可读流 Readable、可写流 Writable、双工流 Duplex、转换流 Transform，文件读写、网络通信等都是基于流。

Readable：实现 _read 方法，通过 push 传入内容，比如 fs.createReadStream，http 的 request 

Writable：实现 _write 方法，通过 next 消费内容，比如 fs.createWriteStream，http 的 response

Duplex：实现 _read、_write，可读可写，比如 net 的 Socket 

Transform：实现 _transform，对写入的内容做转换再传出去，继承自 Duplex，比如 zlib.createGzip（http 压缩传输）

