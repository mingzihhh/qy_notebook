## class

```
class Point {
  constructor(x, y) {//构造方法,this关键字则代表实例对象
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
typeof Point // "function"
Point === Point.prototype.constructor // true

// 等同于

Point.prototype = {
  constructor() {},
  toString() {}
};
```

- 不需要function和逗号分隔

- `constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

- 类必须使用`new`调用，否则会报错。

- 在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为

  ```javascript
  class MyClass {
    constructor() {
      // ...
    }
    get prop() {
      return 'getter';
    }
    set prop(value) {
      console.log('setter: '+value);
    }
  }
  
  let inst = new MyClass();
  
  inst.prop = 123;
  // setter: 123
  
  inst.prop
  // 'getter'
  ```

- static :不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

  父类的静态方法，可以被子类继承，被子类调用，被super类调用

- 私有方法和私有属性

  只能在类的内部访问的方法和属性，外部不能访问。有利于数据封装。

  - 在命名上加以区别 _bar(){}
  - 将私有方法移出模块，内部调用 bar.call(this)
  - 利用Symbol的唯一性，，将私有方法的名字命名为一个`Symbol`值
  - 引入#标记私有属性