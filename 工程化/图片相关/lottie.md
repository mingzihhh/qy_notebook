### 什么是 Lottie？

是一个适用于Android，iOS，Web和Windows的库，它可以使用 Bodymovin 解析以 JSON 格式导出的Adobe After Effects动画，并在移动设备和Web 上原生渲染它们。

> Lottie is a library for Android, iOS, Web, and Windows that parses [Adobe After Effects](http://www.adobe.com/products/aftereffects.html) animations exported as json with [Bodymovin](https://github.com/airbnb/lottie-web) and renders them natively on mobile and on the web!

**优点**

1. 手工制作动画。手工制作动画对于设计和开发来说是一个巨大的时间投入。通常很难证明花这么多时间来制作动画是合理的。
2. Gif。Gif 的大小是 Bodymovin JSON 的两倍多，并且以固定大小呈现，无法放大以匹配大型和高密度屏幕。动画性能也不如 Lottie 好。
3. Png序列帧。Png 序列比 Gif 更糟糕，因为它们的文件大小通常是 Bodymovin JSON 大小的 30-50倍，并且无法放大。



### 使用  Lottie

1. 引入 lottie-web

```
npm install lottie-web
```

1. 加载动画

```JavaScript
lottie.loadAnimation({
  container: element, // the dom element that will contain the animation
  renderer: 'svg',
  loop: true,
  autoplay: true,
  path: 'data.json' // the path to the animation json
});
```

文档链接： https://airbnb.io/lottie/#/web

**Lottie** **组件的实现**

```HTML
<template>
  <div class="content" ref="containerRef" />
</template>

<script>
import lottie from 'lottie-web';
/* 实现的功能: 渲染动画
 * 未来可以实现的功能
 * 1. 动画的播放与暂停 anim.play() anim.stop() anim.pause()
 * 2. 控制动画播放速度 anim.setSpeed(speed)
 * 3. 动画是否自动播放，循环播放
 * 4. ...
 */
export default {
  name: 'Lottie',
  props: {
    animationData: {
      type: Object,
      required: true,
    },
  },
  mounted() {
    this.loadAnimation(this.$refs.containerRef, this.animationData);
  },
  unmounted() {
    this.anim.destroy();
  },
  methods: {
    loadAnimation(container, animationData) {
      this.anim = lottie.loadAnimation({
        container,
        animationData,
        renderer: 'svg',
        autoplay: true,
        loop: true,
      });
    },
  },
};
</script>
```

### 动态文案

#### 常规语言

1. 了解 Bodymovin JSON

```JSON
{
  "v": "5.7.7",
  "fr": 30,
  "ip": 0,
  "op": 91,
  "w": 660,
  "h": 280,
  "nm": "page1_1_lottie",
  "ddd": 0,
  "assets": [],
  "fonts": {},
  "layers": [],
  "markers": [],
  "chars": []
}
```

Bodymovin JSON 里面有很多属性，因为我们的主题是动态文案，所以我们只需要了解 JSON 中的哪个属性代表文案就可以了。

```JSON
{
    "layers": [
        {
            "ddd": 0,
            "ind": 1,
            "ty": 5,
            "nm": "New",
            "sr": 1,
            "ks": {...},
            "ao": 0,
            "t": {
                "d": {
                    "k": [
                        {
                            "s": {
                                "sz": [
                                    41.8199996948242,
                                    24
                                ],
                                "ps": [
                                    -20.9099998474121,
                                    -12
                                ],
                                "s": 20,
                                "f": "ProximaNova-Semibold",
                                "t": "tcm_sidenav_new",
                                "ca": 0,
                                "j": 2,
                                "tr": 0,
                                "lh": 24,
                                "ls": 0,
                                "fc": [
                                    1,
                                    1,
                                    1
                                ]
                            },
                            "t": 0
                        }
                    ]
                },
                "p": {},
                "m": {
                    "g": 1,
                    "a": {
                        "a": 0,
                        "k": [
                            0,
                            0
                        ],
                        "ix": 2
                    }
                },
                "a": []
            },
            "ip": 0,
            "op": 150,
            "st": 0,
            "bm": 0
        }
    ]
}
```

layers 中有个属性是 ty，该属性代表图层的类型， ty === 5 表示该图层是文本。 其中的 layers.t.d.k[].s 属性就是文本的具体内容与样式。

1. 与设计师沟通，检查 JSON 的文本
   1. JOSN 文本中不能包含 chars 属性，否则导致切换语言，文案渲染不出来。是否包含 chars 属性，与设计师的导出方式有关，保证设计师不要勾选 Glyphs 选项，详情看这篇文章。
   2. https://www.zcool.com.cn/article/ZMTE1OTk4MA==.html
   3. 将 JOSN 中的文案修改为我们的 Starling key。
2. 在 loadAnimation 前替换 JSON 的文本

```JavaScript
// 替换 layer 中的文本
function updateLayerText(layers) {
  layers.forEach((layerItem) => {
    if (layerItem.ty === 5) {
      layerItem.t.d.k.forEach((textItem) => {
        textItem.s.t = this.$t(textItem.s.t);
      });
    }
  });
}
// 除了 layers 中包含文本，assets 中可能也有 layers
function updateText(data) {
  data.assets.forEach((assetItem) => {
    this.updateLayerText(assetItem.layers);
  });
  this.updateLayerText(data.layers);
}
```

#### 阿拉伯语言

阿拉伯语言有两个不叫特殊的 feature：

1. 文字方向 rtl
2. 不能单个拆开 （指 str.split）



##### 文字方向的解决方案

1. 将整个动画翻转

```CSS
transform: scaleX(-1);
```

1. 将文字翻转回去

   1. 我们需要找到文字，所以我们需要在文字的图层新增一个 class。只需要在 layer 的属性中添加一个  "cl"， lottie 渲染的时候会在对应的元素上加上 class。

   2. ```JavaScript
      {
          "layers": [
              {
                  "ddd": 0,
                  "ind": 1,
                  "ty": 5,
                  "nm": "New",
                  "cl": "lottie-text",
                  "sr": 1,
                  "ks": {...},
                  "ao": 0,
                  "t": {...},
                  "ip": 0,
                  "op": 150,
                  "st": 0,
                  "bm": 0
              }
          ]
      }
      ```

   3. 翻转 text

   4. ```JavaScript
      this.anim.addEventListener('DOMLoaded', () => {
        const elements = this.$refs.containerRef.querySelectorAll('.lottie-text text');
        for (const el of elements) {
          el.setAttribute('transform', 'scale(-1,1)');
        }
      });
      ```

##### 文字不能拆分的解决方案

1. 需要与设计师功能，不能在文本框中写文字，否则 lottie 会自动拆分。
2. lottie-web 5.9.0 及以上的版本都会自动将文字拆开，所以我们目前暂时要保证 lottie-web 的版本低于 5.9.0。