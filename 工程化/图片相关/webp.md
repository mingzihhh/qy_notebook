- 更小的文件大小，实现更高的压缩率。根据Google的数据，**WebP的文件体积平均比JPEG小25%-34%**
- 支持透明度，可创建带有透明背景的图像。在处理透明图片时，**WebP比PNG的文件体积小了将近26%** 
- 支持动态图像，可创建动画效果。
- 提供良好的图像质量。



#### 如何处理兼容性问题

**使用 `<picture>` 标签进行格式兜底**

```
<picture>
    <!-- 优先加载 WebP 图片 -->
    <source srcset="image.webp" type="image/webp">
    <!-- 如果 WebP 不被支持，则加载 JPG 图片 -->
    <source srcset="image.jpg" type="image/jpeg">
    <!-- 兜底的 img 标签，用于不支持 <picture> 的浏览器 -->
    <img src="image.jpg" alt="Fallback image">
</picture>
```

picture 标签会根据浏览器是否支持特定图片格式来选择合适的图片。



**使用 JavaScript 动态监测 webp 格式支持**

使用 JavaScript 来检测浏览器是否支持 WebP，动态加载合适的图片格式。动态加载一张 base64 编码的非常小的 WebP 图片，并检查加载是否成功。如果加载成功，则说明浏览器支持 WebP；否则，则不支持。

```
function supportsWebP(callback) {
  var webP = new Image();
  webP.onload = function () {
    // 加载成功，支持 WebP
    callback(true);
  };
  webP.onerror = function () {
    // 加载失败，不支持 WebP
    callback(false);
  };
  webP.src = "data:image/webp;base64,UklGRhYAAABXRUJQVlA4TAYAAAAvAAAAAAfQ//73v/+BiOh/AAA=";
}
```



**服务端/CDN 处理**

服务器端根据请求的 `Accept` 头来返回合适的图片格式，如果检测到浏览器支持 WebP，CDN 将返回 WebP 格式图片；否则返回 JPEG 或 PNG。

```
Accept: image/webp
```

