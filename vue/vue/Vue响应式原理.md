## Vue响应式原理

##### 响应式原理

要点

1. 使用 Object.defineProperty 把对象属性全部转为 getter/setter；通过每个组件实例相应的watcher对象，在组件渲染的过程中把“接触”过的数据属性记录为依赖。当属性被访问和修改时通知watcher变化，使组件更新。(**如何追踪变化**)

2. Vue 不能检测到对象属性的添加或删除，原因是vue在初始化实例时对属性执行getter/setter转化，解决方法是手动调用 Vue.set 或者 this.$set（**检测变化的注意事项**）

3. Vue 不允许动态添加根级响应式属性，所以你必须在初始化实例前声明根级响应式属性（**声明响应式属性**）

4. Vue **异步**执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。

   想获得更新后的 DOM 状态，可以在数据变化之后立即使用 `Vue.nextTick(callback)`。这样回调函数将在 DOM 更新完成后被调用。

   

Vue 内部使用了 `Object.defineProperty()` 来实现数据响应式，通过这个函数可以监听到 `set` 和 `get` 的事件。

（ Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。**存取描述符**，访问属性执行get，修改属性执行set）



响应式系统简述:

- 任何一个 Vue Component 都有一个与之对应的 Watcher 实例。
- Vue 的 data 上的属性会被添加 getter 和 setter 属性。
- 当 Vue Component render 函数被执行的时候, data 上会被 触碰(touch), 即被读, getter 方法会被调用, 此时 Vue 会去记录此 Vue component 所依赖的所有 data。(这一过程被称为**依赖收集**)
- data 被改动时（主要是用户操作）, 即被写, setter 方法会被调用, 此时 Vue 会去通知所有依赖于此 data 的组件去调用他们的 render 函数进行更新。



## Proxy与Object.defineProperty的优劣对比?

Proxy的优势如下:

- Proxy可以直接监听对象而非属性
- Proxy可以直接监听数组的变化
- Proxy有多达13种拦截方法,不限于apply、ownKeys、deleteProperty、has等等是`Object.defineProperty`不具备的
- Proxy返回的是一个新对象,我们可以只操作新的对象达到目的,而`Object.defineProperty`只能遍历对象属性直接修改
- Proxy作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利

Object.defineProperty的优势如下:

- 兼容性好,支持IE9



基于 Proxy 的响应式系统，Vue3 解决了这些限制，使得以下几种数据更新可以被监听到，使 Vue 3 的响应式系统更加强大和灵活，开发体验也更佳：

1. **新增或删除对象的属性**

在 Vue 2 中，无法监听对象属性的新增和删除。例如：

```javascript
// Vue 2 中
const obj = {};
Vue.set(obj, 'newProp', 'value'); // 需要使用 Vue.set 手动添加新属性
delete obj.newProp;                // 删除属性不会触发响应
```

在 Vue 3 中，`Proxy` 能够监听对象属性的新增和删除，因此直接赋值或删除属性会触发响应式更新：

```javascript
// Vue 3 中
const obj = reactive({});
obj.newProp = 'value'; // 可以直接新增属性并触发响应
delete obj.newProp;    // 可以删除属性并触发响应
```

2. **数组的索引和长度变化**

在 Vue 2 中，无法检测到数组的索引赋值和 `length` 修改。例如：

```javascript
// Vue 2 中
const arr = [];
arr[1] = 'value';    // 修改数组索引不会触发响应
arr.length = 0;      // 修改数组长度不会触发响应
```

Vue 3 可以监听数组的索引和长度变化，以上操作都会触发视图更新：

```javascript
// Vue 3 中
const arr = reactive([]);
arr[1] = 'value';    // 可以直接通过索引赋值触发响应
arr.length = 0;      // 修改数组长度会触发响应
```

3. **嵌套对象的动态添加**

在 Vue 2 中，嵌套对象的新增属性也需要使用 `Vue.set`，否则无法被检测到：

```javascript
// Vue 2 中
const state = {
  nested: {}
};
Vue.set(state.nested, 'newProp', 'value'); // 必须使用 Vue.set
```

在 Vue 3 中，嵌套对象的属性动态添加会自动触发响应更新：

```javascript
// Vue 3 中
const state = reactive({
  nested: {}
});
state.nested.newProp = 'value'; // 直接赋值即可触发响应
```

4. **`Map`、`Set` 等原生集合类型**

在 Vue 2 中，`Map` 和 `Set` 等集合类型不受响应式系统支持，无法监听它们的变化。Vue 3 增加了对 `Map`、`Set`、`WeakMap` 和 `WeakSet` 的响应式支持：

```javascript
// Vue 3 中
const map = reactive(new Map());
map.set('key', 'value'); // 可以监听 Map 的 set 操作
map.delete('key');        // 可以监听 Map 的 delete 操作

const set = reactive(new Set());
set.add('value');         // 可以监听 Set 的 add 操作
set.delete('value');      // 可以监听 Set 的 delete 操作
```

5. **更好的检测数组的多维嵌套结构**

Vue 2 对多维数组和深层嵌套结构的响应式支持较弱，容易漏掉一些变化。Vue 3 的 `Proxy` 可以更好地处理嵌套对象或数组结构的变更，使得深层的嵌套数据也能被完全响应式地追踪。
