#### 方案对比

iconpark https://iconpark.oceanengine.com/home

- 设计友好的可视化维护
- 支持按需引入
- 基于 vue2 / vue3 的自动组件生成
- 公司内部开源项目



iconfont https://www.iconfont.cn/

- 将 SVG 图标转换成字体，并通过 CSS 进行管理

- 设计友好的可视化维护
- 需要自定义组件，不易维护
- 不支持按需引入
- 第三方服务



svg 图标

- 支持按需引入
- 需要自定义组件，不易维护
- 基于代码仓库托管
  - 使用本地存储的 SVG 图标并生成雪碧图 svg-sprite-loader，在项目中以模块的方式进行引用

