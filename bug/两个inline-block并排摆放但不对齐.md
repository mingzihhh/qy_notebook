## 两个inline-block并排摆放但不对齐

原因：

nline-block的默认对齐方式是vertical-block：baseline

如果一个inline-block元素里面是空的，或者它本身有overflow属性，这种情况下基线是它margin的底边缘。

如果一个inline-block元素里面不是空的（比如里面有文字或者图像），则它的基线由正常流中最后一个line box的基线决定。

=>

第一个元素里有一行文本，第二个元素有overflow属性，导致第一个元素与其底边对齐偏下移

解决方法：

改变元素display属性，或者改变vertical-align