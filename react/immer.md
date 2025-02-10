## immer

https://github.com/bpuns/immer-study

https://juejin.cn/post/6926099651510665230

https://juejin.cn/post/6844904176946774030?from=search-suggest

**动机**：简化对复杂对象（嵌套很深）的修改过程

```
<input
  value={user.address.geo.lat}
  onChange={e => {
    const newUser = produce(user, draft => {
      draft.address.geo.lat = e.target.value;
    });
    setUser(newUser);
  }}
/>

```

**优点：**

- 深度更新轻而易举
- 开箱即用的结构共享
- 开箱即用的对象冻结
- 代码简洁
- 小巧：3KB gzip

**和深拷贝对比**：

避免了对无关属性的修改（如果用深拷贝两次对象完全不同，内容不变内存地址改变，会导致其他依赖其他属性的组件再次渲染），实现**状态共享(structurally sharing the things that weren't changed)**

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/49d2900926414e61acfeba3ad383731d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

**原理**：

核心 API

```
produce(currentState, producer: (draftState) => void): nextState
```

- currentState：被操作对象的最初状态

- draftState：根据 currentState 生成的草稿状态，它是 currentState 的代理，对 draftState 所做的任何修改都将被记录并用于生成 nextState 。在此过程中，currentState 将不受影响

- nextState：根据 draftState 生成的最终状态

  

使用 proxy 对读写操作进行代理

- 仅在写时拷贝 Copy-on-write
- 读操作被代理到了原值上
- 写操作被代理到了拷贝值上
- 如果是多层对象需要多次代理，因为 proxy 只能监听到当前层的属性访问

```
function immer(state, thunk) {
  let copies = new Map(); // Map 的 key 可以是一个对象，非常适合用来缓存被修改的对象

  const handler = {
    get(target, prop) { // 增加一个 get 的劫持，返回一个 Proxy
      return new Proxy(target[prop], handler);
    },
    set(target, prop, value) {
      const copy = {...target}; // 浅拷贝
      copy[prop] = value; // 给拷贝对象赋值
      copies.set(target, copy);
    }
  };

  function finalize(state) { // 增加一个 finalize 函数
    const result = {...state};
    Object.keys(state).map(key => { // 以此遍历 state 的 key
      const copy = copies.get(state[key]);
      if(copy) { // 如果有 copy 表示被修改过
        result[key] = copy; // 就是用修改后的内容
      } else {
        result[key] = state[key]; // 否则还是保留原来的内容
      }
    });
    return result;
  }

  const proxy = new Proxy(state, handler);
  thunk(proxy);
  return finalize(state);
}

const state = {
  "phone": "1-770-736-8031 x56442",
  "website": {site: "hildegard.org"}, // 注意这里为了方便测试状态共享，将简单数据类型改成了对象
  "company": {
    "name": "Romaguera-Crona",
    "catchPhrase": "Multi-layered client-server neural-net",
    "bs": "harness real-time e-markets"
  }
};

const copy = immer(state, draft => {
  draft.company.name = 'google';
});

console.log(copy.company.name); // 'google'
console.log(copy.website === state.website); // true

```

实际实现中，immer 采用`Proxy.revocable()`，能够解除代理关系，更安全些



#### 使用 useImmer 替代 useState 优化 React 渲染

```
import { FC, useState, useEffect } from 'react';

const App: FC = () => {
  const [list, setList] = useState({ a: 1 });

  useEffect(() => {
    setList({ a: 1 });
  }, []);

	// 会输出两次，list是引用对象，虽然内部的值相同，但引用地址变化了，因此react认为state发生了变化，因此触发了渲染
  console.log('测试'); 

  return (
    <>
      <h1>Hello Web3 React {list.a}</h1>
    </>
  );
};

export default App;



import { FC, useEffect } from 'react';
import { useImmer } from '@hooks/useImmer';

const App: FC = () => {
  const [list, setList] = useImmer({ a: 1 });

  useEffect(() => {
    // setList({ a: 1 }); // 只输出一次，避免了无意义渲染
    setList(draft => {
      draft.a = 1;
    });
  }, []);

  console.log('测试');

  return (
    <>
      <h1>Hello Web3 React {list.a}</h1>
    </>
  );
};

export default App;
```

**实现 useImmer**

```
import { useCallback, useState } from 'react';
import { produce, Draft, freeze } from 'immer';

export type DraftFunction<S> = (draft: Draft<S>) => void;
export type Updater<S> = (arg: S | DraftFunction<S>) => void;
export type ImmerHook<S> = [S, Updater<S>];
export function useImmer<S = any>(initialState: S | (() => S)): ImmerHook<S>;

export function useImmer<T>(initialState: T) {
	const [value, updateValue] = useState(
		// 冻结 state，第二参数 true，表示深度冻结，对象不能修改了
		freeze(typeof initialState === 'function' ? initialState() : initialState, true)
	);
	return [
		value,
		useCallback((updater: Updater<T>) => {
			if (typeof updater === 'function') {
				updateValue(produce(updater));
			} else {
				updateValue(freeze(updater));
			}
		})
	]
}
```

