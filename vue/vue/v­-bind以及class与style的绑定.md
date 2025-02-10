## v­-bind以及class与style的绑定

v-bind:动态绑定一些class类名或style样式

#### 对象语法

v-bind:class = {classname1：boolean，classname2：boolean}

![1551249667803](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1551249667803.png)

给 v­-bind:class 设置一个对象，可以动态地切换 class，值对应true ,false

当 class 的表达式过长或逻辑复杂时，还可以绑定一个计算属性，这是一种很友好和常见的用法，一般当条件多于两个时， 都可以使用 data 或 computed

#### 数组语法

v-bind：class= "[变量1，变量2]" 

![1551255507393](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1551255507393.png)

![1551256997358](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1551256997358.png)



#### 在组件上使用



#### 绑定内联样式

![1551272078978](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1551272078978.png)

使用 v­-bind:style （即：style ） 可以给元素绑定内联样式，方法与 ：class 类似，
也有对象语法和数组语法，看起来很像直接在元素上写 CSS

当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS 属性时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

注意 : css 属性名称使用驼峰命名（ came!Case ）或短横分隔命名（ kebab­case
）

**Vue中只要是大写字母，都会转换成 ‘ - ’ 加小写**