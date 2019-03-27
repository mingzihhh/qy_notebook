# requestAnimationFrame

requestAnimationFrame 是浏览器用于定时循环操作的一个接口，类似于 setTimeout，主要用途是按帧对网页进行重绘。设置这个 API 的目的是为了让各种网页动画效果（DOM 动画、Canvas 动画、SVG 动画、WebGL 动画）能够有一个统一的刷新机制，从而节省系统资源，提高系统性能，改善视觉效果。代码中使用这个 API，就是告诉浏览器希望执行一个动画，让浏览器在下一个动画帧安排一次网页重绘。

**requestAnimationFrame代替setTimeout**

requestAnimationFrame跟屏幕刷新同步，大多数屏幕的刷新频率都是60Hz，对应的requestAnimationFrame大概每隔16.7ms触发一次，如果屏幕刷新频率更高，requestAnimationFrame也会更快触发。基于这点，在支持requestAnimationFrame的浏览器还使用setTimeout做动画显然是不明智的。

在不支持requestAnimationFrame的浏览器，如果使用setTimeout/setInterval来做动画，最佳延迟时间也是16.7ms。 如果太小，很可能连续两次或者多次修改dom才一次屏幕刷新，这样就会丢帧，动画就会卡；如果太大，显而易见也会有卡顿的感觉。

有趣的是，第一次触发requestAnimationFrame的时机在不同浏览器也存在差异，Edge中，大概16.7ms之后触发，而Chrome则立即触发，跟setImmediate差不多。按理说Edge的实现似乎更符合常理。

http://www.alloyteam.com/2016/05/javascript-timer/

