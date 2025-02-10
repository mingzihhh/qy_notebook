## Fiber 是什么

Fiber 是 React 16 引入的一种新的协调引擎，用于解决和优化 React 应对复杂 UI 渲染时的性能问题（大组件更新时会出现卡顿）。

Fiber 架构的核心思想是**将虚拟 DOM 树的渲染更新过程拆分为可中断的小任务单元**，允许 React 在任务执行的过程中暂停、继续、丢弃或优先执行某些任务，从而提高整体性能和响应速度。



### 核心目标

- **能够中断渲染任务**：React 需要一种机制来拆分更新任务，使得它们可以被分阶段执行，并且可以在任务执行期间中断，以便处理更高优先级的任务（如用户输入、动画等）。
- **优先级调度**：不同的更新任务根据重要性分配不同的优先级，比如用户输入通常优先级最高，动画次之，数据更新的优先级则可以相对较低。
- **处理复杂的更新场景**：在某些场景下，可能需要优先展示某些内容或将长时间的更新分批完成。
- **更好的可扩展性**：使 React 更加灵活，能够应对未来的扩展和新的用例。



### Fiber 如何提升 React 应用的性能

- 增量渲染
- 并发模式
- 和 Suspense 紧密结合，允许 React 延迟渲染某些组件，直到它们所需的数据可用。
- 优先级调度
- 时间分片



### 设计原理

**JSX => reactElement => Fiber => DOM** 

Fiber 的核心概念是将整个更新过程看作是一个可以拆分的小单元，每个单元称为“Fiber”。**每个组件（（如函数组件、类组件或 DOM 元素））对应一个 Fiber 节点**，组件树则由多个 Fiber 节点构成，它包含了关于该组件的状态、更新和效果等信息，并且通过连接 `child`、`sibling` 和 `return` 形成树结构。所以Fiber 树表示一个**工作单元的链表**。

Fiber 节点的作用类似于传统 React 中的虚拟 DOM 结构，但它更加复杂，增加了调度与优先级处理的能力。它们帮助 React 管理组件的更新、渲染以及协调复杂的任务。Fiber 节点作为协调器，可以让 React 跟踪渲染工作的进度，同时支持将渲染任务拆分成可中断的小单元。

```
// 简化的 fiberNode
const fiberNode = {
  tag: WorkTag, // 对应节点的类型，如 HostComponent（DOM 元素）, FunctionComponent
  type: 'div', // 对应于 React 组件或 DOM 节点的类型, 一般来讲和fiber.elementType一致。 一些特殊情形下, 比如在开发环境下为了兼容热更新
  elementType: any,//一般来讲和ReactElement组件的 type 一致 比如div ul
  key: null, // 唯一标识, 和ReactElement组件的 key 一致
  stateNode: domElement, // 对应的真实 DOM 节点
  mode: TypeOfMode, // 二进制位Bitfield,继承至父节点,影响本fiber节点及其子树中所有节点. 与react应用的运行模式有关(有ConcurrentMode, BlockingMode, NoMode等选项)
  deletions: Array<Fiber> | null, // 存储将要被删除的子节点. 当设置了 enableNewReconciler=true才会启用
  
  // 组成 Fiber 树
  child: null, // 指向子 Fiber 节点
  sibling: null, // 指向兄弟 Fiber 节点
  return: parentFiber, // 指向父 Fiber 节点
  
  // 保存本次更新造成的状态改变相关信息
  pendingProps: {}, // 新的 props，用于和`fiber.memoizedProps`比较可以得出属性是否变动
  memoizedProps: {}, // 上一次生成子节点时用到的属性, 生成子节点之后保持在内存中
  updateQueue: mixed, // 存储state更新的队列, 当前节点的state改动之后, 都会创建一个update对象添加到这个队列中.
  memoizedState: any,// 上一次生成子节点之后保持在内存中的局部状态
  dependencies: Dependencies | null, // 该fiber节点所依赖的(contexts, events)等
  
  // Effect 副作用相关
  flags: 0, // 标识副作用类型，reconciler阶段会将所有拥有flags标记的节点添加到副作用链表中, 等待 commit 阶段的处理
  subtreeFlags: Flags, //替代16.x版本中的 firstEffect, nextEffect. 当设置了 enableNewReconciler=true才会启用
  nextEffect: Fiber | null, // 单向链表, 指向下一个有副作用的fiber节点
  firstEffect: Fiber | null, // 指向副作用链表中的第一个fiber节点
  lastEffect: Fiber | null, // 指向副作用链表中的最后一个fiber节点
  
  // 优先级相关
  lanes: Lanes // 本 fiber 节点的优先级
  childLanes: Lanes, // 子节点的优先级
  alternate: Fiber | null, // 双fiber缓存，指向对应的workInProgress FiberNode或者current FiberNode
};
```

