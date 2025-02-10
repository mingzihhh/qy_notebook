### ts 类型扩展

```
declare module 'axios' {
  export interface AxiosRequestConfig {
    unwrap?: boolean | 'deep' | 'data' | 'response'; // 增加自定义字段
  }
}
```



### 处理 axios 返回

```
axios.post('v/api/data', data, {unwrap: true})
或者 
axios.interceptors.request.use(withDefaults({unwrap: true})) // withDefaults 总是应该是最后一个请求拦截器
```

1. 读取 response.config.unwrap，为 true 的时候需要解包数据 => data = response.data.data
2. 将错误处理收敛至单一的错误处理中：判断 response.code === 0，不为 0 的时候 throw new ServerError



### 统一接口响应 & 错误处理

用来处理 axios 请求返回值的拦截器

- 将真正的数据从 Axios 的响应值、以及服务端的接口格式中解析出来
- 将错误处理收敛至单一的错误处理中
  - 通过错误对象的 `name` 属性判断错误是否是由后端数据格式产生的，或者通过 `code` 属性获取对应的错误码
  - 根据错误类型进行错误上报 & 错误处理（弹窗 / ignore..）

```
export class ServerError<T = unknown, D = any> extends AxiosError<T, D> {

  // @ts-expect-error different types for `code`
  declare code: number;
  declare raw: string;
  declare extra: any;

  // eslint-disable-next-line max-params
  constructor(
    code: number,
    raw: string,
    extra?: any,
    config?: AxiosRequestConfig<D>,
    request?: any,
    response?: AxiosResponse<T, D>,
  ) {
    let message = raw;
    if (typeof raw !== 'string') {
      message = JSON.stringify(raw);
    }
    super(message, undefined, config, request, response);
    this.name = 'ServerError';
    this.code = code;
    this.raw = raw;
    this.extra = extra;
  }
}

```



### 单个并发请求

```
const cancelTokenSourceMap = new Map<symbol, CancelTokenSource>();
const abortControllerMap = new Map<symbol, AbortController>();

export const singletonInterceptor: RequestInterceptor = config => {
  const { singleton } = config;
  if (singleton) {
    const oldController = abortControllerMap.get(singleton);
    if (oldController) {
      oldController.abort();
    }
    const oldSource = cancelTokenSourceMap.get(singleton);
    if (oldSource) {
      oldSource.cancel(`Request canceled with singleton ${String(singleton)}`);
    }
    if (typeof AbortController === 'function') {
      const controller = new AbortController();
      abortControllerMap.set(singleton, controller);
      config.signal = controller.signal;
    } else {
      const { CancelToken } = axios;
      const source = CancelToken.source();
      cancelTokenSourceMap.set(singleton, source);
      config.cancelToken = source.token;
    }
  }
  return config;
};

```

