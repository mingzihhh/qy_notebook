## void 0  和 undefined

void 运算符 对给定的表达式进行求值，然后返回 undefined

undefined是个变量，是js原始类型值之一，也是全局对象window的属性，在部分低级别的浏览器中可以被修改，在局部作用域中也可以被修改。

1. 防止undefined被局部重写，影响判断，用void 0来代替undefined

2. ```<a href="javascript:void(0)">xxxx</a>```

   填充a标签的href，用#填充会滚回顶部

Undefined 跟 null 有一定的表意差别，null 表示的是：“定义了但是为空”，是JS关键字，在任何代码中，你都可以放心用 null 关键字来获取 null