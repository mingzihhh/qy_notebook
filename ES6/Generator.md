## Generator

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用`yield`语句注明。



**生成器**对象是由一个 [generator function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*) 返回的

```
function* gen() { 
  yield 1;
  yield 2;
  yield 3;
  return 4
}
```

Generator 函数是一个**状态机**，封装了多个内部状态，返回的**遍历器对象**（一个指向内部状态的指针对象），可以依次遍历 Generator 函数内部的每一个状态，返回一系列的值。

Generator 函数是**分段执行**的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。

调用遍历器对象的`next`方法，使得指针移向下一个状态，`next`方法返回一个有着`value`和`done`两个属性的对象，`value`是yield产生的的值。

```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();//不执行，返回遍历器对象

hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```



=>Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。

### yield

Generator 函数可以不用`yield`表达式，这时就变成了一个单纯的暂缓执行函数，调用next方法时才会执行。

yield 只能用在generator函数中，forEach等普通函数也不行

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作**上一个`yield`表达式的返回值**。所以在第一次使用`next`方法时，传递参数是无效的。

通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。也就是说，可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。

yield 表达式和 return

- 相似：返回紧跟在语句后面的那个表达式的值
- 区别：
  - 每次遇到`yield`，函数暂停执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能。
  - 一次return，多次yield

