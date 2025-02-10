Sass(Scss)、Less 都是 **CSS 预处理器**，他们定义了一种新的语言，其基本思想是，用一种专门的编程语言为 CSS 增加了一些编程的特性，将 CSS 作为目标生成文件，然后开发者就只要使用这种语言进行 CSS 的编码工作。

| 特性                | SCSS                      | Sass                    | Less                     |
| ------------------- | ------------------------- | ----------------------- | ------------------------ |
| **文件扩展名**      | `.scss`                   | `.sass`                 | `.less`                  |
| **语法风格**        | CSS风格，使用 `{}` 和`;`  | 缩进语法，无 `{}` 和`;` | CSS风格，使用 `{}` 和`;` |
| **变量符号**        | `$`                       | `$`                     | `@`                      |
| **嵌套**            | 支持                      | 支持                    | 支持                     |
| **Mixin（混合宏）** | 支持                      | 支持                    | 支持                     |
| **函数**            | 内置函数库丰富            | 内置函数库丰富          | 函数支持较少             |
| **模块系统**        | 支持 `@use` 和 `@forward` | 不支持                  | 不支持                   |
| **编译方式**        | 需要编译工具              | 需要编译工具            | 可以客户端编译或预编译   |





## Sass/Scss

Sass 是一个将脚本解析成 CSS 的脚本语言，即 SassScript。Sass 是**不使用花括号和分号的**，通过缩进表示层级结构，对于写惯 CSS 前端的 Web 开发者来说很不直观。

因此 Sass 语法进行了改良，Sass3 就变成了 **Scss(Sassy CSS)** 。语法接近标准 CSS，因此能很好地兼容已有 CSS 代码，只是用 `{}` 取代了原来的缩进。所以 Sass 包括两套语法，通常情况下，这两套语法通过 `.sass` 和 `.scss` 两个文件扩展名区分开。

Sass/Scss的工作方式是，在Sass/Scss源文件中写代码，然后由Sass/Scss程序（Sass/Scss编译器）将其转化成浏览器可识别的CSS文件。

主要功能：

- **变量（Variables）**：允许定义和使用变量来存储颜色、字体、尺寸等重复使用的值。通过使用变量，您可以在整个样式表中轻松更改这些值，提高了代码的可维护性和灵活性。

```
$primary-color: #ff0000;

.header {
  color: $primary-color;
}
```

- **嵌套（Nesting）**：允许在样式规则中进行嵌套，以提高样式表的可读性和组织性。您可以嵌套选择器，将相关样式规则组织在一起，避免重复编写选择器。

```
.container {
  background-color: #ffffff;
  
  h1 {
    color: #000000;
  }
  
  p {
    font-size: 14px;
  }
}

```

- **混合（Mixins）**：@mixin 定义可重用的样式块，并在需要的地方通过 @include 进行引用。这使得可以轻松地将相同的样式应用于不同的选择器，提高了代码的复用性。

```
@mixin center-element {
  display: flex;
  justify-content: center;
  align-items: center;
}

.button {
  @include center-element;
  background-color: $primary-color;
}
```

- **继承（Inheritance）**：允许样式规则继承另一个样式规则的属性。这样可以减少代码量并提高样式表的维护性。

```
.button {
  background-color: $primary-color;
  color: #ffffff;
}

.primary-button {
  @extend .button;
  font-size: 16px;
}
```

- **运算符（Operators）**：支持数学运算符，您可以在样式表中执行加法、减法、乘法和除法等操作。这使得可以动态计算样式值，提高样式表的灵活性。

```
$base-font-size: 16px;

h1 {
  font-size: $base-font-size * 1.5;
}
```

- **循环（Loops）**：动态地生成重复的样式，减少冗余代码，尤其是在需要为多个类似元素或类设置样式时。

```
// 根据给定的范围生成样式
@for $i from 1 through 5 {
  .col-#{$i} {
    width: 20px * $i;
  }
}

// 遍历列表或映射，它为每个元素或键值对生成样式
@each $color in $colors {
  .btn-#{$color} {
    background-color: $color;
  }
}

// 在条件满足时不断执行，直到条件不再成立。它适用于你希望根据某些条件动态生成样式的情况。
@while $columns <= $grid-items {
  .grid-#{$columns} {
    width: 100% / $grid-items * $columns;
  }
  $columns: $columns + 1;
}

```



## Less

Less也是一种动态样式语言，受Sass影响较大，对CSS赋予了动态语言的特性，如变量，运算，继承，运算等，**Less既可以在客户端运行，也可以借助node.js在服务端运行**。

Less 没有 Sass 那么多的高级特性（如函数库、`@use` 模块系统等），但对于基本的 CSS 增强功能来说，Less 足够使用。



## PostCSS

主要是对CSS做一些解析和转换的工作，PostCSS本身只有解析能力，各种神奇的特性全靠插件

PostCSS的意思和CSS预处理器的含义相反，预处理器是把不是CSS的语言变成CSS，而PostCSS是在CSS的基础上经过PostCSS转换(做模块化、加前缀、处理兼容性问题)再输出一个新的CSS

常用插件：

- autoprefixier: 自动加浏览器兼容前缀，数据来源是 Can I use
- cssnano： 压缩代码
- postcss-preset-env：未来 css 特性转译
- postcss-import：支持css @import 内联，vite 内部预设置
- postcss-flexbugs-fixes：用于修复 Flexbox 布局中的一些已知 bug，确保兼容性更好
- postcss-pxtorem：使用 px 转 rem 的插件，常用于响应式设计
- postcss-logical：支持 CSS 中的逻辑属性和逻辑值，如 `writing-mode` 和 `text-align`，主要用于支持多语言和不同方向布局的应用
- postcss-dir-pseudo-class：该插件让你可以使用 `:dir()` 伪类，支持动态切换文档方向（例如 `ltr` 和 `rtl`）。
- postcss-inline-rtl：自动转换你写的 left, right 等关键词，自动解决 90% rtl 的场景
