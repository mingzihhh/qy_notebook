## 简单的co模块

run 函数接受一个生成器函数，每当 run 函数包裹的生成器函数遇到 yield 关键字就会停止，当 yield 后面的 promise 被解析成功后会自动调用 next 方法执行到下个 yield 关键字处，最终就会形成每当一个 promise 被解析成功就会解析下个 promise，当全部解析成功后打印所有解析的结果，衍变为现在用的最多的 async/await 语法



```
function run(generatorFunc){
    var it = generatorFunc()
    var result = it.next()
    
    return new Promise((resolve, reject) => {
        var next = function(result){
        	if(result.done){
                reslove(result.value)
        	}
        	result.value = Promise.reslove(result.value)
        	result.value.then(
        		res => {
                    var result = it.next(res)
                    next(result)
        		},
        		err => {
                    reject(err)
        		}
        	)
        }
        next(result)
    }
}
```

