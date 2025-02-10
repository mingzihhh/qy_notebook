`.editorconfig` 文件用于设置**代码格式化和文件风格**的规则。这些规则可以让不同的开发者在使用不同的编辑器时，都能够保持一致的代码风格。.editorconfig 文件主要配置以下几类属性：缩进风格、缩进大小、换行符、字符编码、行末空白、文件末尾新行等。

```
# 表示这是项目根目录下的顶级 .editorconfig 文件，编辑器在查找配置时会停止向上查找
root = true

# 通用设置，适用于所有文件
[*.{js,jsx,mjs,cjs,ts,tsx,mts,cts,vue}]
indent_style = space // 定义缩进风格为 space（空格）或 tab（制表符）
indent_size = 4 // 设置缩进的大小
end_of_line = lf // 指定文件的换行符格式
charset = utf-8 // 定义文件的字符编码
trim_trailing_whitespace = true // 是否自动删除行末的多余空白字符
insert_final_newline = true // 是否在文件末尾添加一个新行

```



#### 有了 Prettier，为什么还需要 `.editorconfig`

两者侧重点不同，互为补充

`.editorconfig` 更侧重于**基础文件规则**的设置，不涉及具体语法，比如**缩进风格、字符编码、行尾空白处理**等，适用于所有文件类型，不限于编程语言。这些基础规范可以确保即使在没有启用 Prettier 的情况下，团队中的开发人员在不同的编辑器中都能保持一致的文件风格。

Prettier 更侧重于**代码格式的自动化排版，代码风格优化**，可以处理代码的**单双引号、加不加分号、哪里换行**等更高层次的代码格式化问题



#### `.editorconfig` 补充了 Prettier 的哪些功能？

1. 基本的文件格式规范（非代码文件）；`.editorconfig` 适用于**所有文件类型**（如配置文件、Markdown、`Makefile` 等），提供基础的缩进、字符编码和换行规则，而 Prettier 主要专注于**代码文件**。
2. 字符编码和换行符管理；`.editorconfig` 可统一设置字符编码和换行符风格（如 LF 或 CRLF），而 Prettier 不处理这些低级别的格式问题。
3. 跨编辑器的统一支持：`.editorconfig` 支持大多数编辑器和 IDE，即使不开启 Prettier，编辑器仍能保持一致的基本文件格式。
4. 支持非编程语言的文件格式；`.editorconfig` 提供文档文件的基础格式化规则，填补了 Prettier 对纯文本文件支持的不足。
