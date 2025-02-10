## JSONP

```
function jsonp(url,jsonpCallback,success){
    let el = documenyt.createElement('script')
    el.src = url
    el.async = true
    el.type = 'text/javascript'
    window[jsonpCallback] = function(data){
        success && success(data)
    }
    document.body.appendChild(el)
}
jsonp('http://xxx', 'callback', function(value) {
  console.log(value)
})

//后端，node.js
const http = require('http')
const fs = require('fs')
const url = require('url')

http.createServer((req, res) => {
    const data = {name: 'uthus'}
    // 获取查询参数callback，就是我们在客户端定义的jsonpCb
    const callback = url.parse(req.url, true).query.callback
    if (callback) {
        const str = callback + '(' + JSON.stringify(data) + ')'
        // 以字符串的形式返回
        res.end(str)
    } else {
        res.end('hello world')
    }  
}).listen(3000)
```

