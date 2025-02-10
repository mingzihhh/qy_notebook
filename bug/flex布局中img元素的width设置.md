### flex布局中img元素的width设置

注意子元素flex-grow、flex-shrink、flex-basis属性的影响

flex-grow: 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大

flex-shrink: 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小

flex-basis: 定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小

- `flex`默认值等同于`flex:0 1 auto`；

- `flex:none`等同于`flex:0 0 auto`；

- `flex:auto`等同于`flex:1 1 auto`；

  https://www.zhangxinxu.com/wordpress/2018/10/display-flex-css3-css/

  https://github.com/kaola-fed/blog/issues/117

1. n个图片的width大于父元素width
   - 设置图片的width<原始图片width，图片自动收缩到设定width
   - 设置图片的width>=原始图片大小，图片自动收缩到原始width
   - 想让图片自动填满父元素，设置图片overflow:hidden;
   - 图片width不受flex自动收缩机制影响：
     - flex-shrink:0;
     - 设置flex-basis
   - 建议在子项中通过设置flex-basis的方式去设置想要的尺寸，而不是设置width