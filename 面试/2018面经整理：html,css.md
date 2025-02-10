# html&css篇

## ==语义化的html==

#### 一、什么是语义化的HTML？

语义化的HTML就是正确的标签做正确的事情，段落就写 p 标签，标题就写 h1 标签，文章就写article标签，视频就写video标签，等等。段落写成p标签，标题写成h1~h6

能够便于开发者阅读和写出更优雅的代码的同时让网络爬虫很好地解析。

#### 二、为什么要做到语义化？

1、有利于SEO，有利于搜索引擎爬虫更好的理解我们的网页，从而获取更多的有效信息，提升网页的权重。

2、在没有CSS的时候能够清晰的看出网页的结构，增强可读性。

3、便于团队开发和维护，语义化的HTML可以让开发者更容易的看明白，从而提高团队的效率和协调能力。

4、支持多终端设备的浏览器渲染。

#### 三、语义化HTML该怎么做呢？

在做前端开发的时候要记住：HTML 告诉我们一块内容是什么（或其意义），而不是它长的什么样子，它的样子应该由CSS来决定。（结构与样式分离！）

写语义化的 HTML 结构其实很简单，首先掌握 HTML 中各个标签的语义，在看到内容的时候想想用什么标签能更好的描述它，是什么就用什么标签。

```
<h1>~<h6> ，作为标题使用，并且依据重要性递减，<h1> 是最高的等级。

<p>段落标记，知道了 <p>
作为段落，你就不会再使用 <br />
来换行了，而且不需要 <br />
来区分段落与段落。<p>
中的文字会自动换行，而且换行的效果优于 <br />。
段落与段落之间的空隙也可以利用 CSS 来控制，很容易而且清晰的区分出段落与段落。

<ul>、<ol>、<li>，<ul> 无序列表，这个被大家广泛的使用，<ol>
有序列表也挺常用。在 web 标准化过程中，<ul>
还被更多的用于导航条，本来导航条就是个列表，这样做是完全正确的，
而且当你的浏览器不支持 CSS 的时候，导航链接仍然很好使，只是美观方面差了一点而已。

<dl>、<dt>、<dd>，<dl> 就是“定义列表”。比如说词典里面的词的解释、定义就可以用这种列表。

<em>、<strong>，<em> 是用作强调，<strong> 是用作重点强调。

<q>也不仅仅只是为文字增加双引号，而是代表这些文字是引用来的。

<table>、<td>、<th>、<caption>， (X)HTML中的表格不再是用来布局。
```

补充：网络爬虫，SEO等概念

#### SEO：Search Engine Optimization

——搜索引擎优化，这是一种利用搜索引擎的搜索规则，采取优化策略或程序，提高网站在搜索结果中的排名。

#### 网络爬虫：

又称网络蜘蛛、网络机器人，是一种搜索引擎用于自动抓取网页资源的程序或者说叫机器人。从某一个网址为起点，去访问，然后把网页存回到数据库中，如此不断循环，一般认为搜索引擎爬虫都是靠链接爬行的，所以管他叫爬虫。这个只有开发搜索引擎才会用到。对于网站而言，只要有链接指向我们的网页，爬虫就会自动提取我们的网页。

#### h5新增标签

