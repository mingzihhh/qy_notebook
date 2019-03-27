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
```

