### Object.assign()

将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

>```
>Object.assign(target, ...sources)
>```

- 浅拷贝
- 会发生属性覆盖
- 会调用 getter 和 setter

### Object.create()

创建一个新对象，使用现有的对象来提供新创建的对象的 __proto__。 

