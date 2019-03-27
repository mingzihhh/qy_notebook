## canvas和svg

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