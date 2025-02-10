## Axios

- 异常处理：提供默认的异常处理功能，判断错误状态码并处理，给出错误提示；

- 请求参数、header 统一处理；

- 返回值的统一处理；

- 增加统一定制化埋点；

- 请求默认参数设置；




####  创建：

- 可以直接使用 axios，也可以使用 axios.create 创建实例

- 工厂模式：如果 axios 是一个单例，一旦我们修改了 axios 的默认配置，会影响所有的请求。
- 支持自定义 config，默认有 method，timeout，headers

```
const instance = axios.create({
  withCredentials: true,
  paramsSerializer: (params) => {
    // 与后端的约定, 如果 GET 请求传入数组类型, 需要进行格式转化
    // list=[1,2,3] => list=1,2,3
    return queryString.stringify(params || {}, { arrayFormat: 'comma' });
  },
});
```



#### 拦截器：

- **请求拦截器先添加的后执行；响应拦截器先添加的先执行**
- nterceptorManager 存在一个属性 handlers 和三个方法 ：`use` 注册每个拦截器，返回注册号（数组长度 - 1）；`eject` 通过注册的 `id` 号来“删除”对应的拦截器，为了保证顺序，设置成 null ；`forEach` 遍历每一个非 null 拦截器

- 利用 unshift（插入请求拦截） 、push（插入响应拦截） 等数组队列、栈方法，实现了请求拦截、执行请求、响应拦截的流程设定
- 在运行时把所有的拦截器按照一个 promise 链的形式以此执行，真正的请求执行放在了一个 `then` 回调中，也就是微任务，它需要等当前的任务执行完毕，真正的 ajax 请求发送时机会有一定的延迟

```
instance.interceptors.request.use(
	(config) => {
	 	...
	 	// 设置 headers
	 	if (csrfToken) {
    	config.headers['X-CSRFToken'] = csrfToken;
    }
	 	return config;
	},
  async (error) => { throw error }，
  // 默认拦截器是异步任务，可以设置成同步
  { synchronous: true })

// 上报数据，错误处理
instance.interceptors.response.use((config) => {})
```



#### 取消相同请求

https://mp.weixin.qq.com/s/By-iXlONjSZLKFG2Xd7rpg

- AbortController：重复请求则可以通过`config`生成 key，如果 key 相同则判断是重复请求。

```
const controller = new AbortController();

axios.get('/foo/bar', {
   signal: controller.signal
}).then(function(response) {
   //...
});
// 取消请求
controller.abort()


// 取消相同请求
const myDataSymbol = Symbol('my data')

function getMyData(params) {
  return axios.get('/path/to/my/api', {
    params,
    singleton: myDataSymbol,
  });
}

instance.interceptors.request.use((config) => {
  if (config.singleton) {
    const oldSource = SingletonCollection.get(config.singleton);
    if (oldSource) {
      oldSource.abort();
    }
    const controller = new AbortController();
    config.signal = controller.signal;
    SingletonCollection.set(config.singleton, controller);
  }

  return config;
});
```

- CancelToken：已在 v0.22.0 中弃用

```
const { CancelToken } = axios;
const SingletonCollection: Map<symbol, CancelTokenSource> = new Map();

instance.interceptors.request.use((config) => {
  if (config.singleton) {
    const oldSource = SingletonCollection.get(config.singleton);
    if (oldSource) {
      oldSource.cancel();
    }
    const source = CancelToken.source();
    config.cancelToken = source.token;
    SingletonCollection.set(config.singleton, source);
  }

  return config;
});
```



#### 接口缓存

- Axios支持自定义适配器，我们可以基于自定义适配器的能力来实现接口数据缓存功能

https://juejin.cn/post/6974902702400602148



#### 错误接口重发

https://juejin.cn/post/6973812686584807432





其他：

- 基于 TS 的类型提示与类型检测；

- 基于类封装，可以创建多个实例，根据不同场景创建不同实例，满足不同场景下的需求；

- 请求与响应拦截器，支持外部传入，支持在全量或部分指定请求上生效；

- 每个实例与每个接口都可以灵活配置请求头、超时时间等；

- 取消请求（可根据 url 取消单个请求，也可取消全部请求）；

- 请求参数、请求体过滤指定空值（undefined等）；

- 内部对错误进行处理，避免错误未捕获；

- 接口缓存：仅针对 get 请求，可开启接口缓存功能；

- 文件下载；

- 接口轮询；

- 请求错误重发；

- 处理请求 path 前、后缀；

- 控制 response 是否直接返回 data；

- 响应码对应处理逻辑；

- slardar 错误数据监控；

- 传入 mera 实例，完成自动上报；

  

| **代码复用和维护性增强** | 可以将请求逻辑和错误处理等代码集中到一个地方，提高代码的可维护性。团队成员无需重复编写相同的请求代码，可以直接使用请求库提供的接口，减少重复工作，并且当需要进行修改或更新时，只需在请求库中进行修改，而不需要在各处进行手动更改。 |
| ------------------------ | ------------------------------------------------------------ |
| **规范化和一致性**       | 可以确保团队成员在进行网络请求时遵循相同的规范和约定。这有助于减少代码风格和实现方式的差异性，使代码更加一致，易于阅读和维护。统一的请求库还可以实现与后端API的一致性，处理请求和响应格式的标准化，提高系统的稳定性和可靠性。 |
| **错误处理和异常管理**   | 可以集中处理网络请求的错误和异常情况。请求库可以提供统一的错误处理机制，例如处理网络连接错误、超时、服务器错误等。这样可以减少代码中的冗余错误处理代码，并且可以更好地管理和记录请求过程中发生的异常，便于排查和修复问题。 |
| **性能优化**             | 请求库可以针对性能进行优化，例如实现请求的批量发送、请求的合并和缓存等功能，以减少网络请求次数和提高请求的效率。这可以减轻前端和后端的负载，提高系统的性能和响应速度。 |
| **接口的抽象和封装**     | 可以将底层的请求细节进行抽象和封装，提供更高层次的接口给其他模块使用。这样可以降低其他模块对请求库的依赖程度，减少对底层实现的暴露，提高代码的灵活性和可扩展性。 |
| **安全性增强**           | 封装统一的请求库可以集中处理安全相关的问题，例如请求的身份验证、防止跨站脚本攻击（XSS）和跨站请求伪造（CSRF）等。通过在请求库中实现这些安全机制，可以确保请求的安全性，并减少安全漏洞的风险。 |