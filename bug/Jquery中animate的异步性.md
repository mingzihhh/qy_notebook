## Jquery中animate的异步性

jquery 的animate 是异步的，一般原理都是利用setTimeout之类的来定时延迟执行，显然animate的回调到点会放到任务队列里

一般回调函数才考虑异步，同一级别层面的调用应该都是同步的也就是顺序执行的。也就是说在animation的回调函数中，才会异步执行，其他地方就是同步。

```
$(document).ready(function () {
    var mark2 = true;
    if (mark2) {
        move(1);
        console.log('运行结束')
    }
})

function move() {
    console.log("move start")
    $(".box").animate({
            width: 50,
            height: 50,
            opacity: 30,
            left: 200,
            top: 200
        }, {duration: 1500,
            start: function () {
                console.log("animate start")
            },
            complete: function () {
                console.log("animate end")
            }
        })
    console.log("move end")
}
```

输出：

![1550927484449](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1550927484449.png)

