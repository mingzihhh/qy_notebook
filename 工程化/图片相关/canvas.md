HTML5 中的 canvas 是很实用的工具，为我们提供了强大的绘图能力，使我们可以通过 js 自由实现出想要的图形效果。写这篇文章的目的是想通过打砖块这一款经典简单的小游戏入门 canvas ，对相关基础知识进行实践。

打砖块的游戏逻辑是抛起一个小球对空中的砖块进行击打，小球可以在砖块间进行反弹碰撞，打中后砖块消失并且得分，本文的主要内容包括：

- 使用 canvas api 进行画面绘制
- 使用 canvas 动画控制画面中的元素
- 实现一个简单有趣的游戏 demo

（这里应是一张gif：

## 画面绘制

在开始之前，先对游戏画面包括的主要元素进行分析：

 砖块 -> 矩形，球 -> 圆， 挡板 -> 矩形

问题也就简化为：**怎么在 canvas 上画矩形/圆，并调整它们的位置和样式？**

简单的来说，canvas 的绘制可以分为以下两个步骤：

**step 1.** **展开****画布 - 获取渲染上下文**

getContext() 方法为我们提供了 canvas 的渲染上下文和绘画能力，接下来的绘制都需要基于它进行。

```JavaScript
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
```

**step 2. 开始绘画 - 绘制形状路径 / 添加颜色样式 / 添加文字等**

我们主要组合使用**路径函数**来绘制形状，改变**填充属性**来填充颜色。canvas 为我们提供了许多绘制不同路径的方法，如线，矩形，圆弧，贝塞尔曲线等，通过组合不同路径，可以画出各种复杂的图形。

以画矩形和圆为例，通过设置位置坐标和一些基本属性，如宽，高，半径等等，就可以画出一个简单的静态图形。

```JavaScript
let canvas = document.getElementById('canvas');
let ctx = canvas.getContext('2d');

function drawRect(x, y, width, height) { 
     // 开始路径
    ctx.beginPath();
    // 绘制矩形路径
    // 参数分别对应：矩形左上角的 x 坐标，y 坐标，矩形的宽，高
    ctx.rect(x, y, width, height); 
    // 设置填充属性
    ctx.fillStyle = 'green'; 
    // 填充路径的内容区域生成实心图形
    ctx.fill(); 
    // 结束路径
    // 绘制一条从当前点到开始点的直线来闭合图形，如果图形已经闭合，则什么也不做
    ctx.closePath(); 
}


function drawCircle(x, y, radius) { 
     // 开始路径
    ctx.beginPath();
    // 绘制圆形
    // 参数分别对应：圆心的 x 坐标，y 坐标，圆的半径，开始角度，结束角度
    ctx.arc(x, y, 10, 0, Math.PI*2); 
    // 设置填充属性
    ctx.fillStyle = 'yellow'; 
    // 填充路径的内容区域生成实心图形
    ctx.fill(); 
    // 结束路径
    // 绘制一条从当前点到开始点的直线来闭合图形，如果图形已经闭合，则什么也不做
    ctx.closePath(); 
}
```

在了解了 canvas 的基本绘制方法以后，我们可以对游戏中的元素进行设计和封装，目前来说，它们至少都需要

- **基本的属性**，如挡板的长宽，球的半径，位置坐标
- **init()**，用来初始化，比如设定游戏开始时球的位置 
- **draw()**, 绘制元素

因为画面中有多个砖块，所以可以提供类作为模板，再将所有砖块整体视为一个元素。

