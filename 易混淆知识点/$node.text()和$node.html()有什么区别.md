### `$node.text()`和`$node.html()`有什么区别

$node.html()：

- 获取集合中第一个匹配元素的html内容，或设置每一个元素的html内容。
- .html() 方法对 XML 文档无效。
- 我们可以使用 `.html()` 来设置元素的内容，这些元素中的任何内容会完全被新的内容取代。此外，用新的内容替换这些元素前，jQuery从子元素删除其他结构，如数据和事件处理程序。（注：这样可以防止内存溢出。）
- 这种方法使用浏览器的`innerHTML` 属性。

$node.text()：

- 获取匹配元素集合中每个元素的合并文本，包括它们的后代；或设置匹配元素集合中每个元素的文本内容为指定的文本内容。
- .text() 在XML 和 HTML 文档中都能使用。.
- text()方法不能使用在 input 元素或scripts元素上。input或textarea需要使用 .val() 方法获取或设置文本值。scripts元素需要使用.html()方法
- 调用DOM 方法 `.createTextNode()`