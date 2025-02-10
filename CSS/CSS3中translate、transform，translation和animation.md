# CSS3中translate、transform，transition和animation

**1. translate:移动，是transform的一个方法**

通过 translate() 方法，元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数：

用法：transform: translate(50px, 100px);

**2. transform:变形,改变，只是改变了视觉位置，元素本身位置不变，不改变布局**
在CSS3中transform主要包括以下几种：

旋转rotate、扭曲skew、缩放scale和移动translate以及矩阵变形matrix。

- 旋转：rotate() 顺时针旋转给定的角度，允许负值 。

  rotate(30deg)

- 扭曲：skew() 元素翻转给定的角度,根据给定的水平线（X 轴）和垂直线（Y 轴）

  skew(30deg,20deg)

- 缩放：scale() 放大或缩小，根据给定的宽度（X 轴）和高度（Y 轴）,scale默认是相对于元素的中心点缩放的，scale可以通过设置`transform-origin`来改变缩放的相对位置

   scale(2,4)

- 移动：translate() 平移，传进 x,y值，代表沿x轴和y轴平移的距离

- 所有的2D转换方法组合在一起： matrix() ，旋转、缩放、移动以及倾斜元素
  matrix(scale.x ,, , scale.y , translate.x, translate.y)

- 改变起点位置 ：transform-origin: bottom left;


综合起来使用：transform: 30deg 1.5 30deg 20deg 100px 200px;//需要有空格隔开

**3. transition: 允许CSS属性值在一定的时间区间内平滑的过渡。(过渡动画)， 改变布局**

1. 简单易用

2. **需要事件的触发**，例如单击、获取焦点、失去焦点等。

   语法：transition:property duration timing-function delay;

   例如：transition:width 2s ease 0s;

3. **一次性的**，不能重复发生

4. transition只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态

transition主要包含四个属性值：

- 执行变换的属性：transition-property；

- 变换延续的时间：transition-duration；

- 在延续时间段，变换的速率变化：transition-timing-function  //例:平缓进入、先快后慢；

- 变换延迟时间：transition-delay。

  

**4. Animation，不改变布局**

1. Animation也是通过指定某一个属性（如width、left、transform等）在两个值之间如何过渡来实现动画的.

Animation模块包括了animation-name、animation-duration、animation-timing-function、
animation-delay、animation-iteration-count、animation-play-state等属性。

```
//梯形来回平移
.box {
  width: 80px;
  height: 0;
  border-left: 40px solid transparent;
  border-right: 40px solid transparent;
  border-bottom: 40px solid #000;
  animation: move 2s alternate infinite;
}
@keyframes move {
  0% {
     transform: translate(0);
  }
  100% {
      transform: translate(200px);
  }
}
```



**与transition不同的是：**

1. Animation可以通过keyframe显式控制当前帧的属性值，而Transition只能隐式来进行
   （不能指定每帧的属性值），所以相对而言Animation的功能更加灵活；
2. Animation通过模拟属性值改变来实现动画，动画结束之后元素的属性没有变化；而transition确实改变了元素的属性值，动画结束之后元素的属性发生了变化；这一点，这在实际应用中会产生很大的区别。