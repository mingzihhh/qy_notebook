## async

 Generator 函数的语法糖

`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已。

`async`函数对 Generator 函数的改进，体现在以下四点：

- 内置执行器，执行只需要一行

- 更好的语义

- 更广的适用性

- 返回值是 Promise

  Generator 函数的返回值是 Iterator 对象