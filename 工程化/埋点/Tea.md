## Tea

主要使用 XHR 和 sendBeacon 方式上报

- 正常事件会走事件队列，队列的大小默认为10，30ms内的事件都会编成一个队列进行发送。队列的大小和30ms的时间都可以配置。

- 使用浏览器 api sendbeacon来上报事件，用于离开页面或者切换路由时的上报，保证上报的成功率，此方法不会走事件队列，直接发送。另外，beconEvent发送的事件请求不是XHR，请在network中的all分类进行查看（类型是json或者ping）

  注：此方法有一定的兼容性问题和数据大小限制，不建议在正常埋点场景下使用，仅限于离开页面等特殊场景。

  大小限制：64000个字符

