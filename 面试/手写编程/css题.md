屏幕正中间有个元素A，随着屏幕宽度的增加，始终需要满足以下条件：

1. A元素垂直居中于屏幕中央；
2. A元素距离屏幕左右边距各20px;
3. A元素里面的文字“A”水平垂直居中；
4. A元素的高度始终是A元素宽度的50%。

```
  <div class='container'>
    <span>123<span>
  </div>
  
  .container{
  margin-left: 20px;
  margin-right: 20px;
  background: pink;
  width: 100%;
  padding-bottom: 50%;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  
}
span{
  position:absolute;
  top:50%;
  left:50%; 
  transform: translate(-50%,-50%);
  font-size:20px;
}

```

### 三角形

### 梯形

### 五角星
