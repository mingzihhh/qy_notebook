## vue-async-manager

在 Vue 应用中更轻松的管理异步调用

- 异步组件(`Async Component`)的加载

```
import { lazy } from 'vue-async-manager'
 
const asyncComponent = lazy(() => import('./my-async.component.vue'))


// 配合 suspense 使用
  <div id="app">
    <!-- 使用 Suspense 组件包裹可能出现异步组件的组件树 -->
    <Suspense>
      <!-- 展示 loading -->
      <div slot="fallback">loading</div>
      <!-- 异步组件 -->
      <asyncComponent1/>
      <asyncComponent2/>
    </Suspense>
  </div>
```

- 发送异步请求从 API 中获取数据

