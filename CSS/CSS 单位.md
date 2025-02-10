## css单位

单位大体分为两大类：

- **绝对单位** ，不会因为其他元素的尺寸变化而变化。
- **相对单位** ，没有一个固定的度量值，而是由其他元素尺寸来决定的相对值。

| 单位   | 类型     | 简介                                              |
| ------ | -------- | ------------------------------------------------- |
| `px`   | Absolute | 像素 (计算机屏幕上的一个点)，`1px = 1/96in`       |
| `pt`   | Absolute | Points, `1pt = 1/72in`                            |
| `pc`   | Absolute | Picas, `1pc = 12pt`                               |
| `in`   | Absolute | Inches, `1in = 96px = 2.54cm`                     |
| `cm`   | Absolute | Centimeters, `1cm = 96/2.54px`                    |
| `mm`   | Absolute | Millimeters, `1mm = 1/10cm`                       |
| `q`    | Absolute | Quarter-millimeters, `1q = 1/4mm`                 |
| `%`    | Relative | 相对于父元素的宽度或字体大小                      |
| `em`   | Relative | 相对于父元素的字体大小                            |
| `rem`  | Relative | (即root em) 相对于html标签的字体大小              |
| `ex`   | Relative | 当前字体环境中 `x` 字母的高度                     |
| `ch`   | Relative | 当前字体环境中 `0` 数字的高度                     |
| `vw`   | Relative | 1% 视口（浏览器可视区域）的宽度                   |
| `vh`   | Relative | 1% 视口（浏览器可视区域）的高度                   |
| `vmin` | Relative | 1% 视口（浏览器可视区域）的宽度和高度中较小的尺寸 |
| `vmax` | Relative | 1% 视口（浏览器可视区域）的宽度和高度中较大的尺寸 |