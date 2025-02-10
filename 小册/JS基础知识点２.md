## JS基础知识点２

#### == vs ===

**== 和 === 有什么区别？**

对于 `==` 来说，如果对比双方的类型**不一样**的话，就会进行**类型转换**

对于 `===` 来说，就是判断两者类型和值是否相同。

判断ｘ＝＝ｙ的流程：

https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.1

![img](https://user-gold-cdn.xitu.io/2018/12/19/167c4a2627fe55f1?imageslim)

---



#### 闭包

**什么是闭包**？

函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。

在 JS 中，闭包存在的意义就是让我们可以间接访问函数内部的变量。

**经典面试题，循环中使用闭包解决 `var` 定义函数的问题**

**for** (**var** i = 1; i <= 5; i++) {
setTimeout(**function** **timer**() {
console.log(i)
}, i * 1000)
}

解决方法：闭包／setTimeout的第三个参数／let



---



#### 深浅拷贝

**什么是浅拷贝？如何实现浅拷贝？什么是深拷贝？如何实现深拷贝？**

首先深浅拷贝只针对像 Object, Array 这样的复杂对象

对于对象来说

- **浅拷贝是对对象地址的拷贝**，并没有开辟新的栈，也就是拷贝的结果是两个对象指向同一个地址，修改其中一个对象的属性，则另一个对象的属性也会改变
- **深拷贝则是开辟新的栈**，原对象的各个属性逐个复制出去，而且将原对象各个属性所包含的对象也依次采用深拷贝的方法**递归复制**到新对象上。修改一个对象的属性，不会改变另一个对象的属性。

实现

- 浅拷贝

  `Object.assign` 拷贝所有的属性值到新的对象中，如果属性值是对象的话，拷贝的是地址

  运算符 `...`

  ```
  let a = {
  age: 1
  }
  let b = Object.assign({}, a)
  a.age = 2
  console.log(b.age) // 1
  
  let a = {
    age: 1
  }
  let b = { ...a }
  a.age = 2
  console.log(b.age) // 1
  ```

- 深拷贝

  JSON.parse(JSON.stringify(object))

  局限：会忽略 `undefined`，会忽略 `symbol`，不能序列化函数，不能解决循环引用的对象

  ```
  let a = {
    age: 1,
    jobs: {
      first: 'FE'
    }
  }
  let b = JSON.parse(JSON.stringify(a))
  a.jobs.first = 'native'
  console.log(b.jobs.first) // FE
  ```

  如果你所需拷贝的对象含有内置类型并且不包含函数，可以使用 `MessageChannel`

  

  ---



#### 原型链

原型链就是多个对象通过 `__proto__` 的方式连接了起来， `__proto__` __指向一些公共属性的内存地址，即原型，记为prototype

任何类的`prototype`属性本质上都是个类`Object`的实例，所以`prototype`也和其它实例一样也有个`__proto__`内部属性，指向其类型`Object`的prototype

`Object` 是所有对象的爸爸，所有对象都可以通过 `__proto__` 找到它，Object的**proto**中放的是Null



当我们「读取」 obj.toString 时，JS 引擎会做下面的事情：

1. 看看 obj 对象本身有没有 toString 属性。没有就走到下一步。

2. 看看 obj.__proto__ 对象有没有 toString 属性

3. 如果 obj.__proto__ 没有，那么浏览器会继续查看 obj.__proto__.__proto__

4. 如果 obj.__proto__.__proto__ 也没有，那么浏览器会继续查看 obj.__proto__.__proto__.proto__

5. 直到找到 toString 或者 __proto__ 为 null。



如果共用属性没有被引用会被系统当作垃圾回收,所以系统必须有对象一直在引用着这些共用属性，即prototype，放在构造函数中

原型的 `constructor` 属性指向构造函数，`constructor` 属性记录临时对象是由哪个函数创建的；构造函数又通过 `prototype` 属性指回原型，但是并不是所有函数都具有这个属性，`Function.prototype.bind()` 就没有这个属性。

结合js中的new理解

https://zhuanlan.zhihu.com/p/23987456

当没写任何代码时，系统也内嵌了全局对象window，window对象中又有Number、Object、String、Boolean这些属性(也是对象），这些属性引用的地址中存放着这么属性的默认返回值以及prototype，其中prototype指向各自的共有属性。这都是系统初始化好的。

而当我们创建一个变量时，创建的变量中有一个**proto**指向共用属性，所以**proto**和prototype指向的地方是一样的；也可以说

**String.prototype防止公用属性被垃圾回收的，s.proto是s要用到这些公用属性而建立的引用。**



![img](https://upload-images.jianshu.io/upload_images/8018340-45371d18f868ffcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)