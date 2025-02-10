dragStart ->计算起使位置，执行animate()，在每次（requestAnimationFrame）重绘前计算transform，值由dragPoint决定

```
proto.animate = function() {

  // only render and animate if dragging

  if ( !this.isDragging ) {

​    return;

  }

  this.positionDrag();

  var _this = this;

  requestAnimationFrame( function animateFrame() {

​    _this.animate();

  });

};

proto.positionDrag = function() {
  this.element.style.transform = 'translate3d( ' + this.dragPoint.x +
    'px, ' + this.dragPoint.y + 'px, 0)';
};
```

dragMove->通过moveVector改变dragPoint值和position

- `moveVector`：类型：Object。鼠标指针移动到距离开始移动位置有多远：`{ x: 20, y: -30 }`。

-   this.position.x = this.startPosition.x + dragX;

    this.position.y = this.startPosition.y + dragY;

-   this.dragPoint.x = dragX;

    this.dragPoint.y = dragY;

dragEnd->清空transform，将position的值赋予left和top

