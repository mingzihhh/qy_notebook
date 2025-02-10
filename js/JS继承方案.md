## JS继承方案

<https://juejin.im/post/5bcb2e295188255c55472db0#heading-7>

<https://zhuanlan.zhihu.com/p/25578222>

<https://blog.csdn.net/qq_21428081/article/details/82463697>

#### 原型链继承

原型链继承通过构造函数、原型对象和实例之间的关系（即**每一个构造函数都有一个原型对象，原型对象又包含一个指向构造函数的指针，而实例则包含一个原型对象的指针**），实现了对象之间的属性和方法共享。

- **重写原型对象**，代之以一个新类型的实例
- 缺点
  - **原型链上的方法和属性都被共享，每个实例对引用类型属性的修改都会被其他的实例共享**。
  - 创建实例时不能向父类构造函数传递参数，因为实例化子类时实际上是调用了父类构造函数，而不是传递参数的方式。

```
function Parent(name) { 
    this.name = name;
}
Parent.prototype.sayName = function() {
    console.log('parent name:', this.name);
}
function Child(name) {
    this.name = name;
}

//关键
Child.prototype = new Parent('father');
Child.prototype.constructor = Child;

//必须写在 Child.prototype = new Parent(); 之后，不然就会被覆盖掉。
Child.prototype.sayName = function() {
    console.log('child name:', this.name);
}

var child = new Child('son');
child.sayName();    // child name: son 
```



#### 借用构造函数继承

- 使用父类的构造函数来增强子类**实例**，等同于复制父类的实例给子类（不使用原型）

- 优点：与原型链继承不同，构造函数继承不会共享父类的引用属性。这意味着每个子类实例都有自己的属性副本，不会相互影响。

- 缺点
  - 只能继承父类的**实例属性**和方法，不能继承原型属性/方法
  - 每个子类都有父类实例函数的副本，可能会导致内存浪费

```
function Parent(name) { 
    this.name = name;
}
Parent.prototype.sayName = function() {
    console.log('parent name:', this.name);
}
Parent.prototype.doSomthing = function() {
    console.log('parent do something!');
}
function Child(name, parentName) {
		// 使用 call 调用父类构造函数，并传递当前子类实例(this)和参数
    Parent.call(this, parentName);
    this.name = name;
}

Child.prototype.sayName = function() {
    console.log('child name:', this.name);
}

var child = new Child('son');
child.sayName();      // child name: son
child.doSomthing();   // TypeError: child.doSomthing is not a function
```



#### 组合继承

- 比较常用，其背后的思路是**使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承**。

>- 实例属性：对象自身独有的属性和方法，用Object.hasOwnProperty判断
>- 原型属性：位于其原型链上的属性和方法
>
>属性都放在构造函数中，方法都放在原型上，既解决了构造函数模式使得所有实例的方法都独立带来的开销大的问题，又解决了原型模式使得所有实例的属性都共享所带来的耦合问题。

- 优点：
  -  组合继承能够同时继承父类构造函数内的属性和方法，以及父类原型链上的方法。这使得子类非常灵活，能够使用父类的属性和方法，同时也能继承共享的方法。
  - 不共享引用属性

- 缺点：
  - 组合继承使用过程中父类的构造函数会被调用两次：一次是创建子类型的时候，另一次是在子类型构造函数的内部 => 获取了两次属性，能会导致性能开销，尤其是如果父类构造函数执行复杂操作或依赖外部资源。

```
function Parent(name) { 
    this.name = name;
}

Parent.prototype.sayName = function() {
    console.log('parent name:', this.name);
}
Parent.prototype.doSomething = function() {
    console.log('parent do something!');
}
function Child(name, parentName) {
    Parent.call(this, parentName);	//第二次
    this.name = name;
}

Child.prototype = new Parent();	//第一次，目的是获取原型方法    
Child.prototype.constructor = Child;

Child.prototype.sayName = function() {
    console.log('child name:', this.name);
}

var child = new Child('son');
child.sayName();       // child name: son
child.doSomething();   // parent do something!
```



#### 寄生组合式继承

- 不需要在一次实例中调用两次父类的构造函数
- 借助 `Object.create()` 方法创建一个新对象，这个新对象的原型（prototype）是另一个已存在的对象，从而实现继承
- **思想在于：用一个空的构造函数去取代执行了 Parent 这个构造函数。**

```
function Parent(name) {
    this.name = name;
}
Parent.prototype.sayName = function() {
    console.log('parent name:', this.name);
}

function Child(name, parentName) {
    Parent.call(this, parentName);  
    this.name = name;    
}

function inheritPrototype(Parent, Child) {
		// 这里改用 Object.create 就可以减少组合继承中多进行一次构造的过程
    Child.prototype = Object.create(Parent.prototype);   
    Child.prototype.constructor = Child;
}

inheritPrototype(Parent, Child);

Child.prototype.sayName = function() {
    console.log('child name:', this.name);
}

var parent = new Parent('father');
parent.sayName();      // parent name: father

var child = new Child('son', 'father');
child.sayName();       // child name: son
```



#### ES6 类继承 extends

- `extends` 关键字主要用于类声明或者类表达式中，以创建一个类，该类是另一个类的子类。
- 内置 `super` 关键字简化了调用父类构造函数和方法的过程，使得在子类中扩展父类更加容易和直观。
- ES6 类继承不支持多继承，即一个类不能同时继承多个父类，这可能限制了某些特定情况下的代码结构。

```
class Parent {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    console.log("Hello from Parent");
  }
}

class Child extends Parent {
  constructor(name, childName) {
    super(name);
    this.childName = childName;
  }
}

const child = new Child("John", "Junior");

console.log(child.childName); // Junior
child.sayHello(); // Hello from Parent

```



#### ES5继承和ES6继承的区别

- ES5的继承实质上是先创建子类的实例对象this，然后再将父类的方法添加到this上（Parent.call(this)）.
- ES6的继承有所不同，实质上是先创建父类的实例对象this，然后再用子类的构造函数修改this。因为子类没有自己的this对象，所以必须先调用父类的super()方法，否则新建实例报错。



`Object.getPrototypeOf`方法可以用来从子类上获取父类。可以使用这个方法判断，一个类是否继承了另一个类。

```javascript
Object.getPrototypeOf(ColorPoint) === Point
// true
```



