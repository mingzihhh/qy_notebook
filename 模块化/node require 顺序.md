Node.js 的 `require` 查找过程是一个自上而下、多步骤的过程：

- 首先查找**核心模块**（node 内置模块，如 `fs`、`http` 等)，node将其已经编译成二进制代码，直接书写标识符fs、http就可以

- 然后查找**路径形式的模块**（相对路径、绝对路径），需要用‘./’'../'开头，require会将这种相对路径转化为真实路径，找到模块
  - Node.js 会尝试以下几种文件扩展名，按顺序查找：
    1. 文件名.js：作为 JavaScript 文件。
    2. 文件名.json：作为 JSON 文件。
    3. 文件名.node：作为编译后的 C/C++ 扩展名文件
  - 在查找路径形式的模块时：
    - 如果指定的是文件，按照文件名查找。
    - 如果指定的是目录，会查找目录下的 `index` 文件（如 `index.js`、`index.json`）

- 最后查找**非路径形式的模块（包）**，查找包的顺序，根据 module 的 paths 变量找
  - 当前目录的 `node_modules`
  - 父目录的 `node_modules`
  - 祖父目录的 `node_modules`
  - Node.js 会读取包的 `package.json` 文件，并查找 `main` 字段以确定入口文件。如果 `main` 字段未指定，则会默认查找包目录下的 `index.js`、`index.json`、或 `index.node` 文件。

- 如果都未找到，则抛出 `MODULE_NOT_FOUND` 错误。
- 在查找并加载模块成功之后，Node.js 会**将模块缓存**，以便后续 `require` 相同模块时直接从缓存中获取，提高性能

```
const a = require('./moduleA');
const b = require('./moduleA');

// 这里 `a` 和 `b` 引用的是同一个模块对象
console.log(a === b); // 输出 true
```

