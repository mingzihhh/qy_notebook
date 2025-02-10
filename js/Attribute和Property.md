# Attribute和Property

1. **attribute对应 HTML， property 对应 DOM。**

   `attribute`是标记语言的概念（比如HTML语言），而标记语言本身就是一种文本，所以`attribute`这种文本描述的性质在标记语言中很容易使用。

   而`property`则是保存在内存中，而内存会随着程序运行结束被释放，因此变得无法长期储存。在JavaScript中，DOM对象通常都是多重继承的。同时继承了HTML和JavaScript的`Object`。

   `Object`是完完全全的内存对象，所以使用的是`property`，而HTML本身就是标记语言，所以使用的是`attribute`。

   

2. **`property`指的是DOM的`property`，是元素属性。**

   在JavaScript中,DOM的`property`可以通过`.`符号或`[]`来获取或重置；但对于自定义的`attribute`通过`.`符号获取的值是`undefined`，不过可以通过`.`来重置它，需要注意的是，虽然重置了，但并不会修改HTML标签中自定义的`attribute`值，它只会存在JavaScript中。

   DOM中的节点事实上是一个对象。使用对象访问属性的方式，可以访问到DOM节点的每一个`property`的属性。另外，`property`的值可以是任何的数据类型，对大小写也敏感。

   

3. **`attribute`是指标签属性，也就是HTML元素指定的属性。**

   比如我们常见的`id`、`class`、`value`和`onclick`等属性，以及一些自定义的属性，比如大家熟悉的`data-*`，甚至像`myAttr`这样的自定义属性。

   而DOM的`attributes`则可以通过`setAttribute`、`getAttribute`和`removeAttribute`来做相应的处理。

   在浏览器开发者工具中，通过`properties`可以查看到`attributes`具体的值，其对应的正好是HTML标签`input`中的`attributes`。

   

4. **`property`和`attributies`都是`properties`的子集，而每个`attribute`是`attributies`的子集。**

   **properties**，其主要是JavaScript获取DOM对象上的属性值。而且它是作为JavaScript的基本对象。这个节点包括很多`property`，比如`classList`、`className`之类的。一个JavaScript对象有很多`property`，该集合名字为`properties`，`properties`里面有其他`property`以及`attributes`。而`attributes`里面有很多`attribute`

   

5. **使用**

   - 在大多数情况下「property」和「attribute」是同步的，对HTML标签的标准的`attribute`（比如，`name`、`id`、`class`）会相互影响。但是参数不一定相同

   - 「node.property」的方式不能获取自定义属性，「node.getAttribute()」的方式可以获取自定义属性

   - 「node. getAttribute()」获取自定义属性忽略属性的大小写

   - 「node.getAttribute()」获取自定义属性得到的值的类型总是字符串

   - **disable**：HTML中只要出现了disabled 属性，不管值是什么，对于 DOM property结果都是true， 而对于 attribute 获取的则是把 HTML 里对应属性的值拿到转换成字符串。

   - **checked**：若元素有设定 HTML 特性 checked，则可用 `.attr()` 取得字串 checked ；若没有设定，则会取到 undefined。若使用 `.prop()` 取该元素的属性 checked 的值，则会取得布尔值 true 或 false。

   - **href**：对于 a 链接的 href， 使用 a.getAttribute('href') 就是从 HTML 里获取对应属性的值转化成字符串，而 a.href 则获取有意义的真实地址。虽然在 HTML 特性上设定 checked 的值为 false，但 checkbox 仍为勾选状态，prop 为 true 且 attr 为 checked。在 [HTML5 規範](https://www.w3.org/TR/html5/infrastructure.html#boolean-attributes)说明得很清楚，只要 attribute 出现，就是勾选，若不要勾选就不要设定。

   - **input中**，`attribute`的`value`值只在初始化的时候跟`property`一样。

     - 使用`getAttribute`获取的值是初始化的值，如果没有设置则是空，除非使用`setAttribute`手动去改变，但这个改变也不会影响到`property`的值。

     - `property`的`value`值是`input`实时输入的值，但该值的改变同样也不会影响`attribute`的`value`值。

     - 即attribute永远是呈现初始值，与目前状态无关，html中不发生变化，除非手动改变；property

       会反映目前状态

   - 对于`style`标签，`element.getAttribute`返回的是`string`，而`element.property`返回的是`object`

     

6. 在实际应用当中，大部分DOM操作都是使用`properties`来完成。但有些情形之下，使用`attributes`更为适合，比如：在自定义HTML的`attributes`，因为它并不同步到DOM的`property`。还有就是访问内置HTML的`attributes`（设置的初始值），`attribute`不能从`property`同步过来，典型的就是`input`的`value`

https://www.w3cplus.com/javascript/dom-attributes-and-properties.html