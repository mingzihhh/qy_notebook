## Vuex

vuex是为vue开发的状态管理工具，方便组件之间的数据共享和修改

Vuex实现了一个单向数据流，在全局拥有一个State存放数据，当组件要更改State中的数据时，必须通过Mutation进行，Mutation同时提供了订阅者模式供外部插件调用获取State数据的更新。而当所有异步操作(常见于调用后端接口异步获取更新数据)或批量的同步操作需要走Action，但Action也是无法直接修改State的，还是需要通过Mutation来修改State的数据。最后，根据State的变化，渲染到视图上。

https://juejin.cn/post/6922616138169843725#heading-33

### 核心-store

new Vuex.Store  //创建store

- 单一状态树，每个应用将仅仅包含一个 store 实例，包含了全部的应用层级状态，响应式

- “store”基本上就是一个容器，它包含着初始state，mutations，actions，modules等
  - **State**：即状态，也就是Vuex核心管理的对象；
  - **Getters**：派生状态，对state的二次包装（例如：默认后端时间戳转化为日期格式），Getters里的方法所有组件都可以使用；
  - **Mutations**：所有状态的修改都是通过提交mutation，mutation类似事件，定义事件类型和回调函数，而回调函数就是进行状态修改的地方，状态修改一定是同步进行，从而确保状态修改可以被追踪到；
  - **Actions**：同样是进行状态修改，跟Mutations唯一不同的是进行异步的状态修改，本质是在回调提交Mutation；
  - **Modules**：为了解决状态树庞大进而store臃肿的问题，提出module概念，分化store到每个module，每个module都是一个小store。
- 通过在vue根实例中注册 `store` 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 `this.$store` 访问到

### state

- 存放状态
- 由于 Vuex 的状态存储是响应式的，从 store 实例中读取状态最简单的方法就是在[计算属性](https://cn.vuejs.org/guide/computed.html)中返回某个状态，使用 `mapState` 辅助函数帮助生成计算属性，获得多个状态，返回一个对象

```js
import { mapState } from 'vuex'

export default {
  // ...
  computed: 
    //对象展开运算符
    ...mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
      
    // 映射 this.count 为 store.state.count
  	'count'
  })
}
```



### getter

- 在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

- store.getters.doneTodos 通过属性访问，会缓存

  store.getters.doneTodos(args) 访问方法访问，每次都会去进行调用，而不会缓存结果。

- `mapGetters` 辅助函数将 store 中的 getter 映射到局部计算属性

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

### mutation

- 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。
- 类似事件注册，每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**，接受 state 作为第一个参数；要唤醒一个 mutation handler，你需要以相应的 type 调用 **store.commit** 方法

```
store.commit('increment', {
  amount: 10  //payload，额外的参数
})
或
store.commit({
  type: 'increment',
  amount: 10
})
```

- 遵守 Vue 的响应规则

  - 提前在你的 store 中初始化好所有所需属性

  - 当需要在对象上添加新属性时，你应该使用 `Vue.set(obj, 'newProp', 123)`, 或者以新对象替换老对象。

    ```
    state.obj = { ...state.obj, newProp: 123 }
    ```

- **mutation 必须是同步函数**

  > 如果mutation是异步函数，当 mutation 触发的时候，回调函数还没有被调用，devtools 不知道什么时候回调函数实际上被调用——实质上**任何在回调函数中进行的状态的改变都是不可追踪的**。

- 你可以在组件中使用 `this.$store.commit('xxx')` 提交 mutation，或者使用 `mapMutations` 辅助函数将组件中的 methods 映射为 `store.commit` 调用（需要在根节点注入 `store`）。

### action

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意**异步操作**，通过 `store.dispatch` 方法触发
- Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，可以用参数结构简化代码获取state或者提交 mutation
- 用promise或async/await组合action
- 在组件中使用 `this.$store.dispatch('xxx')` 分发 action，或者使用 `mapActions` 辅助函数将组件的 methods 映射为 `store.dispatch` 调用（需要先在根节点注入 `store`）

```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', 
      // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' 
      // 将 `this.incrementBy(amount)` 映射为`this.$store.dispatch('incrementBy', amount)`
    ]),
    
    ...mapActions({
      add: 'increment' 
      // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```

### module

- 在应用复杂时，为了防止store对象臃肿，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割

- mutation/getters： 第一个参数 state - 模块的局部状态对象

  action：context. state - 局部状态，context. rootState - 根节点状态

- 默认情况下，模块内部的 action、mutation 和 getter 是注册在**全局命名空间**的，使得多个模块能够对同一个 action 或 mutation 作出响应；不要在不同的、无命名空间的模块中定义两个相同的 getter 从而导致错误。

- 添加 `namespaced: true` ，使其成为带命名空间的模块；当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。

- 在使用 `mapState`, `mapGetters`, `mapActions` 和 `mapMutations` 这些函数来绑定带命名空间的模块时，可以将模块的空间名称字符串作为第一个参数传递给上述函数，这样所有绑定都会自动将该模块作为上下文。

  ```
  computed: {
    ...mapState('some/nested/module', {
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    ...mapActions('some/nested/module', [
      'foo', // -> this.foo()
      'bar' // -> this.bar()
    ])
  }
  ```

- 在命名空间模块中访问全局内容

  - state 和 getter：使用 rootState 和 rootGetters 参数
  -  action 或提交 mutation：将 `{ root: true }` 作为第三参数传给 `dispatch` 或 `commit` 
  - 注册全局 action：添加 `root: true`，并将这个 action 的定义放在函数 `handler` 中
  
  
  
  
  
  
  
  