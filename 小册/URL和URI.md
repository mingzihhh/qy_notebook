## 浏览器基础知识点

#### 事件机制

**事件的触发过程是怎么样的？什么是事件代理？**

事件触发有三个阶段：

- `window` 往事件触发处传播，遇到注册的捕获事件会触发

- 传播到事件触发处时触发注册的事件

- 从事件触发处往 `window` 传播，遇到注册的冒泡事件会触发

  事件触发一般来说会按照上面的顺序进行，但是也有特例，**如果给一个 body 中的子节点同时注册冒泡和捕获事件，事件触发会按照注册的顺序执行。**