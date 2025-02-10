Vue 2.7 的特性：

1. 内置 composition api，同时支持 SFC <script setup>
2. Css v-bind 支持
3. 工具链支持，比如 volar
4. 与 vue 3 一致性更多



### 如何升级

#### 依赖升级

- vue （以及相关的运行时依赖）相关依赖升级
  - "vue": "^2.7.13",
  - Vue-loader
- 移除 @vue/composition-api ，vue-template-compiler 等不再需要的依赖

#### 编译配置修改

- 修改 vloar ts 配置
- 在 eslint 中禁用 @vue/composition-api 

#### 运行时修改

- 移除对 @vue/composition-api 的使用
- 替换对 @vue/composition-api 的命名导出， 从 vue 中导出
- 清除 error warning