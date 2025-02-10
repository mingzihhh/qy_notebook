![1561344992414](C:\Users\irisqu\AppData\Roaming\Typora\typora-user-images\1561344992414.png)

#### window和document的区别是什么？

- window
  1.window对象表示浏览器中打开的窗口。
  2.window对象可以省略，如:alert() 等价于 window.alert()

- document

  1.document对象是window对象的一部分。如：document.body 等价于 window.document.body
  2.浏览器的html文档成为document对象。



#### window.location和document.location一样吗？

window对象的location属性引用的是location对象，表示该窗口中当前显示文档的URL。

document对象的location属性也是引用了location对象。

因此
window.location === document.location //true

location.herf === window.location.href === document.location.href