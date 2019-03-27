## node.js和npm

#### node.js是什么

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 

#### node.js使用

1. 引用node.js的内置模块

   ```
   var fs = require('fs')
   fs.readFile()
   ```

   

2. 写自己的node.js 模块

   ```
   function xxx(){}
   module.exports.xxx = xxx
   //导出一个对象，其中只包含一个属性xxx;如果是当前路径或相对路径下的模块，必须加./，否则会去内置模块中寻找
   
   var xxx = require('./xxx.js')
   //引入自己的node.js模块，.js可以不加，默认为js
   xxx.xxx()
   //使用该模块的功能
   ```

   

3. 处理多次回调，防止回调地狱

## npm

node.js包管理器

#### NPM使用

1. **npm install**

```
npm install -g http-server
//全局下载，命令行工具
npm install --save axios
//在package.json中添加dependencies
npm install --save-dev webpack
//在package.json中添加devDependcies
```

2. **npm uninstall**

#### package.json 

npm init ：创建一个包，包含package.json。

这是一个种子文件，dependencies包括对其他npm包的依赖，方便其他人使用下载，devDependcies包含的是一些运行时不需要，可能构建时需要的包；scripts是命令，命令名：命令行，提供入口

![1552130949382](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552130949382.png)

npm start 

npm run xxx 

npm install



#### require的相对路径

不同的实现对require时的路径有不同要求，一般情况可以省略`js`拓展名，可以使用相对路径，也可以使用绝对路径，甚至可以省略路径直接使用模块名（前提是该模块是系统内置模块）

找第三方模块：

在当前目录下node_modules，一层一层向上找，找到根目录找不到报错

命令行工具：

npm install -g: usr/local/lib/node_modules/...全局

npm install http-server --save-dev： 安装在当前项目目录下

![1552138002862](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552138002862.png)

或者通过package.json中的scripts使用

![1552138065540](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552138065540.png)

![1552138105342](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552138105342.png)



在安装一个要打包到生产环境的安装包时，你应该使用 `npm install --save`，如果你在安装一个用于开发环境的安装包（例如，linter, 测试库等），你应该使用 `npm install --save-dev`

