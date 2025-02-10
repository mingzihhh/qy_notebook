# Object.defineProperty

直接在一个对象上定义一个新的属性，或者是修改已存在的属性。最终这个方法会返回该对象。

Object.defineProperty(object, propertyname, descriptor)

- **object** 必需。 要在其上添加或修改属性的对象。 这可能是一个本机 JavaScript对象（即用户定义的对象或内置对象）或 DOM 对象。

- **propertyname** 必需。 一个包含属性名称的字符串。

- **descriptor** 必需。 属性描述符。 它可以针对数据属性或访问器属性。

   value,   configurable， enumerable， writable

  在调用Object.defineProperty()方法时，如果不指定， configurable， enumerable， writable特性的默认值都是false

  

我们可以通过使用Object.defineProperty，来定义和控制一些**特殊的属性**，如属性是否可读，属性是否可枚举，甚至修改属性的修改器（setter）和获取器(getter)

- MVVM中数据‘双向绑定’实现
- 优化对象获取和修改属性方式 (设置属性的set方法)
- 增加属性获取和修改时的信息 (设置属性的get方法)