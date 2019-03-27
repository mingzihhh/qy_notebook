## Module

ES6 模块不是对象，而是通过`export`命令显式指定输出的代码，再通过`import`命令输入。

ES6 在编译时完成模块加载，自动采用严格模式

`import`和`export`命令只能在模块的顶层，不能在代码块之中（比如，在`if`代码块之中，或在函数之中）。

#### 导出(export)

用于规定模块的对外接口，必须与模块内部的变量建立一一对应的动态关系，实时同步。

一个模块就是一个独立的文件，通过`export`命令向外输出变量，函数或类（class）。可以用as重命名

```javascript
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```



#### 导入(import)

`import`命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。

`import`命令具有提升效果，会提升到整个模块的头部，首先执行。

静态执行，不能使用表达式和变量

```javascript
import { lastName as surname } from './profile.js';
```



#### 默认导出（export default)

为模块指定默认输出。

一个模块只能有一个默认输出所以，i所以mport命令后面不用加大括号，因为只可能唯一对应`export default`命令。

本质上，`export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字。

```javascript
// 第一组
export default function crc32() { // 输出
  // ...
}

import crc32 from 'crc32'; // 输入，不需要大括号

// 第二组
export function crc32() { // 输出
  // ...
};

import {crc32} from 'crc32'; // 输入
```



#### 和CommonJS做对比

- CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

第二个差异是因为 CommonJS 加载的是一个对象（即`module.exports`属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段会生成一个只读引用。

如果要在commonjs中动态获取值，可以使用取值器函数