```JavaScript
const ball = {
    radius: 10,
    position: { x: 0, y: 0 },
    velocity: { x: 0, y: 0 },
    onPaddle: false, // 小球是否在挡板上
    init(){
        this.position.x = CANVAS_WIDTH / 2;
        this.position.y = CANVAS_HEIGHT - 20;
        this.onPaddle = true;
    },
    draw() {
      ctx.beginPath();
      ctx.arc(this.position.x, this.position.y, this.radius, 0, Math.PI*2);
      ctx.fillStyle = "#0095DD";
      ctx.fill();
      ctx.closePath();
    },
}

 const paddle = {
      width: 75,
      height: 10,
      position: { x: 0, y: 0},
      init(){
          this.position.x = CANVAS_WIDTH / 2 - this.width / 2;
          this.position.y = CANVAS_HEIGHT - this.height;
      },
      draw(){
        ctx.beginPath();
        ctx.rect(this.position.x, this.position.y, this.width, this.height);
        ctx.fillStyle = "#0095DD";
        ctx.fill();
        ctx.closePath();
      }
  }
  
class Brick {
    constructor(x,y) {
      this.width = BRICK_WIDTH;
      this.height = BRICK_HEIGHT;
      this.position = { x , y};
      this.hit = false;
    }
    
    draw () {
      ctx.beginPath();
      ctx.rect(this.position.x, this.position.y, this.width, this.height);
      ctx.fillStyle = "#0095DD";
      ctx.fill();
      ctx.closePath();
    }
    
    checkCollision () {
      return !this.hit
          && ball.position.x + ball.radius > this.position.x
          && ball.position.x - ball.radius < this.position.x + this.width
          && ball.position.y + ball.radius > this.position.y
          && ball.position.y - ball.radius < this.position.y + this.height;
    }
    }
  
const wall = {
    bricks: [],
    init(){
        this.bricks = [];
        for(let c = 0; c < BRICK_COLUMN_COUNT; c++) {
            for(let r=0; r < BRICK_ROW_COUNT; r++) {
                const brickX = r * (BRICK_WIDTH + BRICLK_PADDING) + BRICK_OFFSET_LEFT;
                const brickY = c * (BRICK_HEIGHT + BRICLK_PADDING) + BRICK_OFFSET_TOP;
                const brick = new Brick(brickX, brickY);
                this.bricks.push(brick);
            }
        }
    },
    draw(){
      this.bricks.forEach(function(brick) {
          // 与小球碰撞的砖块不进行绘制
          if(!brick.hit) {
              brick.draw();
          }
      })
    }
}
```

## 让元素动起来

 实现 canvas 动画的原理和逐帧动画相同，将每帧不同的图像连续切换播放，当切换速度达到每秒24帧时，就可以产生动画效果。也就是说，**把我们每次绘制的 canvas 图像作为一帧，不断的对 canvas 进行更新重绘，就可以实现 canvas 动画**。

首先，我们需要不断的更新canvas，也就是要使用一个循环执行的方法，这里我们使用**requestAnimationFrame**。

> **`window.requestAnimationFrame()`** 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

当然也可以用 setTimeout 和 setInterval ，但它们的缺点是不够精确，虽然可以设定时间间隔参数，但是由于 js 是单线程的，实际执行的时间会受到任务队列中其他任务顺序的影响；相比之下，requestAnimationFrame 采用的是系统时间间隔，一般每秒重绘60次，更加的平稳并且有效率，可以使动画更加平滑顺畅。

```JavaScript
function draw() {
  // 更新
  updateGame();
  // 清空 canvas，否则会影响下一帧图像
  ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
  // 重绘
  drawGame();
  requestAnimationFrame(draw);
}
```

其次，我们要梳理每个元素在画面上运动的逻辑，为它们添加 update 方法，通过定时执行 update()改变元素的位置状态，达到动画的效果。以弹跳的小球为例：

- 小球发射时，为小球**添加速率矢量，**使小球能够通过速率移动
- 通过比较坐标位置进行**碰撞检测，**当发生碰撞时将速度矢量反转，有两种情况：
  - 与画布上下左右边缘的碰撞，这种情况比较简单，比较圆心和边缘距离即可
  - 与砖块之间的碰撞 （逻辑需要再优化下）

