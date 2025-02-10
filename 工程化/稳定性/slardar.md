### 平台接入和相关功能

监控平台提供 sdk，通过 npm 包引入

默认集成

- PV 插件
  - 首屏：sdk 初始化时发送一次 PV，hash模式提取 hash，history模式提取 path
  - 非首屏：监听 history.pushState && history.replaceState 以及全局 popstate 事件 / hashchange

- JS 错误插件：监听全局 error 和 unhandledrejection
- 静态资源错误插件：监听全局 error，因为静态资源加载异常不会冒泡到 window，需要在捕获阶段监听（iframe 和 css(url) 无法捕捉
- XHR 监控插件：对原生 XHR 对象进行 hook
- Fecth 监控插件：对 fetch 方法进行 hook
- 性能监控相关插件：performance API
- 白屏插件...





### 项目封装

将 slardar 初始化过程包装在 **vue plugin** 里，方便复用

- 在 初始化时，设置 vue.config.errorHandler，捕获到 error 时上报 'captureException'

- 集成 imagex 图片监控插件
  - 收集图片尺寸，格式，加载耗时等指标，
  - 对图片渲染情况（压缩率，格式，分辨率，懒加载）进行评估
- 定义插件
  - jsError: ignoreErrors
  - ajax：因为安全策略不能采集 request body 和 response body，只能上报 logid
  - PV 上报：将 pathname 中的数字去除（各种 id）

- 定义上下文：referrer，pathname，hostname，lang，userId，version，page
- 挂载生命钩子
  - beforeSend（完成采集但未包装数据）：如果是  http 在上报接口数据时在 url 上添加 service_name 和 service_name <= 这些信息在 header 上。
  - Report （数据包装完成）：在上报之前，如果是 boe 环境将 js error 用弹窗显示，方便及时修复



React ：slardar 初始化过程放在脚本里，直接在 index.tsx 里引入执行