## webpack 工作流程

<img src="/Users/qy/Library/Application Support/typora-user-images/image-20250113211317031.png" alt="image-20250113211317031" style="zoom:67%;" />



分块传输，加载分析其他资源，实现表达式的静态分析

- 配置输入输出
- 提取公共变量：服务器的端口路径，项目名称等
- cross-env 跨平台设置和使用环境变量，区分开发和生产环境
- 开发环境配置：
  - webpack-dev-server ：可以在本地起一个 http 服务，通过简单的配置还可指定其端口、热更新的开启等。
- devtool 是一个在 Webpack 配置中用于控制是否生成，以及如何生成 Source Map 的配置选项。Source Map 是一种用于将编译、打包、转换后的代码映射回原始源代码的工具，在 Webpack 中，有多种方式可以生成 Source Map
- 打包编译前清理 dist 目录：如果不清理，dist下面的打包输出会一直累积，但是只有在生产环境下，因为我们开发环境不需要哈希值，所以每次都是app.js会直接替换文件内容




#### 运行webpack打包js

-  运行在初始安装的 webpack 包(package)的 webpack 二进制文件

  ```
  npx webpack
  ```

- 使用config文件运行webpack

  ```
  npx webpack --config webpack.config.js
  ```

- npm脚本

  ```
  "build": "webpack"
  
  npm run build
  ```

  

#### 加载css

1. 安装需要的模块

   ```
   npm install --save-dev style-loader css-loader
   ```

2. 更改配置文件，webpack 根据正则表达式，来确定应该查找哪些文件，并将其提供给指定的 loader。

   ```diff
   +   module: {
   +     rules: [
   +       {
   +         test: /\.css$/,
   +         use: [
   +           'style-loader',
   +           'css-loader'
   +         ]
   +       }
   +     ]
   +   }
   ```

3. 在index.js中引用需要的css文件

   ```diff
   + import './style.css';
   ```



#### 加载图片

1. 安装需要的模块

   ```bash
   npm install --save-dev file-loader
   ```

2. 更改配置文件

   ```diff
   +       {
   +         test: /\.(png|svg|jpg|gif)$/,
   +         use: [
   +           'file-loader'
   +         ]
   +       }
   ```

3. 引入图片

   ```diff
   + import Icon from './icon.png';
   //或在css中直接使用图片地址
   ```



#### 加载字体

1. file-loader 和 url-loader 可以接收并加载任何文件，然后将其输出到构建目录。这就是说，我们可以将它们用于任何类型的文件，包括字体。

2. 更改配置文件

   ```diff
   +       {
   +         test: /\.(woff|woff2|eot|ttf|otf)$/,
   +         use: [
   +           'file-loader'
   +         ]
   +       }
   ```

3. 在css中引入字体



#### 加载数据

1. 类似于 NodeJS，JSON 支持实际上是内置的，也就是说 `import Data from './data.json'` 默认将正常运行。

   要导入 CSV、TSV 和 XML，你可以使用 [csv-loader](https://github.com/theplatapi/csv-loader) 和 [xml-loader](https://github.com/gisikw/xml-loader)。让我们处理这三类文件：

   ```bash
   npm install --save-dev csv-loader xml-loader
   ```

2. 更改配置文件

   ```diff
   +       {
   +         test: /\.(csv|tsv)$/,
   +         use: [
   +           'csv-loader'
   +         ]
   +       },
   +       {
   +         test: /\.xml$/,
   +         use: [
   +           'xml-loader'
   +         ]
   +       }
   ```

3. 可以 `import` 这四种类型的数据(JSON, CSV, TSV, XML)中的任何一种，所导入的 `Data` 变量将包含可直接使用的已解析 JSON

   ```diff
   import Data from './data.xml';
   ```

   

#### 管理输出

一旦开始对[文件名使用哈希(hash)](https://www.webpackjs.com/guides/caching)]并输出[多个 bundle](https://www.webpackjs.com/guides/code-splitting)，手动地对 `index.html` 文件进行管理，一切就会变得困难起来。可以通过一些插件，会使这个过程更容易操控。

1. 更改设置文件，分离入口

   ```diff
     module.exports = {
   +   entry: {
   +     app: './src/index.js',
   +     print: './src/print.js'
   +   },
       output: {
   +     filename: '[name].bundle.js',
         path: path.resolve(__dirname, 'dist')
       }
     };
   ```

2. 用 [`HtmlWebpackPlugin`](https://www.webpackjs.com/plugins/html-webpack-plugin) 来解决入口起点改变，html文件引用旧名称的问题。

   新生成一个 `index.html` 文件，把我们的原来的替换

   ```bash
   npm install --save-dev html-webpack-plugin
   
   webpack.config.js
   + const HtmlWebpackPlugin = require('html-webpack-plugin');
   
   +   plugins: [
   +     new HtmlWebpackPlugin({
   +       title: 'Output Management'
   +     })
   +   ],
   ```

   

   

3. 在每次构建前清理 `/dist` 文件夹，只会生成用到的文件。

4. ```bash
   npm install clean-webpack-plugin --save-dev
   
   webpack.config.js
   + const CleanWebpackPlugin = require('clean-webpack-plugin');
     plugins: [
   +     new CleanWebpackPlugin(['dist']),
         new HtmlWebpackPlugin({
           title: 'Output Management'
         })
       ],
   ```



#### 开发

1. 可以指示 webpack "watch" 依赖图中的所有文件以进行更改。如果其中一个文件被更新，代码将被重新编译，所以你不必手动运行整个构建。

   添加一个用于启动 webpack 的观察模式的 npm script 脚本：

   ```diff
   "scripts": {
         "test": "echo \"Error: no test specified\" && exit 1",
   +     "watch": "webpack --watch",
         "build": "webpack"
       },
   ```



2. 提供了一个简单的 web 服务器，并且能够实时重新加载(live reloading)

   ```
   npm install --save-dev webpack-dev-server
   
   webpack.config.js
   +   devServer: {
   +     contentBase: './dist'
   +   },
   
   package.json
       "scripts": {
         "test": "echo \"Error: no test specified\" && exit 1",
         "watch": "webpack --watch",
   +     "start": "webpack-dev-server --open",
         "build": "webpack"
       },
   ```

   

   