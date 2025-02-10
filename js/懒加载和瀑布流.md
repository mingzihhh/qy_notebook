## 瀑布流

只展示用户看到的图片，随着滚动加载，缓解浏览器压力，增强用户体验

1. 在所有图片展示处先放一张默认图片，只需要请求一次，其余走缓存；自定义属性data-src设置真实地址

2. 检查图片是否出现在用户视野，是则加载

   - 滚动距离+窗口高度 >= 图片元素相对于顶点的距离

     ![img](http://upload-images.jianshu.io/upload_images/10142252-33520b7f5f7a93f8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/322/format/webp)

   - src=data-src

3. 优化：

   节流，已加载的图片不需要重复加载



## 瀑布流布局

宽度一致，而高度不一样的布局就是瀑布流布局

实现：不断地补最短的缺口

1. 确定列数

   Math.floor($('.waterfall').width()/$('.waterfall img').outerWidth(true))

   outerWidth() 方法返回第一个匹配元素的外部宽度。该方法包含 padding 和 border。布尔值，规定是否包含 margin。

2. 摆放顺序

   按图片加载顺序判断摆放

3. 如何存储高度

   用数组存储当前每一列高度之和，比较找到最短列，设置图片top和left，将该图片高度加入此列重新计算该列高度

   