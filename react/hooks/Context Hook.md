### useContext()

读取订阅上下文，上下文帮助组件从祖先组件接收信息，而无需将其作为 props 传递。例如，应用程序的顶层组件可以借助上下文将 UI 主题传递给所有下方的组件，无论这些组件层级有多深。

`useContext()` 总是在调用它的组件 **上面** 寻找最近的 provider， **不考虑** 调用 `useContext()` 的组件中的 provider。

**createConext + Conext.Provider + useContext**

```
import { createContext, useContext, useState } from 'react';

// 创建 context
const ThemeContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('light');
  return (
   // 从 provider 接收到不同的 value 开始，React 自动重新渲染使用了该特定 context 的所有子级。
   // 先前的值和新的值会使用 Object.is 来做比较。使用 memo 来跳过重新渲染并不妨碍子级接收到新的 context 值。
    <ThemeContext.Provider value={theme}>
      <Form />
      <label>
        <input
          type="checkbox"
          checked={theme === 'dark'}
          onChange={(e) => {
            setTheme(e.target.checked ? 'dark' : 'light')
          }}
        />
        Use dark mode
      </label>
    </ThemeContext.Provider>
  )
}

function Form({ children }) {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children }) {
	// 传入先前用 createContext 创建的 context。context 本身不包含信息，它只代表你可以提供或从组件中读取的信息类型。
	// 被确定为传递给树中调用组件上方最近的 ThemeContext.Provider 的 value。
	// 如果没有这样的 provider，那么返回值将会是为创建该 context 传递给 createContext 的 defaultValue。返回的值始终是最新的。如果 context 发生变化，React 会自动重新渲染读取 context 的组件。
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
      {children}
    </button>
  );
}

```



##### Context 有什么缺点，怎么改进？

Context 因为是整个大对象，只要数据变更，所有内容有可能都会需要 rerender。

1. 细化 context value，拆分，PageContext、WorkSpaceContext
2. 通过定义 **xxxProvider**，将数据更新局限在该 provider 的 children 层，不再是 PageContext.Provider，而是 PageProvider；外部的组件不会订阅 `PageContext` 中的变化，从而减少无关组件的渲染开销。

```
import React, { createContext, useState, useContext } from 'react';

// 创建 PageContext
const PageContext = createContext();

export function PageProvider({ children }) {
    const [user, setUser] = useState(null);
    const [theme, setTheme] = useState('light');

    return (
        <PageContext.Provider value={{ user, setUser, theme, setTheme }}>
            {children}
        </PageContext.Provider>
    );
}

// 自定义 Hook
export function usePageContext() {
    return useContext(PageContext);
}

```



#### 使用 useMemo 和 useCallback 优化重新渲染

```
function MyApp() {
  const [currentUser, setCurrentUser] = useState(null);

  function login(response) {
    storeCredentials(response.credentials);
    setCurrentUser(response.user);
  }

  return (
    <AuthContext.Provider value={{ currentUser, login }}>
      <Page />
    </AuthContext.Provider>
  );
}
```

这里的 context value 是一个具有两个属性的 JavaScript 对象，其中一个是函数。每当 `MyApp` 出现重新渲染（例如，路由更新）时，这里将会是一个 **不同的** 对象指向 **不同的** 函数，因此 React 还必须重新渲染树中调用 `useContext(AuthContext)` 的所有组件。

但是，如果基础数据如 `currentUser` 没有更改，则不需要重新渲染它们。为了帮助 React 利用这一点，你可以使用 [`useCallback`](https://zh-hans.react.dev/reference/react/useCallback) 包装 `login` 函数，并将对象创建包装到 [`useMemo`](https://zh-hans.react.dev/reference/react/useMemo) 中。

```
function myApp() {
	const [currentUser, setCurrentUser] = useState(null);
	
	const login = useCallback((response) => {
	    storeCredentials(response.credentials);
    	setCurrentUser(response.user);
	}, []);
	
	const contextValue = useMemo(() => {
		currentValue,
		login,
	}, [currentValue, login])
  return (
    <AuthContext.Provider value={contextValue}>
      <Page />
    </AuthContext.Provider>
  );
}
```



