## canvas和svg

### canvas

[<canvas>](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/canvas)是一个可以使用脚本(通常为[JavaScript](https://developer.mozilla.org/zh-CN/docs/JavaScript))来绘制图形的 [HTML](https://developer.mozilla.org/zh-CN/docs/HTML) 元素.

Canvas 的默认大小为300像素×150像素（宽×高，像素的单位是px）。但是，可以使用HTML的高度和宽度属性来自定义Canvas 的尺寸。`<canvas>` 标签只有两个属性**——** `width`和`height`。

为了在 Canvas 上绘制图形，我们使用一个JavaScript上下文对象，它能动态创建图像（ creates graphics on the fly）。

```js
//绘制矩形，（x,y,width,height)
function draw() {
  var canvas = document.getElementById('canvas');
  if (canvas.getContext) {
    var ctx = canvas.getContext('2d');

    ctx.fillRect(25, 25, 100, 100);//绘制填充矩形
    ctx.clearRect(45, 45, 60, 60);//清除指定矩形区域
    ctx.strokeRect(50, 50, 50, 50);//绘制矩形边框
  }
}
```

```js
function draw() {
 var canvas = document.getElementById('canvas');
 if (canvas.getContext){
 var ctx = canvas.getContext('2d');

 // 填充三角形
 ctx.beginPath();
 ctx.moveTo(25,25);
 ctx.lineTo(105,25);
 ctx.lineTo(25,105);
 ctx.fill();

 // 描边三角形
 ctx.beginPath();
 ctx.moveTo(125,125);
 ctx.lineTo(125,45);
 ctx.lineTo(45,125);
 ctx.closePath();
 ctx.stroke();
 }
}
```



### svg

可缩放矢量图形（Scalable Vector Graphics）是基于可扩展标记语言（XML），用于描述二维矢量图形的一种图形格式。SVG严格遵从XML语法，并用文本格式的描述性语言来描述图像内容，因此是一种和图像分辨率无关的矢量图形格式。



###  Canvas 和 SVG 的区别

#### Canvas

- Canvas 通过 JavaScript 来绘制 2D 图形。
- Canvas 是逐像素进行渲染的。
- 在 canvas 中，一旦图形被绘制完成，它就不会继续得到浏览器的关注。如果其位置发生变化，那么整个场景也需要重新绘制，包括任何或许已被图形覆盖的对象。
- 特点：
  - 依赖分辨率
  - 不支持事件处理器
  - 弱的文本渲染能力
  - 能够以 .png 或 .jpg 格式保存结果图像
  - 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

#### SVG

- SVG 是一种使用 XML 描述 2D 图形的语言。

- SVG 基于 XML，这意味着 SVG DOM 中的每个元素都是可用的。您可以为某个元素附加 JavaScript 事件处理器。

- 在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。

- 特点：

  - 不依赖分辨率

  - 支持事件处理器

  - 最适合带有大型渲染区域的应用程序（比如谷歌地图）

  - 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）

  - 不适合游戏应用

    