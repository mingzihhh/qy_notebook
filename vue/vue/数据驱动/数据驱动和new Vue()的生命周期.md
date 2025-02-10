## 数据驱动和new Vue()的生命周期

指视图是由数据驱动生成的，我们对视图的修改，不会直接操作 DOM，而是通过修改数据。

DOM 变成了数据的映射，我们所有的逻辑都是对数据的修改，而不用碰触 DOM，这样的代码非常利于维护。

#### new Vue()

vue是一个类，只能通过 new 初始



beforeCreate：合并配置，初始化生命周期，初始化事件中心，初始化渲染   

created：初始化 data，props，computed，watch，methods 等等   

并没有渲染dom,如果组件在加载的时候需要和后端有交互，放在这俩个钩子函数执行都可以，如果是**需要访问 `props`、`data` 等数据的话，就需要使用 `created` 钩子函数。**

---

beforemouted：初始化el    

mounted：渲染vnode把vnode patch到真实dom，实例化watcher     

对于同步渲染的子组件而言，会触发它子组件的mounted钩子函数，这样一层层的递归调用，`mounted` 钩子函数的执行顺序也是先子后父。

**mounted 可以访问 dom**

---

beforeupdated：执行diff算法，对比改变是否需要触发UI更新  

updated：组件已更新    

---

beforedestroy：$destroy函数最开始执行的地方   

一系列的销毁动作，包括从 `parent` 的 `$children` 中删掉自身，删除 `watcher`，当前渲染的 VNode 执行销毁钩子函数等，执行完毕后再调用 `destroy` 钩子函数。

**在 `destroy` 钩子函数中可以做一些定时器销毁工作**、

