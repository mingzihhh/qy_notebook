# HTML，css和js

1. 必考：你是如何理解 HTML 语义化的？

   具体化：HTML 语义化就是使用正确的标签，段落就写 p 标签，标题就写 h1 标签，文章就写article标签，视频就写video标签，等等。段落写成p标签，标题写成h1~h6

   抽象化：html发展，意义等

   

2. meta viewport 是做什么用的，怎么写？

   近几年手机的发展是前端的工作中心从电脑桌面转移到手机上，所以会有viewport的出现。使手机上页面不能缩放

   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   

3. 你用过哪些 HTML 5 标签？

   （想知道平时有没有写H5的页面）

   内容：header,main,footer,artical

   功能：canvas,video,audio -> 有后续问题，看mdn,

   如canvas是如何进行绘制的，video的属性，兼容性

   

4. H5 是什么？

   移动端页面（看知乎）

# CSS

1. 必考：两种盒模型分别说一下。

   说清楚区别，拔高：为什么会有新的盒模型

   ->为什么border-box比content-box好，因为写起来很方便

   

2. 必考：如何垂直居中？
   分情况讨论，至少了解四五种

   ->为什么水平居中容易实现，垂直居中不容易实现？css渲染的回溯机制

   

3. 必考：flex 怎么用，常用属性有哪些？

   （平常是否使用flex)举例

   ->给具体场景实现

   

4. 必考：BFC 是什么？
   举例，举几个含有BFC的例子，BFC全程是块级格式化上下文，如果给一个div加一个overflow:hidden，里面的浮点元素就会被包裹起来

   

5. CSS 选择器优先级
   举例，如果引导错误答案（css2），就按其思路回答

   越具体优先级越高

   写在后面的，覆盖写在前面的

   important!最高，要少用

   

6. 清除浮动说一下
   代码，背

   

# 原生 JS

1. 必考：ES 6 语法知道哪些，分别怎么用？
   举例（let const class 展开运算等）

   class,promise

   

2. 必考 Promise、Promise.all、Promise.race 分别怎么用？
   举例，看mdn

   

3. 必考：手写函数防抖和函数节流
   函数节流

   ```
   //cd冷却时间
   function fn(){}
   var cd = false
   button.onclick = function(){
       if(cd){
       //	
       }else{
       	fn()
       	cd = true
       	var timeId = setTimeOut(()=>{
               cd = false
       	},3000)
       }
   }
   ```

   函数防抖

   ```
   var timeId = null
   button.onclick = function(){
       if(timeId){
           window.clearTimeOut(timeId)
       }
       timeId = setTimeout(() => {
       	fn()
       	timeId = null
       },5000)
   }
   ```

   

4. 必考：手写AJAX

   ```
   var request = new XMLHttpRequest()
   request.open('GET','xxxx')
   request.onreadystatechange = function(){
   	if(request.readystate === 4){
   		console.log('请求完成')
   		if(request.status >= 200 &&request.status <= 300){
               console.log('请求成功')
   		}else{
               
   		}
   	}	
   }
   request.send()
   ```

   

5. 必考：这段代码里的 this 是什么？
   看调用

   ![1552305147025](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552305147025.png)

   

   

6. 必考：闭包/立即执行函数是什么？

   **闭包是什么，有什么用途?**

   [函数]和「函数内部能访问到的变量」（也叫环境）的总和，就是一个闭包。

   闭包常常用来「间接访问一个变量」。换句话说，「隐藏一个变量」，可以形成一个私有作用域，保护里面的变量不受外界干扰，但可以间接访问。

   **立即执行函数是什么，立即执行函数有什么用途？**

   立即执行函数就是声明一个匿名函数，并且马上调用这个匿名函数。

   只有一个作用：创建一个独立的作用域。这个作用域里面的变量，外面访问不到（即避免「变量污染」）。

   

7. 必考：什么是 JSONP，什么是 CORS，什么是跨域？
   举例

8. 常考：async/await 怎么用，如何捕获异常？
   举例，看mdn

9. 常考：如何实现深拷贝？
   代码，关键词：递归，判断类型，检查循环引用（环）

   

10. 常考：如何用正则实现 trim()？

```
function trim(str){
    return str.replace(/^\s+|\s+$/g,'')
}
```



11. 常考：不用 class 如何实现继承？用 class 又如何实现？

    ```
    function Animal(){
        this.a = 1
    }
    Animal.prototype.move = function(){}
    function Dog(){
        Animal.apply(this,arguments)//获取属性
        this.b = 2
    }
    //获取方法
    var f = function(){}
    f.prototype = Animal.prototype
    Dog.prototype = new f()
    //或者Dog.prototype = Object.create(Animal.prototype)
    Dog.prototype.constructor = Dog
    Dog.prototype.bark = function()
    ```

    ```
    class Dog extends Animal {
    	constructor() {
            super()
    	}
    }
    ```

    

    

12. 常考：如何实现数组去重？

1. hash

2. [...new Set(array)]

3. WeakMap 支持所有类型去重

   

13. 放弃：== 相关题目（反着答）
    性价比，很少用==，一直用===

    

14. 送命题：手写一个 Promise

    看博客，说思路