## 手写promise

```
function Promise(executor){
   let self = this
   self.status = 'pending' //等待态
   self.value = undefined //成功的返回值
   self.reason = undefined //出错原因
   
   function resolve(value){
   	    if(self.status === 'pending'){
            self.status = 'resolved'
            self.value = value
   	    }  
   }
   
   function reject(value){
       if(self.status === 'pending'){
           self.status = 'rejected'
           self.reason = value
       }
   }
   
   try{
       executor(resolve,reject)
   }catch(e){
       reject(e)//捕获失败返回错误
   }  
}
//onFufiled 成功的回调
//onRejected 失败的回调
Promise.prototype.then = function(onFufilled,onRejected){
    let self = this
    if(self.status === 'resolved'){
        onFufilled(self.value)
    }
    if(self.status === 'rejected'){
        onRejected(self.reason)
    }
}

class Promise() {
	constuctor(fn) {
		this.status = 'pending';
		this.value = undefined;
		this.reason = undefined;
		
		const resolve = (val) => {
			if (this.status === 'pending') {
				this.status = 'fufilled';
				this.value = val; 
			}
		}
		
		const reject = (err) {
			if (this.status === 'pending') {
				this.status = 'rejected';
				this.reason = err;
			}
		}
		
		try {
			fn(resolve, reject);
		} catch (err) {
			reject(err);
		}
	}
	
	 then(onFufilled, onRejected) {
	 		return new Promise((resolve, reject)) {
	 			if (this.sta)
	 		}
	 }
	
}
```



### Promise.all

```
function promiseAll(promises) {
	return new Promise((resolve, reject) => {
			// 特殊情况：如果传入的是空数组，直接 resolve 空数组
			if (promises.length === 0) {
				resolve([]);
			}
			
			const res = [];
      let completedPromises = 0;

      promises.forEach((promise, index) => {
        Promise.resolve(promise) // 确保传入值是一个 Promise
        .then(value => {
            completedPromises++; 
            res[index] = value; // 保存结果，按顺序
            if (completedPromises === promises.length) { // 检查是否所有 Promise 都完成
              resolve(res);
            }
        })
        .catch(err => {
          reject(err); // 只要有一个 Promise 被拒绝，立即 reject
        }) 
      }
	})
}
```



#### Promisify

`promisify` 是一个工具函数，用于将传统的回调风格函数（如 Node.js 中的异步函数）转换为返回 Promise 的函数。其基本原理是将函数的回调部分封装进 Promise，然后通过 `resolve` 和 `reject` 实现成功或失败的处理。

```
function promisify(fn) {
	return function(...args) {
		return new Promise((resolve, reject)) {
			try {
				fn(...args, (error, ...res)) => {
					if (!error) {
						resolve(res);
					} else {
						reject(error);
					}
				})
			}
		}
	}
}
```



**使用 promise 实现每隔一秒输出 1，2，3**

```
async function printNumber() {
	for(let i = 1; i <= 3; i++) {
		await new Promise(resolve => setTimeout(resolve, 1000));
		console.log(i);
	}
}
```

