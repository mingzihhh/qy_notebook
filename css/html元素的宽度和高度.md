## html元素的宽度和高度

![](https://upload-images.jianshu.io/upload_images/15055044-187192118051e8de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 一、div中里是文字的高度
- **字体设计**
  1. 字和字是通过基线对齐的
  2. 每种字体设计时都会有建议行高，由设计师设定

- 当div由文字撑开时，div高度=字体大小的默认行高，可以通过line-height改变
  **当div里只有内联元素时，div高度由内联元素行高决定**

- 内联元素之外的回车空格等于不存在，内联元素之间的回车/空格/看不见的元素缩成一个空格
  <-两个inline-block之间的空格
  <- 消不掉，可以用float，注意清除浮动

### 二、div中里是div的高度
由内部div的width，padding，margin影响，注意margin合并

### 三、div里又有内联又有div

- **div的高度是由它内部文档流元素的高度总和决定的（不是相等）**
  文档流：内联元素从左到右依次排列，宽度不够另起一行；块级元素从上到下排列
  脱离文档流：float /position:absolute/position:fixed 高度不参与父元素计算

### 四、div里的div绝对居中
1. 父div不定高的时候，可以设上下padding相同撑起来
2. 父元素设置flex
   ![](https://upload-images.jianshu.io/upload_images/15055044-0cd30467a3448c55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 五、文字垂直居中

1. 可以使height=line-height，但只能使用在只有一行文字的情况，多行文字就出bug
2. 自适应居中：只设置line-height和上下padding
3. 尽量不要写高度，使其自适应

### 六、1:1div

padding-top:100%代表和宽度的长度一样
![](https://upload-images.jianshu.io/upload_images/15055044-87ab2c6a0cf145b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)