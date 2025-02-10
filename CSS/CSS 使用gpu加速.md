## css使用gpu加速

CSS的 animations，transforms和transition s并不会自动开启GPU加速，取而代之的是利用缓慢的浏览器软件来渲染执行。那么，我们怎么样才能让浏览器切换到GPU模式呢？很多浏览器提供的一些触发的CSS规则。

DOM树和CSS结合后形成浏览器构建页面的渲染树。渲染树中包含了大量的渲染元素，每一个渲染元素会被分到一个图层中，每个图层又会被加载到GPU形成渲染纹理，而图层在GPU中 transform 是不会触发 repaint 的，这一点非常类似3D绘图功能，最终这些使用 transform 的图层都会由独立的合成器进程进行处理。

**启用GPU加速，避开重排和重绘的环节，将进度条单独提升到一个图层，即不影响其它元素**



实践：https://juejin.cn/post/6976810016930005029



如何开启 gpu：

- 使用 `transform` 和 `opacity` ，这两个属性能够触发浏览器的合成层（compositing layer），从而启用 GPU 加速
  - 比如使用 transform 实现动画，避免改变实际位置
- 配合 will-change 使用：允许开发者告诉浏览器哪些属性在未来会发生变化，从而为这些变化准备 GPU 加速。但需要注意的是，过度使用 `will-change` 会占用更多的资源，因此最好只在必要时使用。
- 使用 `filter`（小心使用）：`filter` 属性也可以触发 GPU 加速，尤其是当你使用滤镜效果（如模糊、亮度等）时。虽然 `filter` 能启用 GPU 加速，但不应滥用，因为某些复杂的滤镜可能导致性能下降。



filter：模糊或颜色偏移等图形效果应用于元素。滤镜通常用于调整图像，背景和边框的渲染。

```
filter: blur(5px);
filter: brightness(0.4);
filter: contrast(200%);
filter: grayscale(50%);
filter: opacity(25%);
```



