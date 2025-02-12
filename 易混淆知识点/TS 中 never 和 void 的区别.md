void： 表示函数没有返回值，或者说函数返回的是 undefined。 当一个函数没有显式指定返回值类型时，它的返回类型默认为 void。 不能对 void 类型的变量赋予除 undefined 以外的值。

never：表示函数永远不会正常返回，或者说函数会抛出异常或无限循环。 通常 never 类型用于表示永远不会执行完的函数或抛出异常的函数，或者在类型系统中表示不可能发生的情况。 可以将 never 类型赋值给任何其他类型，但是反过来不行。

| 特性     | `void`                             | `never`                          |
| -------- | ---------------------------------- | -------------------------------- |
| 定义     | 表示没有返回值                     | 表示不会完成或永远不会返回       |
| 用途     | 用于指示函数正常结束但不返回任何值 | 用于指示函数抛出异常或无限循环   |
| 可用场景 | 用于普通函数的返回类型             | 用于错误处理或特殊场景           |
| 类型特征 | 可以被视为返回 `undefined`         | 不能被赋值，表示一种不可能的状态 |