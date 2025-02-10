## vue-router query和params, $router和$route的区别

- $router为VueRouter实例，想要导航到不同URL，则使用$router.push方法
- $route为当前的路由对象，表示当前激活的路由的状态信息，里面可以获取name、path、query、params等



query：

```
传参: 
this.$router.push({
        path:'/xxx',
        query:{
          id:id
        }
      })
  
接收参数:
this.$route.query.id
```

- 一个 key/value 对象，表示 URL 查询参数。例如，对于路径 `/foo?user=1`，则有 `$route.query.user == 1`，如果没有查询参数，则是个空对象。
- 配合path使用
- 类似于ajax中get传参，在浏览器地址栏中显示参数
- 刷新不会丢失query里面的数据



params：

```
传参: 
this.$router.push({
        name:'xxx',
        params:{
          id:id
        }
      })
  
接收参数:
this.$route.params.id
```

- 一个 key/value 对象，包含了动态片段和全匹配片段，如果没有路由参数，就是一个空对象。
- 配合name使用
- 相当于post请求，参数不会在地址栏中显示
- 刷新会丢失 params里面的数据