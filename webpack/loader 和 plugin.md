1. webpack中 loader 与 plugin 有什么区别
2. 我们写代码的时候是 es6，浏览器运行的时候是 es5，在做代码转化的时候，webpack 帮我们做了什么事情



loader

- 处理.css 文件：style-loader，css-loader，sass-loader，postcss-loader



plugin

- html-webpack-plugin ：每一个页面是一定要有 html 文件的，而这个插件能帮助我们将打包后的 js 文件自动引进 html 文件中，毕竟你不可能每次更改代码后都手动去引入 js 文件。
- clean-webpack-plugin：每次打包前先处理掉之前的 dist 目录，以保证每次打出的都是当前最新的