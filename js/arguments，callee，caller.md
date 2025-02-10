## arguments，callee，caller

#### arguments

- arguments和数组类似但不是数组，没有数组的方法

```
var arr = Array.prototype.slice.call(arguments);
```

- 它保存着函数调用时传递过来的所有参数
- 下标从0开始顺序接收
- 一方更改也立即作用到另一方

```
function fun(f1){
		arguments[0]=5;
    console.log(f1);
    f1='hello';
    console.log(arguments[0]);
}
fun(1,2);  
```

- arguments还可以帮助我们在javascript中实现函数重载

```
function fun(f1) {
        if(arguments.length === 1) {
            console.log("我是一个参数的fun:"+arguments[0]);
        } else if(arguments.length === 2) {
            console.log("我是两个参数的fun:"+arguments[0]+arguments[1]);
        } else if(arguments.length === 3) {
            console.log("我是三个参数的fun:"+arguments[0]+arguments[1]+arguments[2]);
        } else{
            console.log("参数太多啦");
        }

}
fun(1,"kx");
```



### callee

- arguments对象的一个属性、用来指向当前执行的函数。

- 可以用它来验证传入的参数是否与形参相等

  ```
  function fun(fun1,fun2){
      if(arguments.length===arguments.callee.length){
          console.log("参数正确");
      } else{
          console.log("参数不正确");
      }
  }
  fun(1,1);
  ```

- 可以用来递归调用匿名函数

  ```
  (function(){ //死循环
      alert(1);
      arguments.callee();
  })();
  ```



### caller

- caller是function对象的一个属性，用于返回一个function引用

- 返回调用它的function对象

- 若直接在全局环境下调用fun1 则返回null，在fun2里调用之后返回fun2

  ```
  function fun1(){
  	console.log(arguments.callee.caller);
  }
  function fun2(){
      fun1();
  }
  fun1();
  fun2();
  ```

  