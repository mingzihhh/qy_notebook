## mobx

### 机制

事件触发了Actions，Actions 作为唯一修改 State 的方式，修改了State，State的修改更新了计算值 Computed，计算值的改变引起了Reactions 的改变，导致了 UI 的改变，Reactions可以经过事件调用 Actions。

![img](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGjjuK2ArCic0yglWtZQssLwB0Xq2FRUGTXCs8BMKgDTgxERZQD0sRLRpic6kSIztQby2nJl9Vp7CYNw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Observable state**：可观察对象，状态对象的属性会被MobX自动转换为可观察的，包括深层属性

**Action**：用于修改状态的函数。

- runInAction：用于处理临时状态更新逻辑，尤其是异步操作中的状态更新。更灵活，但可能降低可读性

**Computed**：计算值，基于其他状态生成的值， MobX 会根据依赖进行优化。

**Observer**：HOC，用于将组件标记为观察者，使组件能够在状态变化时自动重新渲染。



### 和 redux 对比

相同点：都是单向数据流，通过 action 触发全局 state 更新，然后通知视图。

不同点：

- 状态方便：
  - redux 是每次返回一个全新的状态，一般搭配实现对象 immutable 的库来用，减少创建新对象的开销；
  - mobx 每次都是修改的同一个状态对象，基于响应式代理，也就是proxy 代理 get、set 的处理，get 时把依赖收集起来，set 修改时通知所有的依赖做更新。和 vue 的响应式代理很类似。
- 性能方面： mobx 的性能会比 redux 高一些
  - redux 那种方式每次都要返回一个新的对象，mobx 直接修改原对象
  - redux 通知依赖更新的时候是全部通知的，mobx 因为收集了每个属性的依赖，可以精准的通知。



### 实现原理

https://mp.weixin.qq.com/s/zWp-qSVeOjzHKiOYLi2s0g

![img](https://mmbiz.qpic.cn/mmbiz_png/YprkEU0TtGjjuK2ArCic0yglWtZQssLwB0aibdtialrhLHk1gzrpvFzJNFibodR1VPJjntoYMibygnqqibJYULZdM1gg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 创建响应式

创建对象的时候 mobx 会对属性和方法做代理，在对象上添加一个Symbol($mobx)  的隐藏属性，用来保存 ObservableObjectAdministration 对象，它的 values 用 map 记录每个 key 的依赖，对属性的 get 和 set 都会被代理到这个 ObservableObjectAdministration 的 getXxx 和 setXxx 方法上



#### 收集依赖

组件用 observer 高阶组件做一层包装，对组件做一层代理，返回新的组件。

- 在这层代理里面，创建了 Reaction 对象，也就是收到更新的通知之后怎么做出反应，在回调函数里用 setState([]) 的方式实现了强制更新。
- 这层高阶组件的代理里会把当前组件设置到全局，这样后面做 get 的依赖收集的时候就能拿到对应的组件了。
