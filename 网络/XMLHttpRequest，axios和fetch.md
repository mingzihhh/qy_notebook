## XMLHttpRequest，axios和fetch

### XMLHttpRequest对象

现代浏览器，最开始与服务器交换数据，都是通过XMLHttpRequest对象。它可以使用JSON、XML、HTML和text文本等格式发送和接收数据。

**优点**

1. 不重新加载页面的情况下更新网页
2. 在页面已加载后从服务器请求/接收数据
3. 在后台向服务器发送数据。

**缺点**

1. 使用起来也比较繁琐，需要设置很多值。
2. 早期的IE浏览器有自己的实现，这样需要写兼容代码。



### axios

`Axios`是一个基于`promise`的`HTTP`库，可以用在浏览器和 `node.js` 中。它本质也是对原生`XMLHttpRequest`的封装，只不过它是Promise的实现版本，符合最新的ES规范。

```
axios({
    method: 'post',
    url: '/user/12345',
    data: {
      firstName: 'liu',
      lastName: 'weiqin'
    }
  })
  .then(res => console.log(res))
  .catch(err => console.log(err))
```

**优点**：

1. 从浏览器中创建`XMLHttpRequests`

2. 从 `node.js` 创建 `http` 请求

3. 支持 `Promise` API

4. 拦截请求和响应(axiosInstance.interceptors.request.use/axiosInstance.interceptors.response.use)

5. 转换请求数据和响应数据

6. 取消请求

7. 自动转换 `JSON` 数据

8. 客户端支持防御 `XSRF`

   ```
   防止CSRF:就是让你的每个请求都带一个从cookie中拿到的key, 根据浏览器同源策略，假冒的网站是拿不到你cookie中得key的，这样，后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。
   ```

   

**缺点**：

1. 只持现代浏览器.



### fetch

`Fetch API`提供了一个 `JavaScript` 接口，用于访问和操作`HTTP`管道的部分，例如请求和响应。它还提供了一个全局`fetch()`方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

`fetch`是低层次的API，代替`XHR`，可以轻松处理各种格式，非文本化格式。可以很容易的被其他技术使用，例如`Service Workers`。但是想要很好的使用`fetch`，需要做一些封装处理。

```
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

**优势：跨域的处理**
在配置中，添加`mode： 'no-cors'`就可以跨域了

```
fetch('/users.json', {
    method: 'post', 
    mode: 'no-cors',
    data: {}
}).then(function() { /* handle response */ });
```



**优点：**

1. 语法简洁，更加语义化

2. 基于标准 Promise 实现，支持 async/await

3. 同构方便，使用 [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch)

4. 更加底层，提供的API丰富（request, response）

5. 脱离了XHR，是ES规范里新的实现方式

   

一些问题：

1. fetch只对网络请求报错，对400，500都当做成功的请求，服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
2. fetch默认不会带cookie，需要添加配置项： fetch(url, {credentials: 'include'})
3. fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
4. fetch没有办法原生监测请求的进度，而XHR可以