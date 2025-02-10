1. element 样式覆盖放到 `assets/scss/theme-okee/element-ui `文件夹下，新建例如 `alert.scss` 这样的文件
2. 用到关于颜色/字体的变量，`@use "~@/assets/scss/theme-okee"`
3. Element theme-chalk 变量覆写：能覆盖 element 变量达到目的就覆盖 element 变量，其次再是选择器暴力覆盖
4. 不要用 `@import` 废弃语法，使用 @use
5. 变量尽量用 theme-okee 里的，不要引用 Element 变量（新的 Sass 命名规则下，他们的变量都是私有的('-' '_' 开头），无法导出）
6. Element 样式单独打包：目前组件库 SCSS 参与构建流程，虽然热启动有缓存，但是没有必要，组件的覆写也不是频发任务，可以在完成上述 milestones 后打成单独一个 CSS 文件，放 CDN 也行