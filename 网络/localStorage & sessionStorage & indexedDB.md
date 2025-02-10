#### localStorage

- HTML5本地存储web storage特性的API之一，用于将**大量数据（最大5M）**保存在浏览器中，保存后数据永远存在不会失效过期，除非手动清除。

- `localStorage` 的数据存储是基于源（协议/域名/端口）的，所以在同一个浏览器中，同一个源下的所有标签页（tab）、窗口（window）和 iframe 共享同一个 `localStorage` 空间；如果在一个标签页中修改了 `localStorage`，其他标签页的 `localStorage` 也会被同步更新（可以通过监听 `storage` 事件来响应这些变化）。

- 不参与网络传输。一般用于性能优化，可以保存图片、js、css、html 模板、大量数据。

- ```
  localStorage.getItem()
  localStorage.setItem()
  localStorage.removeItem()
  localStorage.clear()
  ```


- 仅支持字符串数据，无法存储复杂对象。

- 当一个js执行流程访问 `localStorage` 时，它将**同步**地等待数据读取或写入完成，该过程中js执行线程会阻塞。这意味着如果涉及较多数据的读写操作时，可能对性能产生负面影响，特别是在主线程上，因为它会阻塞UI的更新和其他js的执行。

  

#### sessionStorage

- 类似  `localStorage`  ，而当页面会话结束——也就是说，当页面被关闭时，存储在 `sessionStorage` 的数据会被清除

- 页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。
- **在新标签或窗口打开一个页面时会复制顶级浏览会话的上下文作为新会话的上下文，这点和 session cookie 的运行方式不同。**
  - **sessionStorage** 不能在多个窗口或标签页之间共享数据，但是在当前页面通过 **window.open** 或链接打开新页面时(不能是新窗口)，新页面会复制前一页的 **sessionStorage**
- 打开多个相同的 URL 的 Tabs 页面，会创建各自的 `sessionStorage`。
- 关闭对应浏览器标签或窗口，会清除对应的 `sessionStorage`。



#### IndexedDB

- **大容量存储**：与 `localStorage` 的 5-10MB 限制相比，`IndexedDB` 可以存储几乎无限量的数据（取决于浏览器和设备的限制）
- **存储对象**：`IndexedDB` 支持存储 JavaScript 对象、文件、图片等，而不仅仅是字符串数据。你可以将复杂的数据结构（如对象、数组）直接存储。
- **异步操作**：大部分操作是异步的（通过回调或 `Promise`），防止阻塞主线程，提高应用的性能。
- **离线支持**：`IndexedDB` 是离线应用程序的重要技术组件，适合需要在离线状态下大量数据读写的应用，比如邮件客户端、笔记应用等。
- **复杂查询和索引**：`IndexedDB` 提供索引和事务功能，适合处理复杂查询操作。