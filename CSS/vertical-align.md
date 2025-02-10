## vertical-align

用在文本和与文本相邻元素的垂直方向上的对齐问题

https://segmentfault.com/a/1190000002668492

#### 要求

用于垂直对齐inline元素，也就是`display`值为`inline`和`inline-block`的元素。`inline-table`的元素不在本文的讨论范围内。



#### line-box

`inline`元素一个挨着一个的摆放在行内，当行内元素太多的情况下，一个新行会被创建出来，这些行也叫做`line--box`。

- 有一个区域叫做line-box，垂直方向上的对齐都是发生在这个区域里面，它有baseline，有文字盒，有上下边界。
- inline元素也有baseline和上下边界，inline元素是需要对齐的对象。
- 在行的开头添加一个字母，比如'x'，这个字母的下边界默认就是baseline的位置。



#### baseline

`inline-block`元素的外边缘就是`margin-box`的边缘。
`inline-block`元素的baseline的位置要看该元素有没有处于正常流之内的内容。

1. 在有处于正常流内容的情况下，`inline-block`元素的baseline就是最后一个作为内容存在的元素的baseline，这个元素的baseline的确定就要根据他自身来确定了。

2. 在overflow属性不为visible的情况下，baseline就是margin-box的下边界了。

3. 内容为空的情况下baseline还是margin-box的下边界。

   ![1552402594209](C:\Users\QY\AppData\Roaming\Typora\typora-user-images\1552402594209.png)



line-box中的所有元素都会影响到baseline的位置。在满足vertical-align数值对齐的条件下，line-box的高度会自我调整。

假设，一个元素按某种方式垂直对齐了，但是这种对齐方式会引起baseline的移动，又因为大部分的垂直对齐方式(除了top和bottom)和baseline有关，因此这个元素的垂直方向对齐的行为会引起该line-box内其他元素位置的调整。



两个建议，如果vertical-align属性不起作用，那么问问自己：

- line-box的上下边缘以及baseline的位置在哪里？
- inline元素的上下边缘以及baseline的位置在哪里？

