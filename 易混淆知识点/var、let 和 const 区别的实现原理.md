# var、let 和 const 区别的实现原理

> 变量生命周期：声明（作用域注册一个变量）、初始化（分配内存，初始化为undefined）、赋值

- var：遇到有var的作用域，**在任何语句执行前都已经完成了声明和初始化**，也就是变量提升而且拿到undefined的原因由来
- function： 声明、初始化、赋值一开始就全部完成，所以函数的变量提升优先级更高
- let：解析器进入一个块级作用域，发现let关键字，变量只是先完成**声明**，并没有到**初始化**那一步。此时如果在此作用域提前访问，则报错xx is not defined，这就是暂时性死区的由来。等到解析到有let那一行的时候，才会进入**初始化**阶段。如果let的那一行是赋值操作，则初始化和赋值同时进行
- const、class都是同let一样的道理