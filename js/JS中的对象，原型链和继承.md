## JS中的对象，原型链和继承

JS 中的对象是一系列无序 key: value 的集合；函数也是 js 对象的一种

面向对象的思路：把某个功能看成一个整体（对象），通过调用对象的某个方法来启动功能。在用的时候不去考虑这个对象内部的实现细节，在去实现这个对象细节的时候不用管谁在调用

通过创建一个函数来实现自动创建对象的过程，至于个性化通过参数实现，开发者不必关注细节，只需要传入指定参数即可

![img](https://p3-juejin-sign.byteimg.com/tos-cn-i-k3u1fbpfcp/9a541d861ecc4399b03d20c9e5de96ea~tplv-k3u1fbpfcp-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgZGI0NnJ0MDBvcnM=:q75.awebp?rk3s=436ef1ef&x-expires=1729332142&x-signature=OalGmp7ot6CI49pH%2BlaGq%2BYpmnQ%3D)

#### new函数的本质

new obj()的过程

1. 创建一个空对象{}，假设名字是 tmpObj。这一步将空对象的proto属性设置为obj.prototype
2. 初始化对象。函数obj被传入参数并调用，关键字this被设定为该tmpObj
3. 函数执行完后返回刚刚创建的 tmpObj

使用 new 关键字，那么可以少做四件事情：

1. 不用创建临时对象，因为 new 会帮你做（**你使用「this」就可以访问到临时对象**）；
2. 不用绑定原型，因为 new 会帮你做（new 为了知道原型在哪，所以指定原型的名字为 prototype）；
3. 不用 return 临时对象，因为 new 会帮你做；
4. 不要给原型想名字了，因为 new 指定名字为 prototype。

**instanceof**  是一个操作符，可以判断**对象**是否为某个类型的实例，内部机制是通过原型链来判断的：

从检查p.proto === P.prototype开始，一直向上找，某层相等则成立

#### 构造函数

- 任何函数使用new表达式就是构造函数
- 每个**函数**都自动添加一个名称为`prototype`属性，这是一个对象
- 每个**对象**都有一个内部属性 `__proto__`(规范中没有指定这个名称，但是浏览器都这么实现的) 指向其类型的prototype属性，类的实例也是对象，其**__proto__**属性指向“类”的prototype，意味着类的prototype对象可以作为一个公共容器，供所有实例访问。
- 所有实例都会通过原型链引用到类型的prototype，prototype相当于特定类型所有实例都可以访问到的一个公共容器，重复的东西移动到公共容器里放一份就可以了



**prototype和proto的区别**：

prototype是函数的属性，proto是用一个函数去创建对象的时候对象的属性



#### 原型链

原型链就是多个对象通过 `__proto__` 的方式连接了起来， `__proto__` __指向一些公共属性的内存地址，即原型，记为prototype

任何类的`prototype`属性本质上都是个类`Object`的实例，所以`prototype`也和其它实例一样也有个`__proto__`内部属性，指向其类型`Object`的prototype

`Object` 是所有对象的爸爸，所有对象都可以通过 `__proto__` 找到它，Object的**proto**中放的是Null

注意： **不是所有对象都有原型**，Object.create(null) 创建的对象没有原型，常用于需要纯净、无污染对象的场景，比如构建某些特定的数据结构或实现特定的安全需求。



#### 继承

继承是指一个对象直接使用另一对象的属性和方法。

JavaScript并不提供原生的继承机制，需要自己实现

1. 得到一个类的属性

   对象属性的获取是通过**构造函数的执行**，我们在一个类中执行另外一个类的构造函数，就可以把属性赋值到自己内部，但是我们需要把环境改到自己的作用域内，这就要借助我们讲过的函数`call`了

2. 得到一个类的方法

   我们知道类的方法都定义在了prototype里面，所以只要我们**把子类的prototype改为父类的prototype的备份**就好了

   这里我们通过`Object.create`(ES5) clone了一个新的prototype，_prototype.proto = superType.prototype

   **Object.create()**方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。 

   ```
   function inherit(superType, subType){
       var _prototype  = Object.create(superType.prototype);
       //非ES5，var _prototype  = new superType()
       _prototype.constructor = subType;
       subType.prototype = _prototype;
   }
   ```

   ##### hasOwnProperty

   `hasOwnPerperty`是Object.prototype的一个方法，可以判断一个对象是否包含自定义属性而不是原型链上的属性，`hasOwnProperty`是JavaScript中唯一一个处理属性但是不查找原型链的函数

   可以用来判断某个属性是自己的还是父类的