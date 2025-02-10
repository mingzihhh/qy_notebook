**引入 hook 的动机**：

在组件之间复用状态逻辑很难; 复杂组件变得难以理解; 难以理解的 class. 为了解决这些实际开发痛点, 引入了`Hook`

**hook 是什么，什么时候会用 hook**：

- Hook  是一个特殊的函数, 它可以让你“钩入” `React` 的特性. 如, `useState` 是允许你在 `React` 函数组件中添加 `state` 的 `Hook`.

- 如果你在编写函数组件并意识到需要向其添加一些 `state`, 以前的做法是必须将其转化为 `class`. 现在你可以在现有的函数组件中使用 `Hook`.



```
export type Hook = {|
  memoizedState: any, // 保持在内存中的局部状态
  baseState: any, // hook.baseQueue 中所有 update 对象合并之后的状态
  baseQueue: Update<any, any> | null, // 存储 update 对象的环形链表, 只包括高于本次渲染优先级的 update 对象
  queue: UpdateQueue<any, any> | null, // 存储 update 对象的环形链表, 包括所有优先级的 update 对象.
  next: Hook | null, // next指针,指向链表中的下一个hook
|};

```

![img](https://7km.top/static/hook-linkedlist.d52c2c25.png)



### Hook 和 fiber 节点的关系

#### 创建 fiber 阶段

在 function 中, 如果使用了`Hook api`(如: `useEffect`, `useState`), 就会通过 `mountWorkInProgressHook` 创建一个与之对应的`Hook`对象，并挂载到`fiber.memoizedState`上, 多个`Hook`以链表结构保存

```
function mountWorkInProgressHook(): Hook {
  const hook: Hook = {
    memoizedState: null,

    baseState: null,
    baseQueue: null,
    queue: null,

    next: null,
  };

  if (workInProgressHook === null) {
    // 链表中首个hook
    currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
  } else {
    // 将hook添加到链表末尾
    workInProgressHook = workInProgressHook.next = hook;
  }
  return workInProgressHook;
}
```

#### 对比更新阶段

- 利用`fiber树`内部的双缓冲技术, 把`current.memoizedState`链表上的所有`Hook`按照顺序克隆到`workInProgress.memoizedState`上，实现了`Hook`从`current`到`workInProgress`转移, 进而实现了`Hook`状态的持久化
- 执行`updateFunctionComponent->renderWithHook`，调用每个 Hook api 时通过 `updateWorkInProgressHook` 获取一个 hook

```
function updateWorkInProgressHook(): Hook {
  // 1. 移动currentHook指针
  let nextCurrentHook: null | Hook;
  if (currentHook === null) {
    const current = currentlyRenderingFiber.alternate;
    if (current !== null) {
      nextCurrentHook = current.memoizedState;
    } else {
      nextCurrentHook = null;
    }
  } else {
    nextCurrentHook = currentHook.next;
  }

  // 2. 移动workInProgressHook指针
  let nextWorkInProgressHook: null | Hook;
  if (workInProgressHook === null) {
    nextWorkInProgressHook = currentlyRenderingFiber.memoizedState;
  } else {
    nextWorkInProgressHook = workInProgressHook.next;
  }

	// 由于renderWithHooks函数设置了workInProgress.memoizedState=null, 所以workInProgressHook初始值必然为null, 只能从currentHook克隆
  if (nextWorkInProgressHook !== null) {
    // 渲染时更新: 本节不讨论
  } else {
     currentHook = nextCurrentHook;
    // 3. 克隆 currentHook 作为新的 workInProgressHook
    // 随后逻辑与 mountWorkInProgressHook 一致
    const newHook: Hook = {
      memoizedState: currentHook.memoizedState,

      baseState: currentHook.baseState,
      baseQueue: currentHook.baseQueue,
      queue: currentHook.queue,

      next: null, // 注意next指针是null，workInProgressHook链表需要完全重建
    };
    if (workInProgressHook === null) {
      currentlyRenderingFiber.memoizedState = workInProgressHook = newHook;
    } else {
      workInProgressHook = workInProgressHook.next = newHook;
    }
  }
  return workInProgressHook;
}
```





### 状态 hook

https://7km.top/main/hook-state



### 副作用 hook

在 React 内部, 依靠`fiber.flags`和`effect.tag`实现了对`effect`的精准识别. 在`commitRoot`阶段, 对不同类型的`effect`进行处理, 先后调用`effect.destroy()`和`effect.create()`

https://7km.top/main/hook-effect



### Context 原理

- 创建状态时
  - 初始值保存在`context._currentValue`(同时保存到`context._currentValue2`. 英文注释已经解释, 保存 2 个 value 是为了支持多个渲染器并发渲染)
  - 同时创建了`context.Provider`, `context.Consumer`2 个`reactElement`对象

- 在消费状态时
  - `ContextConsumer`节点调用 `readContext(MyContext)` 获取最新状态
  - `readContext` 还构造了一个 `contextItem` 添加到 `workInProgress.dependencies` 链表之后, 其中`contextItem.context`保存了当前`context`的引用。这个`dependencies`属性会在更新时使用, 用于判定是否依赖了`ContextProvider`中的值
- 在更新状态时
  - 由 ContextProvider 节点开始，向下遍历，查找所有`fiber.dependencies`依赖该`context`的节点（consumer） 
  - 从 consumer 节点开始，向上遍历，设置父路径上所有节点的 `fiber.childLanes`,表明其父节点有变动，子节点会进入更新逻辑

https://7km.top/main/context

