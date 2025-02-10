#### babel的编译流程

主要分为三步，流程主要为：

**parsing（解析）、transforming（转化）、generating（生成）**

- **parsing**: `babylon`（babel 内部使用的解析类库叫做 `babylon`，现在改名叫`@babel/parser`） 负责将 es6 代码进行语法分析和词法分析后转换成抽象语法树AST

- **transforming**: plugin插件使用 `@babel/traverse` 来遍历 AST 并进行节点的操作，用它提供的 API 来编写对 AST 的遍历和修改逻辑，由此来将一种AST转换为另一种AST

- **generating**：`@babel/generator`负责通过 AST 树生成 ES5 代码

其中第二步的转化是重中之重，babel 的插件机制也是在这一步发挥作用的，plugins 在这里进行操作，转化成新的AST，再交给第三步的`@babel/generator`。 所以如果没有这些plugins进驻平台，那么 babel 这个“平台”是不具备任何能力的。

而这整个过程，由`@babel/core` 负责编译过程的控制和管理。它会调用其他模块来解析、转换和生成代码。



#### plugins

babel只是提供了一个**平台**，Babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。Babel 推崇功能的单一性，就是每个插件的功能尽可能的单一。比如想使用 ES6 的箭头函数，需要插件`@babel/plugin-transform-arrow-functions`



#### presets

presets就是 plugins 的集合，比如

- @babel/preset-env：转译高版本 JavaScript 语法成为低版本浏览器可支持的语法

- @babel/preset-react：转化react jsx，一般包含 @babel/plugin-syntax-jsx 、 @babel/plugin-transform-react-jsx 、 @babel/plugin-transform-react-display-name 这几个 babel 插件

- @babel/preset-typescript

  

#### polyfill

https://juejin.cn/post/7284144079716728873?searchId=20241205225111312233FFCD541E512954#heading-5

https://juejin.cn/post/7051355444341637128#heading-16

垫平不同浏览器或者不同环境下的差异，让新的内置函数、实例方法等在低版本浏览器中也可以使用，原理是把它不支持的方法通过用支持的方法重写来获得支持，内部通过 core-js 实现

-  设置 @babel/preset-env useBuildIns 提供 polyfill
  - 会污染全局作用域，通过向全局对象和内置对象的prototype上添加方法来实现的
  - 可以通过 targets 动态调整 polyfill 对目标浏览器的支持，从而缩小编译后的代码体积
  - 设置方式：
    - entry：在入口处引入 polyfill 进行处理，根据我们配置需要支持的浏览器列表，将目标浏览器中不支持的 polyfill 进行全量引入并且实现。
    - usage：按需引入，只引入目标浏览器中不支持并且我们在代码中使用到的内容，会剔除没有使用到的 polyfill 内容
    - usage 更加轻量智能，但是 entry 可以避免很多第三方模块没有对应 polyfill 的造成的奇怪问题
  - **更适合业务**：代码体积的影响比较大，全局环境污染的问题往往并不是那么重要

- 开启 @babel/plugin-transform-runtime  corejs 提供 polyfill ：
  - 按需导入，不会污染全局作用域
  - 不支持 targets 设置，会全量引入 polyfill
  - **更适合类库**：与浏览器环境无关，首先考虑的是不要污染全局环境

```
// by @babel/plugin-transform-runtime 
// 编译后的 promise 是作为局部变量 _promise 引入的，这也就意味着它并不会污染全局作用域
var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");

var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));

var promise = new _promise["default"]();
```



babel将ES6+版本的代码分为了两种情况处理：

- 语法层： let、const、class、箭头函数等，这些需要在构建时进行转译，是指在语法层面上的转译 => preset-env 转化语法

- api方法层：Promise、includes、map等，这些是在全局或者Object、Array等的原型上新增的方法，它们可以由相应es5的方式重新定义 => polyfill

  

#### Plugins 和 Presets 的运行顺序

- Plugins 在 Presets 前运行
- Plugins 顺序从前往后排列
- Presets 顺序是从后往前



#### 脚手架支持

webpack：**不需要babel.config.json等babel配置文件**，配置 babel-loader

vue-cli：**需要babel.config.json等babel配置文件**，默认装有 @vue/cli-plugin-babel/preset，涵盖了Babel 7 + babel-loader + @vue/babel-preset-app

vite：**不需要babel.config.json等babel配置文件**，在 vite.config.js 里配置 @vitejs/plugin-legacy
