## webpack的基本使用

分块传输，加载分析其他资源，实现表达式的静态分析

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
   npm install --save-dev style-loader css-loade
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

3. 

   

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

   

   