Stylelint 是一个强大的、现代的 CSS 样式检查工具，它帮助开发者通过定义和应用一致的代码风格和质量规则来提高 **CSS、Sass、Less** 等样式代码的质量。Stylelint 通过用户配置的规则和插件，帮助开发者发现在 CSS 代码中的错误和不一致，提升代码的可维护性和可读性。

Stylelint 通过配置文件来定义其规则和行为。你可以在项目根目录下创建一个配置文件 `.stylelintrc`。

```
{
  "extends": "stylelint-config-recommended",
  "rules": {
    "block-no-empty": true,
    "color-no-invalid-hex": true,
    "comment-no-empty": true,
    // 禁用声明前的空行规则
    "declaration-block-trailing-semicolon": "always",
    "no-extra-semicolons": true,
    "property-no-unknown": true,
    "selector-pseudo-class-no-unknown": true,
    "selector-pseudo-element-no-unknown": true,
    "selector-type-no-unknown": true,
    "unit-no-unknown": true
  }
}
```



**如何处理和 eslint 的冲突**

安装插件 stylelint-config-prettier 

```

// 从标准配置中继承规则
  extends: [
    'stylelint-config-standard',
    'stylelint-config-rational-order',
    'prettier',
  ],
    
  plugins: [
    'stylelint-declaration-block-no-ignored-properties',
    'stylelint-prettier'
  ],
```