在渲染过程中，React 会依次遍历 Fiber 节点，并决定如何处理每个节点的更新。例如：

1. **初始化渲染**：React 会为每个组件或元素创建一个 Fiber 节点，并且根据它们的类型生成虚拟 DOM 或类实例。
2. **更新过程**：
   - 当某个组件的 `props` 或 `state` 发生变化时，React 会创建新的 Fiber 节点，并通过 Fiber 树对比新旧节点（即 `pendingProps` 和 `memoizedProps` 的差异），以决定是否需要重新渲染。
   - Fiber 节点的结构使得这个过程可以被中断和恢复，这也是 Fiber 架构区别于老架构的关键点。
3. **副作用管理**：Fiber 节点通过 `effectTag` 标记了哪些 DOM 需要进行变更（如插入、更新、删除等）。React 在 "commit 阶段" 会读取这些标记，并在真实的 DOM 中执行相应的副作用操作。

```
function App() {
  return (
    <Header>
      <img />
      <span>hello! react18</span>
    </Header>
  );
}

function Header({ children }) {
  return <header>{children}</header>;
}

// 创建ReactDOMRoot对象
// 在创建过程中会调用 createRootImpl：
// 1. 创建 fiberRoot 和 HostRootFiber，使 fiberRoot.current = HostRootFiber, HostRootFiber 的 mode 在这时候设置
// 2. 标记 dom 对象, 把 dom 和 fiber 对象关联起来
const root = ReactDOM.createRoot(document.getElementById('root'));
// 调用render
root.render(<App />);
```

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/51bf62209e424c2596d9c0d2a8072e9f~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=748&h=1459&s=82338&e=jpg&b=ffffff" alt="img" style="zoom:50%;" />

在react初始化的时候，会创建三个全局对象，在三个对象创建完毕的时候，react初始化完毕。

**ReactDOMRoot**： 属于`react-dom`包，该对象暴露有render,unmount方法, 通过调用该实例的 `ReactDOM.render` 方法, 可以引导 react 应用的启动

**fiberRootNode**：属于`react-reconciler`包，作为`react-reconciler`在运行过程中的全局上下文, 保存 fiber 构建过程中所依赖的全局状态。此节点的current属性指向的就是 current Fiber树，对应的还有 workInprogress Fiber树，下面会解释。

**hostRootFiber**：属于`react-reconciler`包，这是 react 应用中的第一个 Fiber 对象, 是 **Fiber 树的根节点**, 挂载 React 应用的 dom 对应的 fiberNode。

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d8f42f545b7746d28eb06ac80e52731c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?" alt="img" style="zoom:50%;" />





### 工作机制

#### 双缓存

React Fiber 使用双缓存机制，维护两个 Fiber 树：当前正在渲染的树（current Fiber）和正在构建的新 Fiber 树（workInProgress Fiber ），两棵树之间互相以 alternate 属性连接。

- 如果是初次构造（初始化渲染），页面还没有渲染，此时页面对应的 fiber 树为空（fiberFoot.current = null）

- 当新树构建完成后，React 会将新的 Fiber 树替换为当前渲染的树 => 切换根节点 current 属性指向

**优点**：

- 避免在 UI 未完成更新时直接影响用户体验

- 方便并发打断后从断点恢复状态：在每一次 unitWork 执行完毕后，它的结果（更新后的Fiber）会被赋值给 workInProgress，也就是说 workInProgress 总是保存着最近一次的 unitWork 的结果。当我们从暂停中恢复时，直接读取这个全局变量，并从这个变量为起点继续完成 performWork 的工作即可。

  

#### 时间切片

时间切片的目的是在执行繁重的渲染任务时，避免阻塞主线程（Main Thread），从而保持应用的响应性，确保 UI 在大量计算时依旧保持流畅。



**工作流程**

1. **任务开始**：任务被拆分为多个小单元，开始执行。

2. **检查时间**：在每个单元结束时，检查是否超出时间限制。

3. **任务中断**：

- 如果时间耗尽，暂停当前任务，并将剩余任务加入调度队列。
- 使用调度 API（如 `MessageChannel`）注册下一个空闲时间回调。

4. **任务恢复**：在下一个时间片中继续执行剩余任务，直到所有任务完成



##### 时间切片的工作原理

1. **任务拆分**：当需要进行复杂或耗时的更新时，React 不再一次性完成整个渲染任务，而是将渲染过程分割成多个小的任务片段。

2. **任务调度**：React 使用调度程序（Scheduler），通过浏览器的空闲时间（**requestIdleCallback**）或其他高优先级 API（如 **MessageChannel**）来安排任务执行。React 计算哪些任务需要立即执行，哪些任务可以等待一会儿再执行。

