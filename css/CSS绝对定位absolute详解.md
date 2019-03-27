# CSS绝对定位absolute详解

https://www.jianshu.com/p/a3da5e27d22b

#### absolute和float的相似处

**包裹性** 和 **高度欺骗**

#### 如何确定定位点

- 用户只给元素指定了absolute，未指定left/top/right/bottom。此时absolute元素的左上角定位点位置就是该元素正常文档流里的位置。
- 用户给absolute元素指定了left/right，top/bottom
  - absolute元素没有position:static以外的父元素。此时absolute所处的层是铺满全屏的，即铺满body。会根据用户指定位置的在**body**上进行定位。
  - 选取其最近一个**有定位设置（非static）的父级对象**进行绝对定位

#### 和relative的关系

- 相对定位时，不必拘泥于relative+absolute，试试去掉relative，充分利用absolute自身定位的特性，将relative和absolute解耦，防止父元素的改动对子元素的影响。耦合度越低维护起来越容易。

  即不指定left/top/right/bottom，使用负margin等控制位移

- 拉伸平铺时，用relative可以有效限制子absolute元素的拉伸平铺范围（注意是拉伸，不是缩小。要缩小请再加上width/height:100%;）

#### 和z-index的关系

以下情况不需要设z-index：

- 让absolute元素覆盖正常文档流内元素（不用设z-index，自然覆盖）
- 让后一个absolute元素覆盖前一个absolute元素（不用设z-index，只要在HTML端正确设置元素顺序即可）

什么时候需要设置z-index呢？

当absolute元素覆盖另一个absolute元素，且HTML端不方便调整DOM的先后顺序时，需要设置z-index: 1。非常少见的情况下多个absolute交错覆盖，或者需要显示最高层次的模态对话框时，可以设置z-index > 1。

#### 减少重绘和回流的开销

使用absolute控制隐藏和显示，如absolute+ top:-9999em，或absolute + visibility:hidden。

优点是absolute由于层级的关系，隐藏和显示只会重绘，但不会回流（其实我对absolute不会导致回流这一观点是持怀疑态度的，说不会回流显得过于武断，应该是absolute的回流开销小于正常DOM流中回流的开销。但我战斗力不够，尚未找到靠谱合理的检测回流的方法）。而用display:none会导致render tree重绘和回流。

另外，考虑到重绘和回流的开销，可以将动画效果放到absolute元素中，避免浏览器将render tree回流。

