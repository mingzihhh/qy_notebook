## 虚拟dom

#### 背景

当你用传统的源生api或jQuery去操作DOM时，浏览器会从构建DOM树开始从头到尾执行一遍流程。比如当你在一次操作时，需要更新10个DOM节点，理想状态是一次性构建完DOM树，再执行后续操作。但浏览器没这么智能，收到第一个更新DOM请求后，并不知道后续还有9次更新操作，因此会马上执行流程，最终执行10次流程。显然例如计算DOM节点的坐标值等都是白白浪费性能，可能这次计算完，紧接着的下一个DOM更新请求，这个节点的坐标值就变了，前面的一次计算是无用功。

假如一次操作中有10次更新DOM的动作，虚拟DOM不会立即操作DOM，而是将这10次更新的diff内容保存到本地的一个js对象中，最终将这个js对象一次性attach到DOM树上，通知浏览器去执行绘制工作，这样可以避免大量的无谓的计算量。

用js对象模拟DOM节点的好处是，页面的更新可以先全部反映在js对象上，操作内存中的js对象的速度显然要快多了。等更新完后，再将最终的js对象映射成真实的DOM，交由浏览器去绘制。

#### 虚拟dom

<https://www.jianshu.com/p/616999666920>

Virtual DOM 就是用一个原生的 JS 对象去描述一个 DOM 节点，对真实的dom发生的变化保持追踪所以它比创建一个 DOM 的代价要小很多。

在 Vue.js 中，Virtual DOM 是用 VNode 这么一个 Class 去描述，是一个树状结构。它存储了对应dom的一些重要参数，在改变dom之前，会先比较相应虚拟dom的数据，如果需要改变，才会将改变应用到真实dom上。

Vnode包括节点类型、属性键值对、子节点、key和count等，不包括操作dom的方法.

(key是props的键值，count是子节点的数量)

Virtual DOM 除了它的数据结构的定义，映射到真实的 DOM 实际上要经历 VNode 的 **create、diff、patch**等过程。

#### create

VNode 的 create 是通过的 createElement 方法创建的

1. 规范化`children` :`children` 变成了一个类型为 VNode 的 Array。
2. 创建一个vnode实例

#### update

 `vm._update` :把这个 VNode 渲染成一个真实的 DOM 并渲染出来



#### patch

patch将新老VNode节点进行比对，然后将根据两者的比较结果进行最小单位地修改视图，而不是将整个视图根据新的VNode重绘。patch的核心在于**diff算法**，这套算法可以高效地比较viturl dom的变更，得出变化以修改视图。

diff算法是通过**同层的树节点进行比较**而非对树进行逐层搜索遍历的方式，所以时间复杂度只有O(n)，是一种相当高效的算法。

![img](https://upload-images.jianshu.io/upload_images/8644402-23bd6f96fdeb76cd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540/format/webp)

比较后会出现四种情况：
1、此节点是否被移除 -> 添加新的节点 
2、属性是否被改变 -> 旧属性改为新属性
3、文本内容被改变-> 旧内容改为新内容
4、节点要被整个替换 -> 结构完全不相同 移除整个替换

