## js重复变量声明

### 一、var声明的变量重名

```
var a = 1;
var a;
console.log(a); //1
```

为什么不是输出undefined呢？

从变量提升的角度去理解，上面的代码经过变量提升就可以改写成下面这样，因为变量提升只提升声明不提升赋值

```
var a;
var a;
a = 1;
console.log(a);
```

参考《JavaScript高级程序设计》第7.3章节

> JavaScript从来不会告诉你是否多次声明了同一个变量；遇到这种情况，它只会对后续的声明视而不见（不过，它会执行后续声明中的变量初始化）。



总结：

1. 使用var语句多次声明一个变量不仅是合法的,而且也不会造成任何错误.
2. 如果重复使用的一个声明有一个初始值,那么它担当的不过是一个赋值语句的角色.
3. 如果重复使用的一个声明没有一个初始值,那么它不会对原来存在的变量有任何的影响.
4. 函数内重复定义对函数外无影响，函数内重新赋值对函数外有影响



### 二、函数与变量重名

- JavaScript没有重载这个概念，它仅依据函数名来区分函数。

- 后定义的同名函数覆盖之前的，与参数无关。

- 变量声明会被顶置，函数声明也会被顶置且比变量更先声明。 

- 同名变量和函数可以相互覆盖

  ```
  var a = 100;
  function a() {
      return "function";
  }
  console.log(a);     //输出 100
  console.log(a());   
  /*
  报错
  Uncaught TypeError: a is not a function
      (anonymous function) @test.html:9
      
  等价于
  function a() {
      return "function";
  }
  var a;
  a = 100；
  ```



  ### 三、函数与内部变量重名

  ```
  function a() {
      console.log(this);  //输出 window{...}
      this.a = 1;         //即 window.a = 1，此时window下的function a已经被该变量覆盖了。
      var a = 5;          //下面的这几个变量都是局部变量，仅在花括号范围内有效。  
      a = 10;
      var v = "value"
      return "function";
  }
  console.log(a);         //输出 function a {...}
  console.log(a());       //输出 "function"
  console.log(a);         //输出 1
  console.log(v);
  /*
  输出
  Uncaught ReferenceError: v is not defined
      (anonymous function) @ mycolor.html:15
  */
  
  ```
