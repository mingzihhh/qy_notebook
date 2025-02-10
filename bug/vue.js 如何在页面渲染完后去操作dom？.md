vue.js 如何在页面渲染完后去操作dom？

1. 在mounted生命周期，组件挂载成功，但还未渲染，自然获取不到相关的DOM节点。
2. 

```
mounted: function () {
  this.$nextTick(function () {

  })
}
如果是静态数据上面的方法可行，如果是动态的数据 那就放到数据请求成功的回调里面去
```

3.数据加载完成后，使用this.$nextTick