![image](![](https://ws1.sinaimg.cn/large/006tNbRwly1fw5gvtbappj30qo0u8n1s.jpg)

#### 你用过哪些 HTML 5 标签？

（想知道平时有没有写H5的页面）

内容：header,main,footer,artical，aside,section,figure

在写音乐播放器的时候用过new Audio，使用audio的构造器构造，自动播放autoplay，currentTime属性，duration歌曲长度，还有方法play()，pause()等等



#### meta viewport 是做什么用的，怎么写？

近几年手机的发展是前端的工作中心从电脑桌面转移到手机上，所以会有viewport的出现。使手机上页面不能缩放

<meta name="viewport" content="width=device-width, initial-scale=1.0">
meta：表示那些不能由其他html元素表示的元数据信息

name：把 content 属性关联到一个名称。

width：控制 viewport 的大小，可以指定的一个值，如果 600，或者特殊的值，如 device-width 为设备的宽度（单位为缩放为 100% 时的 CSS 的像素）。
height：和 width 相对应，指定高度。
initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。
maximum-scale：允许用户缩放到的最大比例。
minimum-scale：允许用户缩放到的最小比例。
user-scalable：用户是否可以手动缩放

<https://www.cnblogs.com/lihuijuan/p/9589978.html>



#### h5是什么

移动端页面

- [HTML5](https://link.juejin.im/?target=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTML5) 页面，是按照 HTML5 规范实现的页面，其中用到了许多新的特性。

- [H5](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTML%2FElement%2FHeading_Elements)，是HTML标签中的 标题标签，代表五级标签。

  

## ==position==

#### 1.position中  relative和absolute，fix的区别

   (static:默认，正常文档流）

（stiky: 粘性定位，元素在跨越特定阈值前为相对定位，之后为固定定位。）

- fixed 属性会固定不动，不会随着屏幕的滚动滚动。相对浏览器视口定位。
- absolute：
  1、使用绝对定位的盒子以它的“最近”的一个“已定位”（position属性被设置，不是static）的“祖先元素”为基准进行偏移。如果没有已经定位的祖先元素，那么会以body进行定位。
- 2、绝对定位的框从标准流中脱离，这意味着它们对其后的兄弟盒子的定位没有影响，其他的盒子就好像这个盒子不存在一样。
  3.生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。
- relative:这个是相对自身静态的位置，定位之后，他原来的位置不清空，其他元素不能占用，会影响其他盒子的位置。不脱离文档流

#### 两种盒模型分别说一下

标准盒子模型：宽度=内容的宽度（content）+ border + padding + margin
低版本IE盒子模型：宽度=内容宽度（content+border+padding）+ margin

IE８＋

->为什么border-box比content-box好，因为写起来很方便，更符合ui设计者和前端设计者的逻辑



## ==css垂直水平居中（不知宽高）==

水平居中

- 行内元素: `text-align: center`
- 块级元素: `margin: 0 auto`
- `absolute + transform`/负margin
- `flex + justify-content: center`

垂直居中

- `line-height: height`
- `absolute + transform`/负margin
- `flex + align-items: center`
- `table-cell` + vertical-align : middle
- 上下padding相等（不定高）

水平垂直居中

- `absolute + transform`
- `flex + justify-content + align-items`





## ==margin折叠==

margin 折叠--margin值合并 外边距塌陷

##### **两个或多个毗邻的普通流中的块元素垂直方向上的 margin 会折叠**

代码

```
<!DOCTYPE html>
 <html>
 <head lang="en">
       <meta charset="UTF-8">
      <title>测试</title>
    </head>
 <style>
        *{
            margin: 0;
            padding: 0;
        }
        #father{
            width: 2000px;
            height: 2000px;
            background: #0016d9;
            overflow: hidden;
                   }
        #first-child{
            margin-top: 20px;
            background: chocolate;
            width: 60px;
            height: 60px;
        }
        #second-child{
            background: chartreuse;
            width: 60px;
            height: 60px;
            margin-bottom: 20px;
        }
        #three-child{
            margin-top:40px;
            background: fuchsia;
            width: 60px;
            height: 60px;
            display: inline-block;
        }

     </style>
<body>
    <div id="father">
        <div id="first-child">box1</div>
        <div id="second-child">box2</div>
        <div id="three-child">box3</div>
</div>
 </body>
 </html>
```

1. 如何取消合并？
   - border、padding
   - 设定BFC，但不是所有因素都可以
   >1. 浮动元素、inline-block 元素、绝对定位元素的 margin 不会和垂直方向上其他元素的 margin 折叠**（注意这里指的是上下相邻的元素）**
   >2. **创建了块级格式化上下文的元素，不和它的子元素发生 margin 折叠**（注意这里指的是创建了BFC的元素和它的子元素不会发生折叠）

   补充说明：
   我们都知道触发BFC的因素是**float（除了none）、overflow（除了visible）、display（table-cell/table-caption/inline-block）、position（除了static/relative）**。

   显然相邻元素不发生折叠的因素是**触发BFC因素的子集**，也就是说如果我为上下相邻的元素设置了overflow:hidden，虽然触发了BFC，但是上下元素的上下margin还是会发生折叠。

   这个问题其实和BFC并没有太大的关系，希望大家不要滥用BFC，要知道BFC不是全能，创建BFC的初衷只是为了**让元素本身（包括它的子元素）能够正确的计算自己的宽高**。
   

   不发生折叠的触发因素是**浮动元素、inline-block 元素、绝对定位元素**，这个只是创建BFC因素的子集，但并不能说明创建了BFC的元素就不会发生折叠，因为BFC还可以用overflow:hidden来创建。相反如果父元素触发了BFC，那么他的块级子元素反而会发生折叠。

## ==清除浮动==

- 通过增加尾元素清除浮动

```
.clearfix:after {
    content:'';
    clear: both;
    display: block;
}
```

- 创建父级 BFC
- 父级设置高度

## ==列举一些常见的块级元素和行内元素==

- 行内元素有：title  lable  span  br  a  em  b  i   strong
- 块级元素有：body  form  select  textarea  h1-h6  table  button  hr  p  ol  ul  dl   div
- 行内块元素常见的有： img  input  td  

## ==CSS的优先级【class，id，内联，!important】==

```
!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性

（!important > 行内样式 > #id > .class > tag > * > 继承 > 默认）

选择器 从右往左 解析
类选择器和属性选择器优先级相同，谁在后面谁的优先级较高

注意：通用选择器（*），子选择器（>）和相邻同胞选择器（+），他们的权值是0，所以两个通配符选择器和一个通配符选择器的权值是相同的

```

1. 内联样式表的权值为 1000，就是在元素内写style
2. ID 选择器的权值为 100
3. Class 类选择器的权值为 10
4. HTML 标签选择器的权值为 1

## ==对inline元素设置padding、margin有效吗？==

- inline元素设置width和height无效

- 设置margin-left、margin-right、padding-left、padding-right有效

- 设置margin-top、margin-bottom、padding-top、padding-bottom无效

- 为什么在行内元素的实际使用中，上下padding好像是生效的？

  当我们使用内边距时，只有左右方向有效；当我们设置四个方向的内边距时，对于竖直方向的内边距该行内元素的显示效果是增大了，但其实设置是无效的，对周围元素无任何影响，即**对实际布局没有影响**。
  注意，**img是特例**，它虽然是行内元素，但也是置换元素，所以它的性质不同于行内元素。对于img设置padding和margin都是有效的。

## ==line-heghit==

#### line-height的继承【带单位和不带单位】

line-height是可以继承的，所以子元素就可以不用重复定义line-height了。我们一般也会在后面带上单位(如:line-height:22px; 或是line-height:1.4em;)，但line-height会给人带来麻烦的地方也就是这个继承和后面加的单位。

##### 【Css高级应用】line-height带单位与不带单位的区别

有的时候，我们为了实现单行文字的垂直居中，会给line-height一个和height相同的固定的值；有的时候，我们为了调整特定的某段文字 的行间距，通常会考虑使用百分比或者相对尺寸的em。
或许是习惯，于是我们都习惯了line-height是要有单位的。
这些情况下，我们都不需要考虑 line-height的继承,也不会发现任何问题，当然后我们在使用到line-height继承的时候，就会发现问题的所在。
例如下面的代码：

1、样式

```
<style type="text/css">
 .test{line-height:1.4em; font-size:12px;}
 span{font-size:30px; font-weight:bold;}
 </style>
```

2、HTML结构

```
<div class="test">
    <span> 白培铭先生于1960年出生于中国台湾。<br/>
            毕业于中国台湾省清华大学核物理系，<br/>
    </span>
    之后留学于美国加州大学伯克利分校和密西根大学，获得双硕士学位。<br/>
    在工作之后，凭着对营销领域的浓厚兴趣，他又考入密执安大学深造<br/>
</div>
```

看过例子后，你会发现，只要有单位的line-height继承，都发生了重叠的现象。那到底这是什么原因导致的呢？

- 如果line-height属性值有单位，那么继承的值则是换算后的一个具体的px级别的值；
- 而如果属性值没有单位，则浏览器会直接继承这个 “因子（数值）”，而非计算后的具体值，此时它的line-height会根据本==身的font-size==值重新计算得到新的line-height 值。

## ==rem和em的区别？==

css中单位em和rem的区别
在css中单位长度用的最多的是px、em、rem，这三个的区别是：

- px是固定的像素，一旦设置了就无法因为适应页面大小而改变。
- em和rem相对于px更具有灵活性，他们是相对长度单位，意思是长度不是定死了的，更适用于响应式布局。
- 对于em和rem的区别一句话概括：em相对于父元素，rem相对于根元素。
- rem中的r意思是root（根源），这也就不难理解了。

em

- 子元素字体大小的em是相对于父元素字体大小
- 元素的width/height/padding/margin用em的话是相对于该元素的font-size

rem

- rem是全部的长度都相对于根元素，根元素是谁？<html>元素。通常做法是给html元素设置一个字体大小，然后其他元素的长度单位就为rem。



## ==BFC==

块级格式上下文Block Formatting Context（简称BFC ），是一个独立的渲染区域，让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。

当元素满足一下任何一个条件是都会产生一个BFC:

- float属性取值不是“none”
- overflow属性取值不是“visible”
- display的值为 “table-cell”, “table-caption”, or “inline-block”中的任何一个
- position的值不为 “static” 或 “relative”中的任何一个
- 行内块元素
- 弹性元素（display为 flex 或 inline-flex元素的直接子元素）



规则:

- 属于同一个 BFC 的两个相邻 Box 垂直排列
- 属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠
- BFC 中子元素的 margin box 的左边， 与包含块 (BFC) border box的左边相接触 (子元素 absolute 除外)
- BFC 的区域不会与 float 的元素区域重叠
- 计算 BFC 的高度时，浮动子元素也参与计算
- 文字层不会被浮动层覆盖，环绕于周围



应用:

- 阻止垂直外边距重叠
- 可以包含浮动元素 —— 清除内部浮动(清除浮动的原理是两个`div`都位于同一个 BFC 区域之中)
- 自适应两栏布局
- 可以阻止元素被浮动元素覆盖



## flex

- 在父内容里面**垂直居中**一个块内容。
- 使容器的所有子项占用**等量**的可用宽度/高度，而不管有多少宽度/高度可用。
- 使多列布局中的所有列采用**相同的高度**，即使它们包含的内容量不同。

常用属性：

**父元素**

display: flex;

flex-direction: row;

justify-content: center;

```
flex-start | flex-end | center | space-between | space-around | space-evenly;
```

align-items: center;

```
stretch | flex-start | flex-end | center | baseline
```

flex-wrap: nowrap;

flex-flow: `flex-direction`和`flex-wrap`的缩写

子元素：

flex: 0 1 auto;

flex-grow: 0;		是否在默认宽度之和小于容器的时候发生拉伸

flex-shrink: 1		是否在默认宽度之和大于容器的时候发生收缩

flex-basis: auto;		 flex 元素在主轴方向上的初始大小

order: 0;  

align-self: auto; 控制单独某一个flex子项的垂直对齐方式

```
auto | flex-start | flex-end | center | baseline | stretch;
```



### link 与 @import 的区别

- 功能：`link`功能较多，可以定义 RSS，定义 Rel 等作用，而`@import`只能用于加载 css
- 加载顺序：当解析到`link`时，页面会同步加载所引的 css，而`@import`所引用的 css 会等到页面加载完才被加载
- 兼容性：`@import`需要 IE5 以上才能使用
- 引入：ink 支持使用 js 控制 DOM 去改变样式，而@import 不支持



### 层叠上下文

元素提升为一个比较特殊的图层，在三维空间中 **(z轴)** 高出普通元素一等。

规则：

谁大谁上，后来居上

触发条件

- 根层叠上下文(`html`)

- `position`

- css3属性 

  css3属性造成的层叠上下文跟z-index:auto同一个层级，并且遵循“后来居上”原则

  - `flex`
  - `transform`
  - `opacity`
  - `filter`
  - `will-change`
  - `-webkit-overflow-scrolling`

层叠等级：层叠上下文在z轴上的排序

- 在同一层叠上下文中，层叠等级才有意义
- `z-index`的优先级最高



## 前端动画

- animation过渡动画

  控制关键帧，模仿属性变化，功能更多，动画结束触发aimationed

- transition过渡动画

  控制属性变化，需要明确开始和结束状态

  需要事件触发，一次性

  动画结束触发aimationed

- JS原生控制DOM位置

  requestAnimationFrame

- canvas绘制动画

- 动画属性: 尽量使用动画属性进行动画，能拥有较好的性能表现

  - `translate`
  - `scale`
  - `rotate`
  - `skew`
  - `opacity`
  - `color`



