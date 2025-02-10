 Vue 3 引入了组合式 API，旨在通过**函数和逻辑组合**的方式增强代码的组织性和复用性。`setup` 是组合式 API 的核心，用于定义组件的逻辑和状态。它是一个特殊的组件选项，作为组件初始化时的入口函数，允许你以更直观的方式编写代码，并且可以轻松组织和复用逻辑。



### setup 功能

- **组件初始化逻辑**：
  - `setup`  是组件创建过程中最早调用的生命周期之一（在 `beforeCreate` 和 `created` 之前）
  - 可以在这里定义组件的数据、方法、计算属性，以及生命周期钩子。

- **提供响应式数据**：
  - 在 `setup` 中，可以通过 Vue 提供的 `ref` 和 `reactive` 创建响应式数据。
  - 返回的数据会被直接暴露给模板使用。

- **逻辑复用**：
  - 可以使用组合式 API（比如自定义的 hooks 或第三方库）来复用逻辑，而不是依赖传统的 mixins 或 `extends`。
- **访问外部属性和方法**：
  - `setup` 的参数提供对 `props` 和 `context` （提供 attrs，slots，attrs 等）的访问，用来处理父组件传递的属性、事件以及插槽。
- **更清晰的代码组织**：
  - 通过组合式 API，`setup` 函数可以将逻辑按功能分组，而不需要在选项式 API 中分散管理（如 data、methods、computed 等分散定义）。



### script setup 

Vue 3.2 引入的一种语法糖，旨在简化组合式 API 的使用。`<script setup> `是在 **Vue 编译器中进行的一个语法转换**，它通过编译时处理将常规的组合式 API 写法转化为内部支持的格式。

- 自动导入 Vue API，将 `ref`、`reactive`、`computed` 等 API 自动注入
-  无需 `export default`：Vue 会通过编译时转换，自动生成 `setup()` 函数，并将其中的变量和方法暴露给模板。
- `<script setup>` 中的变量、函数等会在编译过程中直接暴露给模板，而不需要通过 return 显式返回。
- 自动推导类型支持：由于没有 `this` 上下文，Vue 会更好地推导类型，特别是在组合式 API 中，推导更为准确。
- 自动优化和性能提升：在编译时，Vue 会对 `<script setup>` 中的代码进行优化处理，减少不必要的操作。例如，它可以避免不必要的重新渲染和计算，提升应用的性能。



### 原理

vue 文件需要编译为包括 render 函数的 js 文件提供给浏览器，然后执行 render 生成虚拟 DOM，再调用浏览器的 DOM API 根据虚拟 DOM 生成真实 DOM 挂载到浏览器上。

编译时会用 render 函数 和 sfc 对象取代 template 和 script ，并用 sfc.render = render 将 render 函数挂载到 sfc 对象上，并将 sfc export default 出去。所以实质上 import 一个 vue 文件，等于引用了这个 js 对象，这个 js 对象包括 render 方法 和 setup 方法：

template => render 方法 ，setup 语法糖 => setup 方法

**setup 方法**： 执行后返回一个对象，包括在 **setup 中定义的顶级变量和 import 导入的内容**，将返回值对象赋值给 vue 实例的 setupState 属性

**render 方法**：可以通过 $setup 访问定义的变量，而 $setup 是调用 render 函数传入的第四个变量，即从 setupState 属性 上取值
