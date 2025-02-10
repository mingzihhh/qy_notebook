## em和rem

#### em

通俗的讲就是： **1em**，就是这个元素上的 **font-size** 的值，如果元素本身没有设置 **font-size** ，就按照他的父级的 **font-size** 计算（其实本身的font-size继承了父级的font-size），如果有就按照自己的font-size计算

如果用em布局会存在一个问题，牵一发而动全身，一旦某个节点的字体大小发生变化，那么其后代元素都得重新计算

#### rem

rem作用于非根元素时，相对于根元素字体大小；rem作用于根元素字体大小时，相对于其出初始字体大小

字体大小font-size继承父级或者自己设置

#### rem布局

rem布局的本质是等比缩放，一般是基于宽度

假设我们将屏幕宽度平均分成100份，每一份的宽度用x表示，`x = 屏幕宽度 / 100`，如果将x作为单位，x前面的数值就代表屏幕宽度的百分比

```
document.documentElement.style.fontSize = document.documentElement.clientWidth / 100 + 'px';
```

#### 比rem更好的方案

vw —— 视口宽度的 1/100；vh —— 视口高度的 1/100 

vw还可以和rem方案结合，这样计算html字体大小就不需要用js了

```
html {fons-size: 1vw} /* 1vw = width / 100 */
p {width: 15.625rem}
```

