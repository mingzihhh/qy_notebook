## Symbol

Symbol的目的就是为了实现一个唯一不重复不可变的值，任何一个Symbol都是唯一的，不会和其他任何Symbol相等。

防止命名冲突

```
var s = Symbol.for("description")；//全局
var s = Symbol("description");//局部
```

创建 Symbol 时传入的一个字符串只是对 Symbol 的一个说明，与symbol本身无关，方便调试

