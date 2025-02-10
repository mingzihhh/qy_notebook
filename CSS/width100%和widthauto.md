## width:100%和width:auto

#### 一、width:100%

1. 当设置"box-sizing:content-box"时（默认），子元素设置宽度的百分比是指**子元素内容区域**相对于**父元素内容区域**；

>父元素 width=子元素 width
>
>子元素width随着父级width自动变化
>
>增加子元素的padding和margin之后，width:100%很容易使子元素溢出父元素。

2. 当设置"box-sizing:border-box"时，子元素设置宽度的百分比是指子元素**整个盒子区域**相对于父元素**内容区域**；

>父元素 width=子元素 margin-left + border-left + padding-left +width + padding-right + border-right + margin-right

3. 如果想要正确使用"width:100%"这一属性，一定要设置"box-sizing:border-box",否则会造成子元素溢出。

#### 二、width:auto

width的默认值是'auto'

1. 块级元素宽度默认是100%于父级容器的，**fill-available**

>父元素 width=子元素 margin-left + border-left + padding-left +width + padding-right + border-right + margin-right
>width自己根据情况伸缩

2. 浮动，绝对定位，inline-block水平元素或table表格，**fit-content**

3. table-layout为auto的表格中，空间收缩到最小，文字能断就断，中文是随便断的，英文单词不能断，**min-content**

上面1~3情况，除非有明确的width相关设置，否则尺寸都不会主动超过父级容器宽度的

4. 存在一些特殊情况，例如，连续的英文数字，好长好长；或者内联元素被设置了white-space:nowrap，会超出容器限制，**max-content**

参考资料：
https://www.jianshu.com/p/7d565f14f98e
https://www.zhangxinxu.com/wordpress/2018/07/css-width-auto/