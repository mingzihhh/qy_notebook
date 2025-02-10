### **Tailwind CSS 原理**

Tailwind CSS 是一个功能类（Utility-first）的 CSS 框架，它通过提供大量的低层次、原子化的类来快速构建用户界面，而不是使用传统的组件化的类或特定的样式。其原理基于一种 **按需生成** 和 **原子化的 CSS 类** 的模式，让开发者能够使用简单的类组合来构建复杂的布局。



- 工具类（Utility-first）：Tailwind CSS 提供了大量的工具类，这些类是非常基础的样式，用来实现具体的功能。这些类都是非常低层次的，针对单一的样式声明，使得开发者能够通过组合多个类来创建复杂的布局和样式。
- 按需生成：Tailwind CSS 通过 **JIT 模式** 来生成和优化 CSS。传统的 CSS 框架会预先生成所有可能的类，然后将它们打包成一个庞大的 CSS 文件。而在 JIT 模式下，Tailwind 只会根据你项目中实际使用的类动态生成 CSS。
  - Tailwind CSS 会扫描你的项目（如 `.html`, `.js`, `.vue` 等文件），找出其中使用的类。
  - 然后，Tailwind 仅会生成这些类的样式，并将它们打包到最终的 CSS 文件中。
  - 这样避免了生成所有可能的类（比如生成成千上万的颜色、大小等组合类），大大减小了最终生成的 CSS 文件的体积。
- 支持自定义配置：
  - 可以自定义的颜色、字体、间距、屏幕尺寸等
  - addUtilities：添加新的 CSS 样式规则作为工具类，直接在模板中使用
  - addVariant：自定义变体，用于改变工具类的作用范围或状态，比如 hover，focus等，会扩展现有类的使用场景



**优点：**

1. 解决 css class 起名麻烦问题
2.  raw size css 打包体积减小，因为都是在同套 css 组出自己的样式
3. css 更改安全，不会出现 class 之间的互相影响问题



**缺点：**

1. 学习成本 => 引入相关提 示VSCode 插件 https://v1.tailwindcss.com/docs/intellisense
2. 本身体积大 => https://v1.tailwindcss.com/docs/controlling-file-size
3. 类名长 => 代码规范限制上限

4. 语义化差 =>  使用 prettier 插件保证顺序增强可读性 https://github.com/tailwindlabs/prettier-plugin-tailwindcss



很适合写：

1. 外观规范
   - color
   - 字体
   - shadow
   - border
2. 布局，对齐方式
   - layout：flex..
   - justify-content，align-items
3. 尺寸
   - width，height
   - margin，padding



不适合写：

1. 重复 item 样式
2. 复杂动画
3. 复杂选择器
4. css 注释
5. deep css 覆写
6. 多层 css 跟随变量变化



和内联样式的区别

1. 支持响应式设计
2. 支持伪类
3. 可以方便改变样式单位，比如改变 mb-1 的单位



项目使用：

- 通过 postcss plugins 引入 tailwind

- 引入 tailwind 后有个别样式被重置，导致展示异常

  - 把 tailwind 样式的引入提升到自定义样式之前
  - tailwind 把 svg，img 设置为 display：block，导致部分 svg 换行显示：svg-icon 里设置 display：unset
  - tailwind 中 sup sub 都设置了 top，buttom 属性（sup top：-0.5em, 在 chrome 默认是 0），导致 el-badge 上移：el-badge 中 sup 重新设置成 0

- 使用 JIT 模式加快编译速度（冷启动速度快了三倍左右）：

  - 不使用时会直接生成所有可能用到的原子模式，JIT 模式时根据当前项目用到的 class 进行按需编译
  - 有效利用缓存机制，在热更新过程中仅生成需要更新的部分

  