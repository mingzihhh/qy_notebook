## v-model原理

在 Vue 中，我们可以通过 `v-model`来实现双向绑定。

数据响应：数据变化->dom视图 

数据绑定：双向关系

```
addProp(el, 'value', `(${value})`)
addHandler(el, event, code, null, true)
```

给 `el` 添加一个 `prop`，相当于我们在 `input`上动态绑定了 `value`，又给 `el` 添加了事件处理，相当于在 `input` 上绑定了 `input` 事件，其实转换成模板如下

```
<input
  v-bind:value="message"
  v-on:input="message=$event.target.value">
```

态绑定了 `input` 的 `value` 指向了 `messgae`变量，并且在触发 `input` 事件的时候去动态把 `message`设置为目标值，这样实际上就完成了数据双向绑定了，所以说 `v-model` 实际上就是语法糖。

`v-model` 是 Vue 双向绑定的真正实现，但本质上就是一种语法糖，它即可以支持原生表单元素，也可以支持自定义组件。在组件的实现中，我们是可以配置子组件接收的 `prop` 名称，以及派发的事件名称。