## 组件系统

- 创建全局组件且组件数量不会太多的情况下，可以使用 Vue.component；
- 如果需要创建多个实例且每个实例之间互不干扰，可以使用 Vue.extend，比如命令式 loading / toast / message 等

#### Vue.extend

https://juejin.cn/post/7239715295484821565?searchId=20240410155335C58825C0FCA62592C59F

- Vue.extend 是 Vue 的全局 API, 用于**局部注册组件并创建子类**，方法返回一个**组件构造器**，通过组件构造器创建组件实例，该实例的参数是一个包含组件选项的对象，用来在实例上扩展属性和方法。
- 通过 Vue.extend + mount 这对组合使一些动态渲染或使用 js 全局调用的组件变得更加灵活 。
- $mount 的原理：在 vm.$mount 方法中，会根据传入的 el 参数获取到对应的 DOM 元素，然后通过 updateComponent 方法创建一个渲染 Watcher 对象，并将其挂载到 Vue 实例上
- **使用场景**
  -  希望根据函数形式调用全局组件，比如ElementUI 里的$message，我们使用this.$message('hello')的时候，其实就是通过这种方式创建一个组件实例，然后再将这个组件挂载到了 body 上
  - 希望根据接口动态渲染组件

```
// 创建构造器
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})

// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')

<div id="mount-point">
  <p>Walter White aka Heisenberg</p>
</div>
```



#### Vue.component

- 注册全局组件。
- 第一个参数是自定义元素名称，也就是将来在别的组件中使用这个组件的标签名称。
- 第二个参数是将要注册的Vue组件。

```
import Vue from 'vue';
// 引入loading组件 
import Loading from './loading.vue';
// 将loading注册为全局组件，在别的组件中通过<loading>标签使用Loading组件
// 内部自动调用 Vue.extend
Vue.component('loading', Loading);

// 使用
<Loading />


or
Vue.component('Loading', Vue.extend({...}))
```



**element-ui 的按需引入为什么需要通过 Vue.use(component) 而不是通过 Vue.component(component)** 

```
const Button = {
  install(Vue) {
    Vue.component(Button.name, Button);  // 注册 Button 组件
  }
};
```

- `Vue.use(component)` 会调用组件的 `install` 方法，确保它能按照插件的方式正确注册并执行必要的初始化逻辑，允许更多的功能扩展
- Vue.use 更适合按需引入
  - 当组件库使用 `Vue.use(component)` 的方式封装时，组件会通过插件的 `install` 方法进行注册。这意味着，在按需引入组件时，只有当你通过 `import { Button } from 'element-ui'` 引入组件时，`Vue.use(Button)` 才会被调用，从而动态注册该组件，避免全局加载不必要的组件。
  - `Vue.component(component)` 会立刻注册组件，如果你引入一个组件，它会自动把组件注册到全局，这就不利于按需引入。
