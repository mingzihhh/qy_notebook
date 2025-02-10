### 安卓端解决 input 键盘弹出导致页面压缩变形

写个监听resize事件（浏览器窗口大小调整时触发）

```
let Height = $('body').height();
$(window).resize(function() {
    $('body').height(Height);
});
```

当键盘弹出的时候，固定`body`高度不变。