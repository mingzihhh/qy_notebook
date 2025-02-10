`postMessage` 和 `MessageChannel` 都是 Web API，用于不同上下文之间的通信（如窗口、iframe、Web Worker 等）。它们的主要区别在于通信方式、适用场景和灵活性。

#### window.postMessage

- **单向通信**：只能发送消息，从一个上下文发送到另一个上下文。

- **目标限制**：需要明确指定消息发送的目标（如目标窗口或 Web Worker）。

- **适用场景**：多用于页面和 iframe、不同窗口、主线程与 Web Worker 之间的简单通信。

- window.postMessage 中的指的是你想发送跨域消息的那个窗口(你需要通信的目标窗口)，而不是自身窗口的window
  - 父页面中：父页面向子页面发送跨域信息，window就是在父页面中嵌入的iframe指向的子页面的window，即：iFrame.contentWindow
  - 子页面中：子页面想父页面发送跨域信息，window就是父页面的window，在这里因为子页面是嵌入到父页面中的，对于子页面来讲，window就是top或者parent


```
// 主线程发动给 worker
const worker = new Worker('worker.js');
worker.postMessage('Hello from main thread');

// worker 接收消息并响应
onmessage = (event) => {
  console.log('Message from main thread:', event.data);
  postMessage('Hello from Worker');
};
```



### MessageChannel

- **双向通信**：生成两个端口 `port1` 和 `port2`，每个端口可以监听和发送消息。

- **无目标限制**：端口可通过 `postMessage` 传递给任何上下文（如 Worker、iframe），实现双向通信。

- **适用场景**：适合复杂的双向或多端通信场景，如多个 Worker、iframe或窗口之间的交互。

```
const worker = new Worker('worker.js');
const channel = new MessageChannel();

// 向 worker 传递 port2
worker.postMessage({ port: channel.port2 }, [channel.port2]);

// 监听来自 worker 的消息
channel.port1.onmessage = (event) => {
  console.log('Message from Worker:', event.data);
};

// 使用 port1 向 Worker 发送消息
channel.port1.postMessage('Hello from main thread');


// worker 中接收端口并监听
onmessage = (event) => {
  const port = event.data.port;

  // 监听来自主线程的消息
  port.onmessage = (event) => {
    console.log('Message from main thread:', event.data);
    port.postMessage('Hello from Worker');
  };
};
```

