## Vue组件中的data为什么必须是函数

`vue`的自定义组件中，data必须要是一个函数，并且返回一个对象，这样每个组件就能够有自己独立的data属性。

与JS中的原型链和继承机制有关。

在Vue中，data是的值是一个对象，不同的是这个对象的生成方式。如下：

1、方式一，在Vue实例中的data

```
data: { a: 1,b: 2 }

var vueComponent = function() {}
vueComponent.prototype.data = {
  a: 1,
  b: 2
}

```

2、方式二，在组件中的data

```
data: function(){
    return { a: 1,b: 2 }
}

var MyComponent = function() {
  this.data = this.data()
}
MyComponent.prototype.data = function() {
  return {
    a: 1,
    b: 2,
  }
}
```

我们知道，函数返回值返回一个对象时，每次执行函数都会创建一个新的对象。

所以，

方式一的data始终指向固定的对象，所以组件在调用时候公用一个数据a和数据b；

而方式二，组件执行时候data每次都创建新的对象，这个对象里面有数据a和数据b。假如组件被复用了十次，则data里面的对象就被创建了十次，我们更新一个组件的a或b的值，并不会影响其他组件的a和b。



