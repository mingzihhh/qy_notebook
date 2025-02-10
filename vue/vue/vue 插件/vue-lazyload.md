## vue-lazyload

图片懒加载

### 核心思想

- 先将图片的src设置为同一张图片或者不设置，同时给img标签设置一个特殊属性，例如：data-src用于存放图片的真实预览地址；
- 若图片未进入可视区域时，展示同一张图片或者直接不展示图片，此时就不会发生http请求，当图片进入可视区域时，将data-src上的值赋给src，此时再发送http请求。

https://juejin.cn/post/6844904083724173326

```
import VueLazyLoad from 'vue-lazyload';

// 最外层static目录下的图片引用
Vue.use(VueLazyLoad, {
  error: '/static/images/defaultAvatar.png', // 此处是图片加载失败时候 显示的图片
  loading: '/static/images/defaultAvatar.png', // 此处是图片加载中 显示的图片
  attempt: 1, // 加载一屏图片
  preLoad: 1, // 失败尝试次数
});

// src下的assets目录下的图片
Vue.use(VueLazyLoad, {
  error: require('common/assets/defaultAvatar.png'), // 此处是图片加载失败时候 显示的图片
  loading: require('common/assets/defaultAvatar.png'), // 此处是图片加载中 显示的图片
  attempt: 1, // 加载一屏图片
  preLoad: 1, // 失败尝试次数
});

```

