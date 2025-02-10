## 目标

实现一个`useInterval(callback)` 的自定义hook，每隔一秒调用一次 `callback`

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter/index.js
function Counter() {
  const [count, setCount] = useState(0);
  const callback = () => setCount(count + 1);

  useInterval(callback);

  return <h1>{count}</h1>;

}
```

## Hooks概览

我们只介绍当前分享涉及到的hooks。https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Hook/demo1.js

## 第一次尝试

现在我想要一个每秒增加的 interval，它需要清理副作用，所以我将用到 `useEffect()` 并返回清理函数。

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter1/index.js
function Counter() {
  const [count, setCount] = useState(0)
  
  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);

  });

  return <h1>{count}</h1>;

}
```

看上去好像成功了，count会每秒加1。让我们看一下下面这个例子👇👇👇，在 `Counter` 组件中再新增一个state----`count1`。

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter1/demo1.js
function Counter() {
  const [count, setCount] = useState(0);
  const [count1, setCount1] = useState(0);
  
  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  });

  return (
    <>
      <h1>{count}</h1>
      <h1 onClick={() => setCount1(count1 + 1)}>{count1}</h1>
    </>
  );

}
```

连续点击修改count1时，count就不会定时新增了。这是因为**每次修改count1，useEffect都会执行一次，setInterval还没执行，就被注销了**。



## 第二次尝试

仔细想想，我们好像只需要 `useEffect()` 在组件mounted的时候执行就可以了。

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter2/index.js
function Counter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>;

}
```

`count` 变成1后，直接不变了！为什么？闭包，由于 `setInterval` 本身是在组件初次渲染时创建的，内部并没有自动更新最新的 `count` 值，每次调用 `setCount(count + 1)` 时，它依然使用的是创建 `useEffect` 时捕获的那个旧的 `count` 值（0）。因此，`setCount(0 + 1)` 永远只会将 `count` 增加到 `1`。

