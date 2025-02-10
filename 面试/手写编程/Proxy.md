### proxy 加装饰器实现 API 缓存

```
function cacheDecorator(fn) {
	const cache = new Map();
	
	return async function(...args) {
		const key = JSON.stringify(args);
		if (cache.has(key)) {
			return cache.get(key);
		} 
		const res = await fn(...args);
		cache.set(key, res);
		return res;
	}
}

async function fetchData(endpoint) {
    console.log(`Fetching data from ${endpoint}`);
    // 模拟 API 调用
    return new Promise((resolve) =>
        setTimeout(() => resolve({ data: `Data from ${endpoint}` }), 1000)
    );
}


const cachedApi = new Proxy(cacheDecorator(fetchData), {
	apply(target, thisArg, args) {
		return target(...args);
	}
})

// 使用
(async () => {
    console.log(await cachedApi("/api/data1")); // 调用 fetchData
    console.log(await cachedApi("/api/data1")); // 从缓存返回
    console.log(await cachedApi("/api/data2")); // 调用 fetchData
    console.log(await cachedApi("/api/data1")); // 从缓存返回
})();
```

