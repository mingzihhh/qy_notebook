## 块级元素，行内元素和inline-block

### 一、基础知识

#### 1. 块元素的特点：

a.总是在新行上开始；
b.高度、行高以及外边距和内边距都可控制；
c.宽度默认是它容器的100%，除非设定一个宽度；
d.它可以容纳内联元素和其他块元素。

**常见的块元素**：
address - 地址
blockquote - 块引用
center - 举中对齐块
dir - 目录列表
div - 常用块级容易，也是css layout的主要标签
dl - 定义列表
fieldset - form控制组
form - 交互表单
h1 - 大标题
h2 - 副标题
h3 - 3级标题
h4 - 4级标题
h5 - 5级标题
h6 - 6级标题
hr - 水平分隔线
isindex - input prompt
menu - 菜单列表
noframes - frames可选内容（对于不支持frame的浏览器显示此区块内容）
noscript - 可选脚本内容（对于不支持script的浏览器显示此内容）
ol - 排序列表
p - 段落
pre - 格式化文本
table - 表格
ul - 非排序列表

#### 2. 行内元素的特点：

a.和其他元素都在同一行；
b.行内元素设置width无效，height无效(可以设置line-height)，margin上下无效，padding上下无效
c.宽度(width)就是它的文字和图片的宽度，不可改变；
d.行内元素只能容纳文本或者其他行内元素。
e.可以通过改变行高或者水平边框、内边距或外边距修改行内元素尺寸

**常见的内联元素：**
a - 锚点
abbr - 缩写
acronym - 首字
b - 粗体（不推荐）
bdo - bidi override
big - 大字体
br - 换行
cite - 引用
code - 计算机代码（在引用源码的时候需要）
dfn - 定义字段
em - 强调
font - 字体设定（不推荐）
i - 斜体
img - 图片
input - 输入框
kbd - 定义键盘文本
label - 表格标签
q - 短引用
s - 中划线（不推荐）
samp - 定义范例计算机代码
select - 项目选择
small - 小字体文本
span - 常用内联容器，定义文本内区块
strike - 中划线
strong - 粗体强调
sub - 下标
sup - 上标
textarea - 多行文本输入框
tt - 电传文本
u - 下划线
var - 定义变量

#### 3.  块级元素和行内元素之间的转换：

- display
  块元素默认display:block

  行内非替换元素(a,span)默认为display：inline

  行内替换元素(input)默认为display:inline-block;

  a.display:none;不显示该元素，也不会保留该元素原先占有的文档流位置。
  b.display:block;转换为块级元素。
  c.display:inline;转换为行内元素。
  d.display:inline-block;转换为行内块级元素。

- float
  当把行内元素设置完float:left/right后，该行内元素的display属性会被赋予block值，可以设置宽高等，且拥有浮动特性。块级元素也呈现出inline-block的特性，宽度会根据内容收缩。

- position
  当为行内元素进行定位时，position:absolute与position:fixed.都会使得原先的行内元素变为块级元素。

#### 4. inline-block 

inline-block 的元素（如input、img)既具有 block 元素可以设置宽高的特性，同时又具有 inline 元素默认不换行的特性。当然不仅仅是这些特性，比如 inline-block 元素也可以设置 vertical-align（因为这个垂直对齐属性只对设置了inline-block的元素有效） 属性。

**HTML 中的换行符、空格符、制表符等合并为空白符，字体大小不为 0 的情况下，空白符自然占据一定的宽度，连续使用inline-block 会产生元素间的空隙。**

### 二、相关问题

#### 1. 为什么在行内元素的实际使用中，上下padding好像是生效的？

当我们使用内边距时，只有左右方向有效；当我们设置四个方向的内边距时，对于竖直方向的内边距该行内元素的显示效果是增大了，但其实设置是无效的，对周围元素无任何影响，即**对实际布局没有影响**。
注意，**img是特例**，它虽然是行内元素，但也是置换元素，所以它的性质不同于行内元素。对于img设置padding和margin都是有效的。

#### 2. 如何取消inline-block产生的间隙

[去除inline-block元素间间距的N种方法](http://www.zhangxinxu.com/wordpress/?p=2357)

参考资料：
[说说行内元素和块级元素](https://www.jianshu.com/p/d69878549d92)
[给行内元素设置padding和margin是否有效](https://www.codetd.com/article/3473871)
 [行内元素和块级元素的具体区别是什么？inline-block是什么？（面试题目）](https://www.cnblogs.com/iceflorence/p/6626187.html)