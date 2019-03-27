### JQuery中动画队列

#### 动画队列常用方法

- queue([queueName,]callback):将callback动画数添加到当前jQuery对象里所有DOM元素的动画函数队列的尾部。
- queue([queueName,]naeQueue):用newQueue动画函数队列代替当前jQuery对象里所的DOM元素的动画函数队列。
- stop([clearQuery],[gotoEnd]):停止当前正在运行的动画
  - clearQueue：是否清除未执行的动画队列，default: false
  - jumpToEnd：是否展示当前动画最终状态，default: false
- dequeue():执行动画函数队列头的第一个动画函数，并将该动画函数移出队列。
- clearQueue([queueName]):清空动画函数队列中的所有动画函数。
- .finish():停止当前动画，并清除动画队列中所有未完成的动画,最终展示动画队列最后一帧的最终状态

#### 举例

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <script src="http://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
    <title>JS Bin</title>
    <style>
        #box {
            position:relative;
            height: 100px;
            width: 100px;
            background-color: yellow;
        }
    </style>
</head>
    <button id="btn">开始动画</button>
    <button id="btn1">clearQueue</button>
    <button id="btn2">finish</button>
    <button id="btn3">stop</button>
    <button id="btn4">stop(true)</button>
    <button id="btn5">stop(true,true)</button>
    <span id="result"></span>
    <div id="box"></div>
    <script>
        $('#btn').click(function () {
            setInterval(function () {
                $('#result').html('队列数是:' + $('#box').queue().length)
            }, 1000)
            $('#box').animate({
                    'left': '100px'
                }, 1000)
                .animate({
                    'width': '200px'
                }, 1000);
            $('#box').queue(function () {
                $(this).css('height', '200px');
                $(this).dequeue();
            },1000)
            $('#box').animate({
                    'left': '0'
                }, 1000)
                .animate({
                    'width': '100px',
                    'height':'100px'
                }, 1000);
        });

        $('#btn1').click(function () {
            $('#box').clearQueue();
        })
        $('#btn2').click(function () {
            $('#box').finish();
        })
        $('#btn3').click(function () {
            $('#box').stop()
        })
        $('#btn4').click(function () {
            $('#box').stop(true)
        })
        $('#btn5').click(function () {
            $('#box').stop(true, true)
        })
    </script>

</body>

</html>
```

实现效果：

http://js.jirengu.com/kanizoyoja

