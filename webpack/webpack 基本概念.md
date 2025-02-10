## webpack 基本概念

本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个**依赖关系图**(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 *bundle*。



### **entry**

定义项目的入口点，作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。每个依赖项随即被处理，最后输出到称之为 bundles 的文件中，


- 单页面，一个入口；多页面，一个页面会对应一个构建入口
- 每个入口生成一个对应的 bundle，所以需要**提取共享模块，减少 bundle 重复代码**
- **动态加载**的模块**不是**入口起点。

```
module.exports = {
  entry: {
    main: './src/index.js',
    app: './src/app.js'
    // 可以添加更多入口，创建多个 bundle
  }
};
```



### **output**

告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件，默认值为 `./dist`。基本上，整个应用程序结构，都会被编译到你指定的输出路径的文件夹中。

- webpack 最终构建出来的静态文件,构建结果的文件名、路径等都是可以配置的
- 如果配置创建了多个单独的 "chunk"（例如，使用多个入口点或使用像 CommonsChunkPlugin 这样的插件），应该**使用占位符(substitutions)来确保每个文件具有唯一的名称**。
  - [name].js：[name] 是一个占位符，为每个入口点生成带有其名称的输出文件
  - [id].js：[id] 将会被替换为每个 chunk 的内部唯一标识符
  - 按照 hash 值类型可以分为：
    - **hash**：与整个项目的构建相关的哈希值。不论哪个文件有变化，整个项目构建的哈希值都会改变
    - **contenthash**：与文件内容直接相关的哈希值。只有文件内容改变了，`[contenthash]` 才会改变。这在使用如 css-extract-plugin 分离 CSS 文件或者处理其他静态资源离散文件时非常有用，因为你可能只希望在 CSS 文件的内容实际发生变化时才改变文件名，内容不变时文件哈希不变，使缓存更有效
    - **chunkhash**：与 Webpack 打包过程中生成的 chunk 相关的哈希值。同一 chunk 内文件没有变化时，[chunkhash] 是不变的。它可以用来**优化浏览器缓存**，因为只要 `chunk` 内容不变，文件名也不会变，独立的共享 `chunk`（如 `chunk-lodash`）可以在版本不变时被浏览器长期缓存
    - **modulehash**：与单个模块相关的哈希值
    - **fullhash**：针对整个打包过程生成的全局哈希值，Webpack 5 引入的哈希值，用来代替旧版本中的 [hash]，**常用于区分不同的构建版本**

```

const path = require('path');

module.exports = {
  //...
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    publicPath: '/', // 指定在浏览器中被引用的时候的公共的URL的路径
    library: 'MyLibrary', // 暴露库的名称
    libraryTarget: 'umd',// 在何种形式下导出库
    chunkFilename: '[id].[chunkhash].js',// 非入口(non-entry) chunk 文件的名称
    assetModuleFilename: 'images/[hash][ext][query]'// 生成的资产(asset)文件名模版
  }

};
```



### **resolve**

设置如何解析代码模块路径，常用功能：

- resolve.alias：设置 import 或 require 的别名
- resolve.extensions：导入语句没带文件后缀时，Webpack 会自动带上后缀后去尝试访问文件是否存在。该配置用于配置在尝试过程中用到的后缀列表。



### module

配置处理项目中不同类型模块的规则，常用功能：

- module.rules：配置模块的读取和解析规则，通常用来配置 Loader。
  - **条件匹配**：通过 test、include、exclude 三个配置项来选中 Loader 要应用规则的文件
  - **应用规则**：对选中的文件通过 use 配置项来应用 Loader，可以只应用一个 Loader 或者**按照从后往前的顺序**应用一组 Loader，同时可以分别向 Loader 传入参数
  - **重置顺序**：一组 Loader 的执行顺序默认是从右到左执行的，通过 enforce 选项可以将其中一个 Loader 的执行顺序放到最前或最后
  - parser.dataUrlCondition：如果一个模块源码大小小于 `maxSize`，那么模块会被作为一个 Base64 编码的字符串注入到包中， 否则模块文件会被生成到输出的目标目录中。用于处理图片
  - 


##### **loader** ：**对模块的源代码进行转换**

 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript），**loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块**，然后你就可以利用 webpack 的打包能力，对它们进行处理。

本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。在没有添加额外插件的情况下，webpack 会默认把所有依赖打包成 js 文件。

当我们需要**使用不同的 loader 来解析处理不同类型的文件**时，我们可以在 `module.rules` 字段下来配置相关的规则



### **plugin**

模块代码转换的工作由 loader 来处理，除此之外的其他任何工作都可以交由 plugin 来完成，**用于扩展 Webpack 功能**。

插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量，功能极其强大，可以用来处理各种各样的任务。想要使用一个插件，你只需要 `require()` 它，然后向 `plugins` 数组中传入 `new` 实例。

常用插件：

- DefinePlugin：定义环境变量
- webpack-deadcode-plugin：检查死代码
- workbox-webpack-plugin：自动生成和配置 Service Worker 文件，可以用来对图片等静态资源进行离线缓存
- stats-webpack-plugin：生成打包过程的统计信息文件，JSON 文件格式，帮助开发者分析和调试打包结果，例如查看模块的依赖关系、资源大小和打包性能等。
- webpack-bundle-analyzer：提供交互式的可视化图表，HTML 页面，用于分析打包结果。





### optimization

用于优化打包结果，提升应用的性能和资源利用效率。

- splitChunks：将代码拆分为多个包，减少初始加载时间，优化缓存。适用于将第三方库（如 `lodash`、`react`）分离到 `vendors`，提取重复代码到 `common` 模块等

- runtimeChunk：将运行时代码单独抽取，减少业务代码的变化，优化缓存。

  - 运行时代码：Webpack 自动生成，负责加载和管理应用的各种模块、资源、依赖以及执行过程的代码。

- minimize：控制是否启用代码压缩，Webpack 5 默认使用 `TerserPlugin` 压缩 JavaScript。适用于生产环境，压缩代码并移除无用内容。

  - 可以进一步对 js 进行处理，比如删除 console，优化箭头函数，简化条件语句等，优化 js 初始化执行时间优化

- removeEmptyChunks：移除空块，提高性能。适用于清理代码分割过程中产生的空文件。

- removeAvailableModules：自动检测模块依赖关系并移除已包含的模块。

- usedExports：标记未引用的模块。可以配合 deadcode 检查

  


### cache

缓存生成的 webpack 模块和 chunk，来改善构建速度。`cache` 会在开发模式默认设置成 `type: 'memory'` 而且在生产模式中被禁用

- type：
  - memory 存储在内存
  - filesystem 存储在文件系统，将构建的中间结果保存在磁盘上，而不是仅仅保存在内存中。这样做的好处是可以在重新启动 Webpack 时（例如重启开发服务器）避免重新构建所有模块，极大地提高了构建效率。
- allowCollectingMemory：允许 Webpack 收集内存缓存。启用此选项后，Webpack 会尝试将部分缓存数据存储在内存中，但最终会将其写入到文件系统中，以便下次构建时能够快速加载。仅当 cache.type 设置为 `'filesystem'` 时生效。这需要将数据复制到更小的缓冲区中，并有性能成本。
- buildDependencies：设定依赖关系，当依赖变化时缓存会被清除，推荐在 webpack 配置中设置 `cache.buildDependencies.config: [__filename]` 来获取最新配置以及所有依赖项。



### devServer

实时重新加载的开发服务器，用于开发

- open：在启动时自动打开浏览器并访问指定的 URL

- hot：启动 HMR （热模块替换），自动更新变动的模块而无需刷新整个页面。

- proxy：将某些请求代理到后端服务

- headers：设置开发服务器的 HTTP 响应头，比如设置 CORS

- server：可以指定服务器类型或者添加中间件

  



### 其他

- devTool：用于控制是否生成以及如何生成 SourceMap
  - 生产环境：
    - `source-map` - 整个 source map 作为一个单独的文件生成。它为 bundle 添加了一个引用注释，以便开发工具知道在哪里可以找到它。
    - `hidden-source-map` - 与 `source-map` 相同，但不会为 bundle 添加引用注释。如果你只想 source map 映射那些源自错误报告的错误堆栈跟踪信息，但不想为浏览器开发工具暴露你的 source map，这个选项会很有用。
- externals： 设置外部扩展，构建中无须打包的模块，也就是这些模块是外部环境提供的，比如 cdn
- watch：支持监听文件更新，在文件发生变化时重新编译。在使用 Webpack 时，监听模式默认时关闭的。使用 DevServer 时，监听模式默认是开启的。
