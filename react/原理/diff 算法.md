https://7km.top/algorithm/diff

diff 算法作用在 reconcile 阶段，也就是构建 JSX 编译转换之后，FIber 树建立之前。

在第一次渲染时，React 会直接将虚拟 DOM 转换为 Fiber 树，不需要进行 diff 操作。每个虚拟 DOM 节点都对应一个新的 Fiber 节点。在后续渲染中，React 会生成一个新的虚拟 DOM 树，**对新 reactElemement与旧 fiber 进行比较, 判定旧 fiber 是否可以复用**。这个过程决定了如何更新 Fiber 树：

1. 对于可以复用的节点，React 会在相应的 Fiber 节点上打上更新的标记
2. 旧的、无法复用的节点将被打上删除标记
3. 新增的节点则会被打上新增标记



### Diff 算法的三大假设/原则

React 的 diff 算法引入了几个关键的假设，这使得算法能在 O(n) 的时间复杂度内完成，而不是 O(n^3)（普通两棵树的最小差异对比复杂度）：

1. **不同类型的元素产生不同的树，相同类型的节点才继续 diff**：
   - 如果两个虚拟 DOM 节点类型不同，React 会直接销毁原来的节点及其子树，重新创建新节点及其子树。
   - 例如，从 `<div>` 变为 `<p>`，React 不会试图复用旧的 DOM 节点，而是直接移除 `<div>`，并创建 `<p>`。
2. **同层次比较，不跨层次**：
   - React 只比较同一层级的节点，不会跨层次比较。如果父节点不同，则子节点会被完全销毁并重新创建。
   - 我们可以从同级的节点数量将Diff分为两类：
     - 当 newChild 类型为 JSX 对象、number、string，代表同级只有一个节点
     - 当 newChild 类型为 Array，同级有多个节点
   - 这种方式大大简化了树的对比过程，避免了复杂的跨层次对比。
3. **通过 key 进行子节点的对比**：
   - 当对比列表（如 `ul` 或 `ol` 中的 `li`）的子节点时，React 使用 key 属性来确定哪些节点是相同的。
   - 如果不提供 key，React 会使用默认的索引作为 key，从而可能导致性能问题（特别是在节点位置变化时）。
   - key 使得 React 能够准确地识别节点，即使它们的位置改变了。通过 key，React 可以有效地复用、插入、删除节点。



### 具体流程

#### 比较对象

 fiber 对象与 ReactElement 对象相比较

- 注意: 此处有一个误区, 并不是两棵 fiber 树相比较, 而是旧 fiber 对象与新 ReactElement 对象向比较, 结果生成新的 fiber 子节点
- 可以理解为输入 `ReactElement`, 经过`reconcileChildren()`之后, 输出`fiber`



#### 比较方案

##### 单节点

1. 如果是新增节点, 直接新建 fiber, 没有多余的逻辑
2. 如果是对比更新，**先比较 key，再比较 element.type 和 fiber.elementType**，如果都相同则复用，否则删除当前 fiber 并新建（复用节点是新建 fiber 但复用 stateNode 属性，复用 DOM 节点



##### 可迭代节点

- 多节点一般会存在两轮遍历，第一轮寻找最长公共序列（key 相同）， 公共序列的节点都视为可复用；第二轮遍历剩余非公共序列
- 实际就是 2 个序列之间的比较（链表 oldFiber 和数组 newChildren), 最后返回合理的 fiber 序列

**第一轮循环**

1. `let i = 0`，遍历`newChildren`，将`newChildren[i]`与`oldFiber`比较，判断`DOM节点`是否可复用。
2. 如果可复用，`i++`，继续比较`newChildren[i]`与`oldFiber.sibling`，可以复用则继续遍历。
3. 如果不可复用，分两种情况：

- `key`不同导致不可复用，立即跳出整个遍历，**第一轮遍历结束。**
- `key`相同`type`不同导致不可复用，会新增 fiber 并将 `oldFiber`标记为`DELETION`，并继续遍历

4. 如果`newChildren`遍历完（即`i === newChildren.length - 1`）或者`oldFiber`遍历完（即`oldFiber.sibling === null`），跳出遍历，**第一轮遍历结束。**



**第二轮循环**

遍历剩余非公共序列, 优先复用 oldFiber 序列中的节点

- `newChildren`与`oldFiber`同时遍历完：diff结束
-  `newChildren`没遍历完，`oldFiber`遍历完：意味着没有可以复用的节点了，遍历剩下的`newChildren`为生成的`workInProgress fiber`依次标记`Placement`
- `newChildren`遍历完，`oldFiber`没遍历完：意味着有节点被删除了，需要遍历剩下的`oldFiber`，依次标记`Deletion`。
- 如果`newChildren`与`oldFiber`都没遍历完：
  - 将 oldFiber 剩余序列加入到一个 map 中，目的是为了第二次循环能顺利的找到可复用节点， {元素的key：老的fiber节点}
  - 继续遍历 `newChildren`，如果可以复用，会把`key`从`map`中删除，并判断位置是否移动。每次新 jsx 复用到节点，`lastPlaceIndex` 会指向最后一个成功复用的老`fiber`节点，
    - 如果复用节点  oldIndex < lastPlaceIndex，说明该 Fiber 节点在新的列表中出现的位置比它之前的位置靠后，需要在旧 fiber 树中向后移动位置，标记为 `Placement`
    - 如果复用节点 oldIndex > lastPlaceIndex，节点位置可以保持不动，lastPlaceIndex 更新为 oldIndex
    - 如果没有复用，需要插入，标记为 `Placement`
  - map 中只剩还没被复用的节点，等着新的`jsx`数组遍历完，`map`里面的`fiber`节点全部设置为删除



### 算法优化的核心思想

React diff 算法的核心思想是避免在整个树上做完全对比，而是根据上述假设进行有选择性的比较，从而将复杂度降低到 O(n)，其中 n 是节点的总数。以下是一些常见的优化点：

- **通过 key 提高列表渲染效率**：帮助 React 更加高效地对比和操作 DOM。
- **同类型节点复用**：如果节点类型相同，React 只会更新节点的属性，而不会销毁和重新创建整个节点。
- **减少不必要的渲染**：在生命周期方法中进行优化，比如 `shouldComponentUpdate`、`PureComponent` 或 `React.memo` 来防止不必要的更新。

 
