https://mp.weixin.qq.com/s/-wzyAJI_guqUmqsiJkEQ_w

前端工程打包后代码会与源码产生不一致，当代码运行出错时控制台会定位出错代码的位置。**SourceMap的用途是可以将转换后的代码映射回源码**，如果你部署了js文件对应的map文件资源，那么在控制台里调试时可以直接定位到源码的位置。

```
// 源码
//src/index.js
function a() {
  for (let i = 0; i < 3; i++) {
    console.log('s');
  }
}
a();


// 打包后
//dist/main-145900df.js
!function(){for(let o=0;o<3;o++)console.log("s")}();

// sourcemap
//dist/main-145900df.js.map
{
	//目前source map标准的版本为3
  "version": 3,
  //生成文件名
  "file": "main-145900df.js", 
  //记录位置信息的字符串
  "mappings": "CAAA,WACE,IAAK,IAAIA, 
  EAAI,EAAGA,EAAI,EAAGA,IACrBC,QAAQC,IAAI,KAGhBC",
  //源文件地址列表
  "sources": ["webpack://source-map-webpack-demo/./src/index.js"], 
  //源文件的内容，一个可选的源文件内容列表
  "sourcesContent": ["function a() {\n  for (let i = 0; i < 3; i++) {\n    console.log('s');\n  }\n}\na();"],
  //转换前的所有变量名和属性名
  "names": ["i", "console", "log", "a"],
  //源文件目录地址，可以用于重新定位服务器上的源文件
  "sourceRoot": ""
}
```

其中 mappings 这个字段是按照一些特殊的规则来生成，用来记录原始位置和目标位置映射关系

- 一个位置信息由1、4或5个可变长度的字段组成。

  -  generatedColumn, [sourceIndex, originalLine, orignalColumn, [nameIndex]]

  - 分别对应：
    - 在转换后的代码第几列，使用的是相对于上一个的相对位置，除非这是这个字段的第一次出现，**必选**
    - 所在的文件是属于sources属性中的第几个文件，这个字段使用的是相对位置。
    - 对应转换前代码的第几行，这个字段使用的是相对位置
    - 对应转换前代码的第几列，这个字段使用的是相对位置
    - 属于names属性中的第几个变量，这个字段使用的是相对位置。
  - 字段的生成原理是将数值通过vlq-base64编码转换成字母



**SourceMap使用的规则是如何优化存储位置信息空间的？**

- 使用相对位置，使位置数值尽可能小，在后续计算中获取真实的位置数值，从而减少存储空间
- 使用vlq-base64编码减少存储空间，如32000=》ggxT，通过计算减少存储空间
- 行数信息直接用；分割来表示



#### babel 生成 SourceMap的实现方式

babel主要执行了三个流程：解析（parse），转换（transform），生成（generate）。

parse解析阶段（获得源码对应的ast）=> transform（plugin插件执行转换ast）=> generate通过ast生成代码

- **获取源码的行列信息**：parse 和 transform 阶段，源码对应的ast经过一些plugin的执行后节点的类型或者值会发生改变，但节点中有一个loc属性（类型为SourceLocation）会一直记录着源码最开始的行列位置，所以获取到源码的ast就能够得到源码中的行列信息。
- **获取生成代码的行列信息**：generator 阶段通过ast生成转译后的代码，在这个阶段会对ast树进行遍历。针对不同类型的ast节点根据节点的含义执行word/space/token/newline等方法生成代码，这些方法里都会执行append方法添加要生成的字符串代码。在此之中有一个记录生成代码的行列信息属性会按照添加的字符串长度进行不断的累加，从而得到转译前后行列信息的对应。
- **将前后一一对应起来，然后进行vlq-base64编码并按照规则生成sourcemap文件**

