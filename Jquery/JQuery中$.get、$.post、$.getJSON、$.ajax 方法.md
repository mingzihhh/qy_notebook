### JQuery中$.get、$.post、$.getJSON、$.ajax 方法

- GET - 从指定的资源请求数据，发送至服务器的key/value数据会作为QueryString附加到请求URL中,服务器返回数据的格式其实是字符串形式，并不是我们想要的json数据格式
- POST - 向指定的资源提交要处理的数据,这个函数跟$.get()参数差不多，多了一个type参数，type为返回的数据类型，可以是html,xml,json等类型，如果我们设置这个参数为：json，那么返回的格式则是json格式的，如果没有设置，就 和$.get()返回的格式一样，都是字符串的
- getJSON-使用 AJAX 请求来获得 JSON 数据,使用JSON相比传统的通过 GET、POST直接发送“裸体”数据，在结构上更为合理，也更为安全。至于jQuery的getJSON()函数，只是设置了JSON参数的 ajax()函数的一个简化版本。这个函数也是可以跨域使用的，相比get()、post()有一定优势。另外这个函数可以通过把请求url写 成"myurl?callback=X"这种格式，让程序执行回调函数X。
- ajax()-$.ajax() 返回其创建的 XMLHttpRequest 对象

