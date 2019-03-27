## Generator

**生成器**对象是由一个 [generator function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*) 返回的

```
function* gen() { 
  yield 1;
  yield 2;
  yield 3;
  return 4
}
```

Generator 函数是一个状态机，封装了多个内部状态，返回的遍历器对象（一个指向内部状态的指针对象），可以依次遍历 Generator 函数内部的每一个状态。

调用遍历器对象的`next`方法，使得指针移向下一个状态，`next`方法返回一个有着`value`和`done`两个属性的对象，`value`是yield产生的的值。

``yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作**上一个`yield`表达式的返回值**。所以在第一次使用`next`方法时，传递参数是无效的。

=>Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。

通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。也就是说，可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。

Generator 函数可以不用`yield`表达式，这时就变成了一个单纯的暂缓执行函数。