3. **任务让步**：如果浏览器在渲染过程中接收到更高优先级的任务（如用户点击或键盘输入），React 可以暂停当前的渲染任务，等待这些高优先级任务完成后再继续处理剩余的渲染任务。

4. **优先级管理**：React 通过对不同类型的更新任务设置优先级，确保高优先级的任务（如用户输入）比低优先级的任务（如渲染大量不重要的组件）更早执行。

   

React 会把 vdom 树转成 fiber 链表，因为 vdom 里只有 children，没有 parent、sibling 信息，而 fiber 节点里有，这样就算打断了也可以找到下一个节点继续处理。**fiber 结构就是为实现并发而准备的**。

react18 里同时存在着这两种循环方式，普通的循环和带时间分片的循环。**react 会根据 lane 来判断是否要开启时间分片**。

比如 startTransition、useTransition、useDeferredValue 这些



**并发模式**

并发模式也就是在 workLoop 里通过 shouldYield 的判断来打断渲染，之后把剩下的节点加入 Schedule 调度，来恢复渲染。

一个时间分片是 5ms（默认）， shouldYield  会根据时间判断，每次开始处理时记录时间，如果处理完这个 fiber 节点，时间超了就打断，让出主线程

把不可中断的执行过程变成切片执行，在执行空隙期可以

- 渲染 UI 界面
- 判断是否有高优先级的任务，优先处理
- 及时响应用户操作



#### 优先级机制

**React 使用 Scheduler 根据任务的优先级对任务排序来调度**。高优先级任务（如用户输入）会在低优先级任务（如背景数据更新）之前被处理，确保用户体验的流畅性。

react 中有 2 套优先级体系和 1 套转换体系：

- **Scheduler 优先级**：

```
NoPriority = 0; 
ImmediatePriority = 1; // 离散的事件，如 click，keydown，input
UserBlockingPriority = 2; // 连续的事件，如 scroll, drag, mouseover
NormalPriority = 3;
LowPriority = 4;
IdlePriority = 5; // 空间优先级
```

- **Lane 优先级**：**和 fiber 构造过程相关的优先级**， React 内部有 31 种 Lane 优先级，React 是通过单个 32 位二进制变量的方式来保存不同 Lane 优先级的，方便使用位运算事件优先级。但是调度 Scheduler 任务的时候，会**先将 Lane 优先级转成事件优先级，然后再转成 Scheduler 的 5 种优先级**。

```
export const SyncLanePriority: LanePriority = 15;
export const SyncBatchedLanePriority: LanePriority = 14;

const InputDiscreteHydrationLanePriority: LanePriority = 13;
export const InputDiscreteLanePriority: LanePriority = 12;

// .....

const OffscreenLanePriority: LanePriority = 1;
export const NoLanePriority: LanePriority = 0; // 初始值
```

- **优先级等级（ReactPriorityLevel）**，负责上述 2 套优先级体系的转换

```
export const NoPriority = 0;
export const ImmediatePriority = 1;
export const UserBlockingPriority = 2;
export const NormalPriority = 3;
export const LowPriority = 4;
export const IdlePriority = 5;
```



#### Hook

`Hook`用于`function`组件中, 能够保持`function`组件的状态(与`class`组件中的`state`在性质上是相同的, 都是为了保持组件的状态).在`react@16.8`以后, 官方开始推荐使用`Hook`语法, 常用的 api 有`useState`,`useEffect`,`useCallback`等，这些 api 背后都会创建一个`Hook`对象

```
export type Hook = {|
  memoizedState: any, //内存状态, 用于输出成最终的fiber树
  baseState: any,
  baseQueue: Update<any, any> | null,
  queue: UpdateQueue<any, any> | null,
  next: Hook | null, // 指向该function组件的下一个Hook对象, 使得多个Hook之间也构成了一个链表
|};

type Update<S, A> = {|
  lane: Lane,
  action: A,
  eagerReducer: ((S, A) => S) | null,
  eagerState: S | null,
  next: Update<S, A>,
  priority?: ReactPriorityLevel,
|};

type UpdateQueue<S, A> = {|
  pending: Update<S, A> | null,
  dispatch: ((A) => mixed) | null,
  lastRenderedReducer: ((S, A) => S) | null,
  lastRenderedState: S | null,
|};
```

**Hook 和 fiber 的关系：**

在`fiber`对象中有一个属性`fiber.memoizedState`指向`fiber`节点的内存状态. 在`function`类型的组件中, `fiber.memoizedState`就指向`Hook`队列(`Hook`队列保存了`function`类型的组件状态)
