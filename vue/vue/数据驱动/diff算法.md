## diff算法

<https://www.cnblogs.com/wind-lanyan/p/9061684.html>

<https://github.com/aooy/blog/issues/2>

![](https://images2018.cnblogs.com/blog/998023/201805/998023-20180519213134497-676744027.png)

### 1. 当数据发生变化时，vue是怎么更新节点的？

渲染真实DOM的开销是很大的，比如有时候我们修改了某个数据，如果直接渲染到真实dom上会引起整个dom树的重绘和重排，diff算法能够帮助我们只更新修改的那一小块dom而不要更新整个dom。

我们先根据真实DOM生成一棵`virtual DOM`，当`virtual DOM`某个节点的数据改变后会生成一个新的`Vnode`，然后`Vnode`和`oldVnode`作对比，发现有不一样的地方就直接修改在真实的DOM上，然后使`oldVnode`的值为`Vnode`。

diff的过程就是调用名为`patch`的函数，比较新旧节点，一边比较一边给**真实的DOM**打补丁。

在采取diff算法比较新旧节点的时候，比较只会在**同层级**进行, 不会跨层级比较。

>按层遍历指的是，先比较父节点，如果相同，再比较它们同一层的子节点

当数据发生改变时，set方法会让调用`Dep.notify`通知所有订阅者Watcher，订阅者就会调用`patch`给真实的DOM打补丁，更新相应的视图。

```
function patch (oldVnode, vnode) {
    // some code
    if (sameVnode(oldVnode, vnode)) {
        patchVnode(oldVnode, vnode)
    } else {
        const oEl = oldVnode.el // 当前oldVnode对应的真实元素节点
        let parentEle = api.parentNode(oEl)  // 父元素
        createEle(vnode)  // 根据Vnode生成新元素
        if (parentEle !== null) {
            api.insertBefore(parentEle, vnode.el, api.nextSibling(oEl)) // 将新元素添加进父元素
            api.removeChild(parentEle, oldVnode.el)  // 移除以前的旧元素节点
            oldVnode = null
        }
    }
    // some code 
    return vnode
}
```

patch最后会返回vnode，vnode和进入patch之前的不同在哪？
没错，就是vnode.el，**唯一的改变就是之前vnode.el = null, 而现在它引用的是对应的真实dom。**



### 2.判断两节点是否值得比较

```
//判断两节点是否值得比较，值得比较则执行patchVnode
//不值得比较则用Vnode替换oldVnode
function sameVnode (a, b) {
  return (
    a.key === b.key &&  // key值
    a.tag === b.tag &&  // 标签名
    a.isComment === b.isComment &&  // 是否为注释节点
    // 是否都定义了data，data包含一些具体信息，例如onclick , style
    isDef(a.data) === isDef(b.data) &&  
    sameInputType(a, b) // 当标签是<input>的时候，type必须相同
  )
}
```



### 3.进一步比较结点

- 找到对应的真实dom，称为`el`
- 判断`Vnode`和`oldVnode`是否指向同一个对象，如果是，那么直接`return`
- 如果他们都有文本节点并且不相等，那么将`el`的文本节点设置为`Vnode`的文本节点。
- 如果`oldVnode`有子节点而`Vnode`没有，则删除`el`的子节点
- 如果`oldVnode`没有子节点而`Vnode`有，则将`Vnode`的子节点真实化之后添加到`el`
- 如果两者都有子节点，则执行`updateChildren`函数比较子节点，这一步很重要

```

patchVnode (oldVnode, vnode) {
    const el = vnode.el = oldVnode.el
    let i, oldCh = oldVnode.children, ch = vnode.children
    if (oldVnode === vnode) return
    if (oldVnode.text !== null && vnode.text !== null && oldVnode.text !== vnode.text) {
        api.setTextContent(el, vnode.text)
    }else {
        updateEle(el, vnode, oldVnode)
        //比较子节点
       	if (oldCh && ch && oldCh !== ch) {
            updateChildren(el, oldCh, ch)
        }else if (ch){
            createEle(vnode) //create el's children dom
        }else if (oldCh){
            api.removeChildren(el)
        }
    }
}
```

### 4.比较子节点

- 将`Vnode`的子节点`Vch`和`oldVnode`的子节点`oldCh`提取出来

- `oldCh`和`vCh`各有两个头尾的变量`StartIdx`和`EndIdx`，它们的2个变量相互比较，一共有**4种比较方式**。

  - 如果是oldS和S匹配上了，不需要对dom进行移动，指针移动。
  - 如果是oldE和E匹配上了，不需要对dom进行移动，指针移动。

  - 如果是oldS和E匹配上了，那么真实dom中的第一个节点会**移到最后**
  - 如果是oldE和S匹配上了，那么真实dom中的最后一个节点会**移到最前**，
  - 如果四种匹配没有一对是成功的，那么遍历`oldChild`，`S`挨个和他们匹配，匹配成功就在真实dom中将成功的节点移到最前面，如果依旧没有成功的，那么将`S对应的节点`插入到dom中对应的`oldS`位置，`oldS`和`S`指针向中间移动。

- 匹配过程的结束有**两个条件**：

  - `oldS > oldE`表示`oldCh`先遍历完，那么就将多余的`vCh`根据index添加到dom中去

  - `S > E`表示vCh先遍历完，那么就在真实dom中将区间为`[oldS, oldE]`的多余节点删掉

- 如果4种比较都没匹配，如果设置了`key`，就会用`key`进行比较，在比较的过程中，变量会往中间靠，一旦`StartIdx>EndIdx`表明`oldCh`和`vCh`至少有一个已经遍历完了，就会结束比较。



设置key和不设置key的区别：
**不设key，newCh和oldCh只会进行头尾两端的相互比较，设key后，除了头尾两端的比较外，还会从用key生成的对象oldKeyToIdx中查找匹配的节点，所以为节点设置key可以更高效的利用dom。**



### 总结

- 尽量不要跨层级的修改dom
- 设置key可以最大化的利用节点
- diff的效率并不是每种情况下都是最优的



