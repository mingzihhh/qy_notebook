## Jquery插件开发

 jQuery为开发插件提拱了两个方法，分别是： 

jQuery.extend(object);为扩展jQuery类本身.为类添加新的方法。 
jQuery.fn.extend(object);给jQuery对象添加方法。   



**jQuery.fn.extend(object);**

对jQuery.prototype进得扩展，就是为jQuery类添加“成员函数”。jQuery类的实例可以使用这个“成员函数”。 

$.fn是指jquery的命名空间，加上fn上的方法及属性，会对jquery实例每一个有效。 
如扩展$.fn.abc(),即$.fn.abc()是对jquery扩展了一个abc方法,那么后面你的每一个jquery实例都可以引用这个方法了，如$("div").abc()

jQuery.fn =jQuery.prototype

可以用来开发Jquery插件，如

```
$.fn.carousel = function() {
    $.each(this, function(index, node) {
        new Carousel($(node))
    })
}
$('.carousel').carouse()
```



**jQuery.extend(object);**　

为jQuery类添加添加类方法，可以理解为添加静态方法。

如：
$.extend({ 
　　add:function(a,b){returna+b;} 
}); 

便为jQuery添加一个为add的“静态方法”，之后便可以在引入 jQuery的地方，使用这个方法了， $.add(3,4); //return 7 