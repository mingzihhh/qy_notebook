## JS  获取页面元素高度

- offsetHeight 

  offsetHeight的值包括元素内容+内边距+边框

- clientHeight

  clientHeight的值等于元素内容+内边距。

- getComputedStyle

  获取的是最终应用在元素上的所有CSS属性对象

  ```
  console.log(window.getComputedStyle(div).getPropertyValue('height')); // 200px
  ```

