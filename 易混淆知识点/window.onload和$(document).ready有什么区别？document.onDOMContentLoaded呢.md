### `window.onload`和`$(document).ready`有什么区别？`document.onDOMContentLoaded`呢?

window.onload和$(document).ready都是页面加载时我们就去执行一个函数或者动作。
 **区别**

1. 执行时间不同
    window.onload必须等到页面内包括图片的所有元素（包括元素的关联文件）加载完毕后再去执行，即JavaScript此时才可以访问网页中的任何元素。
    $(document).ready()时DOM结构回执完毕后就执行，不必等到加载完毕。此时，网页的所有元素对jQuery而言都是可以访问的，但是，这并不意味着这些元素关联的文件都已经下载完毕。
2. 编写个数不同
    window.onload不能同时编写多个，如果有多个window.onload方法，只会执行一个
    $(document).ready()可以同时编写多个，并且可以得到执行
3. 简化写法
    window.onload没有简化写法
    $(document).ready(function(){})可以简写成$(function(){});



document.onDOMContentLoaded在页面中触发[DOMContentLoaded]事件时触发。此时，文档被加载和解析，并且DOM被完全构造，但链接的资源（例如图像，样式表和子帧）可能尚未被加载。