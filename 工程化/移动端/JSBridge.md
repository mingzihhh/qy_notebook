## JSB 通信

https://juejin.cn/post/7293728293768855587

#### 容器

客户端可以通过 **Webview 容器打开**相应的h5链接，也可以通过 Webview 容器来执行相应的 JS 代码或者注入一些运行的环境参数。反过来，h5 借助 Webview 容器可以和客户端Native 进行通信执行相应的方案。

- iOS

WebKit: WKWebView (UIWebview 暂不考虑)

- Android

android.webkit.WebView

#### JSBridge

Native <-> JSBridge <-> JavaScript

有了JSBridge 就可以从 h5 内获得端上的native 能力。JSBridge 就像其名称中的『Bridge』的意义一样，是 Native 和非 Native 之间的桥梁，它的核心是 **构建 Native 和非 Native 间消息通信的通道**，而且是 **双向通信的通道**。

所谓 **双向通信的通道**:

- JS 向 Native 发送消息 : 调用相关功能、通知 Native 当前 JS 的相关状态等。
- Native 向 JS 发送消息 : 回溯调用结果、消息推送、通知 JS 当前 Native 的状态等

#### 客户端路由

可以通过特定规则的 URL 来访问客户端内的某些内容。在Android 和 iOS 都有一定的实现。

- [Google Applink(Android)](https://developer.android.com/training/app-links)
- [Universal LInk (iOS)](https://developer.apple.com/documentation/xcode/supporting-associated-domains)

同时都支持常规的域名对应到app。常规的表现就是访问了配置域名时，ios safari 浏览器中会有提示可以在app内访问该页面

#### 细分场景 - 前端视角

- 客户端打开h5页面 - 客户端提供webview 容器
- h5页面与客户端通信 - JSBridge 函数与函数签名（接口）约定
  - window.postMessage 传递信息到客户端
  - 客户端调用 JS 方法返回信息
- h5页面的跳转
  - 客户端拦截相应的url，比如 myscheme://page2?a=1&b=1
- h5页面的提示信息 / 或者通信接口，弹框
  - 常见的有 alert promt confirm

