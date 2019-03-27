## JS基础知识点1

#### 原始类型

boolean,null,undefined,string,number,symbol

- 原始类型存储的都是值，是没有函数可以调用的
- 在必要的情况下强转类型       '1'.toString()
- number是浮点类型，在使用中会遇到某些 Bug，比如 `0.1 + 0.2 !== 0.3`
- string类型的值不可变
- `typeof null` 会输出 `object`

------



#### 对象类型

**对象类型和原始类型的不同之处？**

对象类型和原始类型不同的是，原始类型存储的是值，对象类型存储的是地址（指针）。



**函数参数是对象会发生什么问题？**

- 函数传参是传递对象指针的副本
- 函数内部修改参数的属性，原来对象的值也会改变
- 函数内部分配参数新的对象，参数拥有新的地址，与原来对象没有关系了

-------



#### typeof vs instanceof

**typeof 是否能正确判断类型？**

`typeof` 对于原始类型来说，除了 `null` 都可以显示正确的类型

`typeof` 对于对象来说，除了函数都会显示 `object`     (不能判断具体判断数组等)

所以说 `typeof` 并不能准确判断变量到底是什么类型



**instanceof 能正确判断对象的原理是什么？**

如果我们想判断一个对象的正确类型，这时候可以考虑使用 `instanceof`，因为内部机制是通过原型链来判断的

对于原始类型来说，你想直接通过 `instanceof` 来判断类型是不行的，需要借助 `Symbol.hasInstance` ，一个能让我们自定义 `instanceof` 行为的东西

?????

```
class PrimitiveString {
  static [Symbol.hasInstance](x) {
    return typeof x === 'string'
  }
}
console.log('hello world' instanceof PrimitiveString) // true
```



#### 类型转换

在 JS 中类型转换只有三种情况，分别是：

- 转换为布尔值

- 转换为数字

- 转换为字符串

  ![img](https://user-gold-cdn.xitu.io/2018/11/15/16716dec14421e47?imageslim)

----



#### this

**如何正确判断 this？**

- 对于直接调用 `foo` 来说，不管 `foo` 函数被放在了什么地方，`this` 一定是 `window`
- 对于 `obj.foo()` 来说，我们只需要记住，谁调用了函数，谁就是 `this`，所以在这个场景下 `foo`函数中的 `this` 就是 `obj` 对象
- 对于 `new` 的方式来说，`this` 被永远绑定在了 `c` 上面，不会被任何方式改变 `this`
- 对于 `bind` 这些改变上下文的 API 了，对于这些函数来说，`this` 取决于第一个参数，如果第一个参数为空，那么就是 `window`；不管我们给函数 `bind` 几次，`fn` 中的 `this` 永远由第一次 `bind` 决定
- 规则优先级：`new` 的方式优先级最高，接下来是 `bind` 这些函数，然后是 `obj.foo()` 这种调用方式，最后是 `foo` 这种调用方式，同时，箭头函数的 `this` 一旦被绑定，就不会再被任何方式所改变。



**箭头函数的 this 是什么？**

- 箭头函数其实是没有 `this` 的，箭头函数中的 `this` 只取决包裹箭头函数的第一个普通函数的 `this`。
- 如果箭头函数外部没有包裹函数，此时箭头函数中的this指向window
- 对箭头函数使用 `bind` 这类函数是无效的



##### apply,bind, call 三个函数的区别

bind会返会一个改变了this指向的函数，所以foo.bind(a)后面要加一个()来调用

call是函数调用，调用的同时指定this的值，直接返回执行后的结果

apply和call作用完全一样，都是调用函数的同时指定this，只不过传参的形式不一样，如：apply(this, [arg1, arg2])、call(this, arg1, arg2)；



![img](https://user-gold-cdn.xitu.io/2018/11/15/16717eaf3383aae8?imageslim)