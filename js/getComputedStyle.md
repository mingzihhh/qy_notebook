https://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/

### getComputedStyle方法

获取当前元素所有最终使用的CSS属性值。返回的是一个CSS样式声明对象([object CSSStyleDeclaration])，只读。

#### 语法

```
var style = window.getComputedStyle("元素", "伪类");
```

#### getComputedStyle与style的区别

1. **只读与可写**
   正如上面提到的`getComputedStyle`方法是只读的，只能获取样式，不能设置；而`element.style`能读能写，能屈能伸。

2. **获取的对象范围**
   `getComputedStyle`方法获取的是最终应用在元素上的所有CSS属性对象（即使没有CSS代码，也会把默认的祖宗八代都显示出来）；

   `element.style`只能获取元素`style`属性中的CSS样式。

   因此对于一个光秃秃的元素`<p>`，`getComputedStyle`方法返回对象中`length`属性值（如果有）就是`190+`(据我测试FF:192, IE9:195, Chrome:253, 不同环境结果可能有差异), 而`element.style`就是`0`。

#### getComputedStyle与currentStyle

IE中的属性，作用类似getComputedStyle，不支持伪类样式获取

### getPropertyValue方法

可以获取CSS样式申明对象上的属性值（直接属性名称），不支持驼峰写法

```
window.getComputedStyle(element, null).getPropertyValue("float");
```

##### getPropertyValue和getAttribute

在老的IE浏览器（包括最新的），`getAttribute`方法提供了与`getPropertyValue`方法类似的功能，可以访问CSS样式对象的属性。需要驼峰写法



