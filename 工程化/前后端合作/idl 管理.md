后端：设计idl，在技术文档中写明idl分支名、接口名和url

前端：拉取后端 IDL, 生成类型和接口调用代码

- IDL 中心化, 前后端维护一套 IDL，保持前端接口代码与后端接口定义的**一致性**

- 100% 全自动化生成类型与接口调用代码，将前端编写接口相关代码的过程**自动化**

  

### Twinkle thrift-engine

根据模板从 Thrift 文件生成代码。

https://github.com/creditkarma/thrift-parser

用TypeScript编写的Thrift解析器，生成的AST可用于从Thrift文件中编码JavaScript，或者仅用于检查Thrift结构。

thrift => AST => 通过自定义 transformer 获取 AST 中的 namespaces / enums / types / services 等 => 通过 eta 模板生成 ts 文件

### BAM

BAM 是公司内中心化管理接口的平台， **以 PSM 为逻辑空间组织接口。**

只要后端的 PSM 在 BAM 注册了，都能够通过 BAM 生成接口代码（ts / go）,包括第三方接口比如 passport，接口函数和类型默认导出

> 工具原理可以类比  NPM 这个包管理工具，BAM 可以当做数据管理后台，后端会在 BAM 上同步接口数据，前端只按照 PSM、版本或分支来拉取即可。 

- BAM 平台管理着各种方式导入的接口，平台内部会转换成一套统一的 schema 格式进行存储
- 可以通过 PSM、版本获取 BAM  上的一份接口信息
- code-generator 根据配置从 BAM 平台获取到接口配置相关信息，然后会经过解析、生成、格式化等过程最后写入到文件