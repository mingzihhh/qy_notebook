## Pinia 优势

- 完整的 TypeScript 支持
- 面向过去和未来, 同时能支持 **Compostion Api 和 Options Api** 写法
- Pinia 不需要嵌套结构的模块，设计上提供的是一个**扁平的结构**， 可以更灵活的组织各个 store
- 设计上更加扁平精简, **删除 mutations**, 统一在 actions 里操作 state.
  - Vuex mutations 的作用：存储数据快照，方便 devtool 跟踪
  - 借助 Vue 3 的响应式系统和 Proxy，Pinia 在没有 `mutation` 的情况下，依然可以实现状态的自动跟踪和调试支持
    - 在 Vue 2 中，响应式系统的实现基于 `Object.defineProperty`，不便于直接追踪复杂数据的修改（比如新增属性），因此需要 `mutation` 来集中管理状态变化。而 Vue 3 的 Proxy 机制则使得这些额外的封装不再必要。
  
- 官方认证, Vuex 将不再维护, Pinia 将作为官方认可的 Vuex5



**如何迁移**

短期内 Vuex 和 Pinia 共存, 新需求的 store 用 Pinia 来写. 等有人力时进行全面快速的迁移.

pinia 中 使用 vuex ： 引用全局 store，比如 store.getters['app/isPayment']

```
import store from '@/store';
```



### 实现原理

- Pinia 内部使用了 Vue 3 的 `reactive` 和 `ref` API 来确保 store 的状态是响应式的，并且 Vue 3 的响应式系统会在状态发生变化时自动触发视图更新。
- 使用`effectScope` 来自动管理 store 中的响应式副作用，当 store 销毁或组件卸载时，相关的副作用会被清理。通过 `effectScope` 可以动态管理 store 的副作用，确保这些 store 的副作用在需要时才会启动，并在不需要时销毁。
