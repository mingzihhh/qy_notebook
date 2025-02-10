代码风格规范  拆成**两**个部分：

1. 格式化规则 ( **Formatting rules** ) : 比如 单行最大宽度、空格和 Tab 混用、结尾分号、逗号、关键字空格等等等等……

2. 代码质量规则 ( **Code-quality rules** )  : 未命名变量、隐式全局变量、未定义抛错等……

其中格式规则其实**不影响代码运行**，而代码质量规则就可能会引入 Possible errors ，这也是引入 Lint 工具的重要性



https://juejin.cn/post/7050127881371910152

https://blog.theodo.com/2019/08/why-you-should-use-eslint-prettier-and-editorconfig-together/

### ESLint

**检查格式错误和代码语法质量问题**，但是默认只针对 JS 生态，需要通过插件扩展

例如，它可以强制使用箭头函数而不是普通函数，确保 React 依赖项数组正确填写，禁止使用 var 声明而使用 let 和 const，以及应用驼峰式命名约定等等。

ESLint 在格式化方面做的不够健壮，比如在 ESLint 设置 max-len 时

**.eslintrc.***

```
const JSRules = require('./rules/js');

module.exports = {
  root: true, // 表示这是 ESLint 配置的根目录，防止 ESLint 继续查找父目录的配置文件。
  extends: '@byted/eslint-config-eden/core', // 用于扩展已有的配置，可以继承一些已经存在的配置文件
  env: {
    "browser": true,
    "node": true,
    "es6": true
  },
  parserOptions: {
    ...localTsConfig,
    ecmaVersion: 'latest', // 支持的 ECMAScript 版本。'latest' 指定最新版本
    sourceType: 'module', // 支持 ES6 模块语法
    ecmaFeatures: { // 附加语法特性，支持 JSX 语法
      jsx: true,
    },
    extraFileExtensions: ['.vue'], // 添加 Vue 文件扩展名支持
  },
  ignorePatterns: ['src/gateway/*', 'src/static/*', 'src/common/dev/init.ts'， ‘dist’], // 指定 ESLint 应忽略的文件或目录路径
  plugins: ['eslint-plugin-unicorn', 'prettier'], // 指定使用的 ESLint 插件
  ignorePatterns: ['dist'],
  // 规则配置，用于设定规则的严格程度，如"error"、"warn"、"off"
  rules: {
    ...JSRules,
  },
};

```

### Prettier

 https://prettier.io/en/configuration.html

https://juejin.cn/post/7050127881371910152

只检查**格式错误**，但是比 ESLint 做的好，多语言天然支持（CSS、JSON、YAML……

例如，如果项目的标准规定使用双引号，那么你就不能使用单引号，因为 Prettier 会自动将它们替换掉。 此外，Prettier 还可以进行各种其他修复和格式化，例如代码对齐、在语句末尾添加分号等等。

 **.prettierrc.***

```
module.exports = {
	// 使用较大的打印宽度，因为 Prettier 的换行设置似乎是针对没有注释的 JavaScript.
    "printWidth": 120,
    "tabWidth": 2,
    "useTabs": false,
    "semi": true, // 强制分号
    // 单引号代替双引号
    "singleQuote": true,
    "quoteProps": "as-needed",
    "jsxSingleQuote": true,
    // 尾随逗号保留
    "trailingComma": "all",
    // 大括号内部不加空格
    "bracketSpacing": true,
    // 大括号与代码不在同一行
    "bracketSameLine": false,
    // 箭头函数参数周围加上括号
    "arrowParens": "always",
    
    // 使用 .gitattributes 来管理换行
    endOfLine: 'auto',
    // 对于 ES5 而言, 尾逗号不能用于函数参数，因此使用它们只能用于数组
    trailingComma: 'all'
};

```



### 如何处理不同工具间的冲突

-  `eslint-config-prettier`：禁用所有可能与 Prettier 冲突的 ESLint 规则
- `eslint-plugin-prettier`：将 Prettier 整合到 ESLint 中，使 Prettier 错误能够通过 ESLint 提示出来。

```
// .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "plugin:prettier/recommended" 
  ],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error" 
  }
}
```

这里做了以下几件事：

1. **继承推荐的 ESLint 配置**：`"extends": ["eslint:recommended"]`
2. **继承 Prettier 的推荐插件**：`"plugin:prettier/recommended"`，这个规则会自动包含 `eslint-config-prettier` 和 `eslint-plugin-prettier` 的正确顺序和配置。
