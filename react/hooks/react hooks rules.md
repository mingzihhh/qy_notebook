## react hooks rules

https://zh-hans.react.dev/reference/rules

React Hooks 的设计目的，允许你在不编写类组件的情况下使用状态（state）和其他 React 特性（比如生命周期方法），够让函数组件变得更简洁、更直观，并且可以通过自定义 Hooks 轻松复用代码逻辑。



React 中的一个核心概念是**保持纯粹**。一个纯组件或 Hook 应该是：

- **幂等的**：每次使用相同的输入（组件输入的 props、state、context 以及 Hook 输入的参数）总是得到相同的结果
- **在渲染中没有副作用** ：具有副作用的代码应该与渲染过程分开
  - 一个快速判断代码是否在渲染过程中运行的方法是检查代码书写的位置：写在顶层的代码很可能会在渲染过程中运行，事件处理函数和 Effect 在渲染过程中不会运行

- **不要修改非局部作用域中的值**：组件和 Hook 在渲染时中不应该修改非局部创建的值（不能修改 props / 直接修改 state）



在 React 中，以 `use` 开头命名的函数被称为 **[Hook](https://zh-hans.react.dev/reference/react)**。，但是使用它们会有两个额外的规则：

- 只能在**顶层**调用 Hook。不要在循环、条件判断、嵌套函数或 try/catch/finally 代码块中调用。
- 只能在 **React 的函数组件**或者**自定义的 Hook** 中调用 Hook 





