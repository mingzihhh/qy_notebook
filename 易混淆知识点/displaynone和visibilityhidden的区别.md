# display:none和visibility:hidden的区别

如果给一个元素设置了display: none，那么该元素以及它的所有后代元素都会隐藏，**不占据空间**

如果给一个元素设置了visibility: hidden也可以隐藏这个元素，但是隐藏元素仍**占据空间**，也就是说虽然元素不可见了，但是仍然会影响页面布局。

### display: none与visibility: hidden的区别

1、visibility具有继承性，给父元素设置visibility:hidden;子元素也会继承这个属性。但是如果重新给子元素设置visibility: visible,则子元素又会显示出来。这个和display: none有着质的区别

2、visibility: hidden不会影响计数器的计数，如图所示，有序列表中，visibility: hidden虽然让一个元素不见了，但是其计数器仍在运行。这和display: none完全不一样

```
  <body>
        <div>
            <strong>给元素设置visibility:hidden样式</strong>
            <ol>
                <li>元素1</li>
                <li style="visibility:hidden;">元素2</li>
                <li>元素3</li>
                <li>元素4</li>
            </ol>
        </div>
        <div>
            <strong>给元素设置display:none样式</strong>
            <ol>
                <li>元素1</li>
                <li style="display:none;">元素2</li>
                <li>元素3</li>
                <li>元素4</li>
            </ol>
        </div>
    </body>
```

![](https://img-blog.csdn.net/20180624222342801)

3、CSS3的transition支持visibility属性，但是并不支持display，由于transition可以延迟执行，因此可以配合visibility使用纯css实现hover延时显示效果。提高用户体验。