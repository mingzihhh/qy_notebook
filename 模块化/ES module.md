## ES module

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

ES6 模块不是对象（CommonJs 是对象），而是通过`export`命令显式指定输出的代码，再通过`import`命令输入。

```javascript
// ES6模块
import { stat, exists, readFile } from 'fs';
```

上面代码的实质是从`fs`模块加载 3 个方法，其他方法不加载。这种加载称为“**编译时加载**”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

ES6 自动采取严格模式，ES6 模块之中，顶层的`this`指向`undefined`，即不应该在顶层代码使用`this`。

`import`和`export`命令只能在模块的顶层，不能在代码块之中（比如，在`if`代码块之中，或在函数之中）。



**export 命令**

- 规定的是对外的接口，必须与模块内部的变量建立一一对应关系。
- 能够对外输出的就是三种接口：函数（Functions）， 类（Classes），var、let、const 声明的变量（Variables）
- 接口与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。**动态更新**
- 只能处在模块顶层，出现在块级作用域内报错，因为处于条件代码块之中，就没法做静态优化了，违背了 ES6 模块的设计初衷。
- `export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字。



**import 命令**

- 输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。
- 具有**提升效果**，会提升到整个模块的头部，首先执行。这种行为的本质是，`import`命令是编译阶段执行的，在代码运行之前。



**import 函数（动态加载模块）**

```javascript
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
	// 这里的module是default
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });
```

- 参数是所在模块的地址，返回一个promise 对象，所以需要使用`then()`方法指定处理函数  / await
- **运行时加载**，也就是说，什么时候运行到这一句，就会加载指定的模块。另外，`import()`函数与所加载的模块没有静态连接关系，这点也是与`import`语句不相同。

- 类似于 Node.js 的`require()`方法，区别主要是 import() 是异步加载，Node.js  是同步加载。

- 使用场景：
  - 按需加载
  - 条件加载
  - 根据动态路径加载



#### import.meta

 import 命令的元属性`import.meta`，返回一个对象，该对象的各种属性就是当前模块的元信息。具体包含哪些属性，标准没有规定，由各个运行环境自行决定。

**import.meta.url**：当前模块的 URL 路径。Node.js 中总是返回本地路径，即`file:URL`协议的字符串，比如`file:///home/user/foo.js`。

**mport.meta.scriptElement**：浏览器特有的元属性，返回加载模块的那个`<script>`元素，相当于`document.currentScript`属性。



#### 加载规则

**浏览器加载**

浏览器加载 ES6 模块，也使用`<script>`标签，但是要加入`type="module"`属性。

浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的`defer`属性。

**Node.js 加载**

Node.js 要求 ES6 模块采用`.mjs`后缀文件名，或者在项目的`package.json`文件中，指定`type`字段为`module`，一旦设置了以后，该项目的 JS 脚本，就被解释成 ES6 模块。