```JavaScript
  // 发射小球
  ball.launch = function(){
        this.onPaddle = false;
        this.velocity.x = getRandomInt(-3, 3);
        this.velocity.y =  -INITIAL_BALL_SPEED;
    };
   ball.update = function(){
      if (this.onPaddle) {
        if (input[SHIFT_KEY]) {
        this.launch();
        }
       // 小球在挡板上时，根据挡板位置计算小球坐标
        this.position.x = paddle.position.x + paddle.width / 2;
        this.position.y = paddle.position.y - this.radius;
      } else {
       // 根据速率移动小球
        this.position.x += this.velocity.x;
        this.position.y += this.velocity.y;
        
        // 检测与画布左右边缘的碰撞
        if (this.position.x < this.radius || this.position.x > CANVAS_WIDTH - this.radius) {
            this.velocity.x = -this.velocity.x;
        }
        // 检测与画布上方的碰撞
        if (this.position.y < this.radius) {
            this.velocity.y = -this.velocity.y;
        } else if (this.position.y > CANVAS_HEIGHT - this.radius ) {
            // 与挡板相撞时
            if (this.position.x > paddle.position.x - this.radius && this.position.x < paddle.position.x + paddle.width + this.radius) {
                this.velocity.y = -this.velocity.y;
            } else {
            // 撞到画布下边缘则结束游戏
                lives--;
                if (lives === 0) {
                    alert("GAME OVER");
                    start();
                }
                this.onPaddle = true;
            }
        }
      }
    };
    
    // 检测小球和砖块的碰撞
    wall.update = function() {
      this.bricks.forEach(function(brick, index) {
        if (brick.checkCollision()) {
            brick.hit = true;
            score++;
            if (score === BRICK_ROW_COUNT * BRICK_COLUMN_COUNT) {
                alert('YOU WIN, CONGRATS!')
                start();
            }
            ball.velocity.y = -ball.velocity.y;
        }
      })
   }
```

## 完善游戏逻辑

了解了上面的知识以后，我们根据自己的想法可以完善游戏逻辑，基本上就是对 update函数的改动。举几个🌰：

**键盘控制游戏开始 / 挡板移动**

使用事件监听获取并记录键盘输入，再根据按键状态更新挡板位置，挡板不能移出画布，所以需要和画布边缘做碰撞检测。

```JavaScript
const SHIFT_KEY = 16;
const LEFT_KEY = 37;
const RIGHT_KEY = 39;

function onKeyDown(e) {
    input[e.keyCode] = true;
}

function onKeyUp(e) {
    input[e.keyCode] = false;
}

function initGame() {
    ...
    
    document.addEventListener('keydown', onKeyDown);
    document.addEventListener('keyup', onKeyUp);
}

paddle.update = function() {
      if (input[LEFT_KEY]) {
          this.position.x -= 8;
      } else if (input[RIGHT_KEY]) {
          this.position.x += 8;
      }
      
      // 挡板撞到画布边缘时
      if (this.position.x < 0) {
          this.position.x = 0;
      } else if (this.position.x > CANVAS_WIDTH - this.width) {
          this.position.x = CANVAS_WIDTH - this.width;
      }
}
```

**显示分数 / 生命值** 

canvas 提供了在指定位置渲染文本的功能，可以绘制实体文字或者文字边框，改变文字的样式。

```JavaScript
function drawGame() {
    ...
    ctx.font = "16px Arial";
    ctx.fillStyle = "#0095DD";
    // 分数
    ctx.fillText("Score: " + score, 8, 20);
    // 生命值
    ctx.fillText("Lives: " + lives, CANVAS_WIDTH-65, 20);

    if (ball.onPaddle) {
        ctx.font = '20px Arial';
        const text = 'Press shift to launch the ball!';
        // 预测量文本的尺寸
        const metrics = ctx.measureText(text);
        ctx.fillText(text, CANVAS_WIDTH/2 - metrics.width/2, CANVAS_HEIGHT/2);
  }
}
```

**给小球落体加上重力加速度**

当速率不变时，小球进行的是匀速运动，我们在小球下落且没有发生碰撞的时候，可以为它加上一个向下的加速度，这样小球下落时速度会越来越快，向上时速度会逐渐减慢，从而达到模仿重力的效果。

```JavaScript
const GRAVITY = 0.05;

ball.update = function() {
    ...
    // 如果没有发生碰撞
     this.velocity.y += GRAVITY;
}
```

## 最后

通过实现这个 demo 可以对 2d canvas 的基本知识进行了解，顺便锻炼下解决梳理问题的思维。偶尔写些跟工作没有关系的东西还是一件蛮有趣的事情，希望下次可以写一点更炫酷的东西！

具体代码可以查看 https://codepen.io/qyhhh/pen/mdBWWbz