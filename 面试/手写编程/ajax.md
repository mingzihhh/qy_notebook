## ajax

使用XMLHttpRequest对象与服务器通信，它可以使用JSON，XML，HTML和text文本等格式发送和接收数据。AJAX最吸引人的就是它的“异步”特性，也就是说他可以在不重新刷新页面的情况下与服务器通信，交换数据，或更新页面。

```
function ajax(url,fn){
    let request = new XMLHttpRequest()
    request.open('GET',url,true)
    request.onreadystatechange = function(){
        if(rrequest.readyState === 4 && request.status === 200){
            fn(request.responseText)
        }
    }
    request.send()
}

//post
xhr.open("POST", url, true);
xhr.setRequestHeader("Content-Type": "application/x-www-form-urlencoded");
xhr.send("key1=value1&key2=value2");
```

### Ajax与cookie

- ajax会自动带上同源的cookie，不会带上不同源的cookie

- 可以通过前端设置withCredentials为true， 后端设置Header的方式让ajax自动带上不同源的cookie，但是这个属性对同源请求没有任何影响。会被自动忽略。

  前端代码：

  ```
  var xhr = new XMLHttpRequest();
  xhr.open('GET', 'http://example.com/', true);
  xhr.withCredentials = true;
  xhr.send(null);
  ```

  后端：

  增加 response 头信息Access-Control-Allow-Origin，且必须指定域名，而不能指定为*。

  ```
  res.setHeader('Access-Control-Allow-Origin','http://172.19.0.215:3333');
  ```

  

#### promise封装ajax

```
function pajax(url,fn){
	return new promise((resolve,reject) => {
        let request = new XMLHttpRequest()
        request.open('GET',url,true)
        request.onreadystatechange = function(){
            if(request.readyState === 4 && request.status === 200){
                let result = fn(request.responseText)
                resolve(result)
            }
        }
        request.onerror = function(err){
            reject(err)
        }
        request.send()
	})
    
}
```