说到闭包，来看一个有趣的[例子](https://codesandbox.io/s/intelligent-mayer-58rb4 )。`class` 和 `function` 是不同的，`class` 可以通过 `this` 获取到当前最新的值，但 `function` 的每次运行都会保存其作用域内的值。

这里的解决方案有很多种：

1、将 `[count]` 作为 `useEffect()` 的第二个参数，每次 `count` 更新时，旧的定时器（`setInterval`）被清除，然后重新创建一个新的定时器，新的定时器会使用当前最新的 `count` 值（而不是旧的值），并在 1 秒后递增该值，**不推荐**

```JavaScript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, [count]);

  return <h1>{count}</h1>;
}
```

2、使用 `useRef()`来保持对最新 `count` 值的引用

```JavaScript
function Counter() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);

  useEffect(() => {
    countRef.current = count;
  }, [count]);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(countRef.current + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  
  return <h1>{count}</h1>;
}
```

3、`setCount()` 的参数使用函数 `count => count + 1`，这种回调函数的方式，确保能获取到最新的状态值进行更新。

```JavaScript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count => count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>;
}
```



**言归正传，如何实现一个`useInterval` 的自定义hook？**

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter2/demo1.js

const useInterval = (callback) => {
  useEffect(() => {
    const id = setInterval(callback, 1000);
    return () => clearInterval(id);
  }, []);
}

function Counter() {
  const [count, setCount] = useState(0);
  const callback = () => {
    setCount(count => count + 1);
  }
  useInterval(callback);
  
  return <h1>{count}</h1>;

}
```

在这个例子中，`useInterval` 已经可以正常运行了。但是 `callback` 可能并不是总是一成不变的。我们新增一个state， 用来控制每次递增的步长，来模拟 `callback` 发生变化。来看一下我们的hook是否可以正常运行。

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter2/demo2.js
const useInterval = (callback) => {
  useEffect(() => {
    const id = setInterval(callback, 1000);
    return () => clearInterval(id);
  }, []);
};

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);
  const callback = () => {
    setCount((count) => count + step);
  };
  useInterval(callback);
  
  return (
    <>
      <h1>{count}</h1>
      <h1 onClick={() => setStep(step + 1)}>step: {step}</h1>
    </>

  );

}
```

怎么修改步长没有用啊，每次还是增加1 => 应该在 `useEffect()` 添加 `callback` 这个依赖。

```JavaScript
const useInterval = (callback) => {
  useEffect(() => {
    const id = setInterval(callback, 1000);
    return () => clearInterval(id);
  }, [callback]);

};

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);
  const callback = () => {
    setCount((count) => count + step);
  };
  useInterval(callback);

  return (
    <>
      <h1>{count}</h1>
      <h1 onClick={() => setStep(step + 1)}>step: {step}</h1>
    </>
  );

}
```

然而，第一次尝试遇到的问题又出现了。。。

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter2/demo3.js

const useInterval = (callback) => {
  useEffect(() => {
    const id = setInterval(callback, 1000);
    return () => clearInterval(id);
  }, [callback]);
};

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);
  const [count1, setCount1] = useState(0);

  const callback = () => {
    setCount((count) => count + step);

  };
  useInterval(callback);

  return (
    <>
      <h1>{count}</h1>
      <h1 onClick={() => setStep(step + 1)}>step: {step}</h1>
      <h1 onClick={() => setCount1(count1 + 1)}>count1: {count1}</h1>
    </>

  );

}
```

为什么呢？每次render的时候， `callback` 都是一个新的函数，所以 `useEffect()` 都要重新执行。使用 `useCallback()` 不就可以解决了，让只有 `step` 发生变化后，`callback` 才变化。

```JavaScript
const useInterval = (callback) => {
  useEffect(() => {
    const id = setInterval(callback, 1000);
    return () => clearInterval(id);
  }, [callback]);
};

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);
  const [count1, setCount1] = useState(0);

  const callback = useCallback(() => {
    setCount((count) => count + step);
  }, [step]);
  useInterval(callback);

  return (
    <>
      <h1>{count}</h1>
      <h1 onClick={() => setStep(step + 1)}>step: {step}</h1>
      <h1 onClick={() => setCount1(count1 + 1)}>count1: {count1}</h1>
    </>

  );

}
```

额。。。。又陷入了沉思，难道我要在 `useInterval` 注明，参数 `callback` 要使用 `useCallback()` 嵌套吗？

## 第三次尝试

总结一下上面遇到的问题，我们只需要在组件mount后调用 `useEffect()`，但是我们要获取每次最新的 `callback` 值。这说的不就是 `useRef()`。

```JavaScript
// https://codesandbox.io/s/snowy-https-h5gp7?file=/src/Counter3/index.js
const useInterval = (callback) => {
  const savedCallback = useRef();
  useEffect(() => {
    savedCallback.current = callback;
  });
  useEffect(() => {
    const tick = () => {
      savedCallback.current();
    };
    const id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, []);
};

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);
  const [count1, setCount1] = useState(0);

  const callback = () => {
    setCount((count) => count + step);
  };
  useInterval(callback);

  return (
    <>
      <h1>{count}</h1>
      <h1 onClick={() => setStep(step + 1)}>step: {step}</h1>
      <h1 onClick={() => setCount1(count1 + 1)}>count1: {count1}</h1>
    </>
  );
}
```

组件的每次的render都有自己独立的作用域，可以理解为每次都要执行一次函数，所以里面的变量都是全新的。副作用写在`useEffect`中。但`useRef`可以保证`.current`在整个生命周期中都不会发生变化。`useMemo`和`useCallback`可以用来缓存变量和函数。通常在计算开销大，或变量作为依赖时使用。



了解更多useEffect：[A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)

如何使用Hooks请求数据： [How to fetch data with React Hooks?](https://www.robinwieruch.de/react-hooks-fetch-data)

主要参考：[Making setInterval Declarative with React Hooks](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)