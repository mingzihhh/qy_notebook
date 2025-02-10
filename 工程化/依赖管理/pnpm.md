要解决的问题：

1. 减少同样的依赖被复制多次，从而减少磁盘空间的占用

2. 避免幽灵依赖。

   

**pnpm实现思想**：

在全局仓库 store（通常位于 `~/.pnpm-store` ）通过这种方式，即使在项目中使用了多个版本的同一依赖项，也只会在存储区中保留一 保存 npm 包的内容，其余的地方都 link 过去

**硬链接**

- 硬链接就是另一个文件名，但是**指向了相同的磁盘地址**。
- 当移动或删除原始文件时，硬链接不会被破坏，因为它所引用的是文件的物理数据而不是文件在文件结构中的位置。
- 当所有的硬链接都被删除，则文件本身也被删除了。

**软链接**

- 包含一条以绝对路径或相对路径的形式指向其他文件或者目录的引用，只是一个快捷方式，删除所有的软链接也不会对文件有任何影响
- 对软链接的所有操作会被文件系统替换为对软链接指向的文件的操作，如果原始文件的位置被移动了，那么软链接就找不到对应的文件了，改动自然也无法同步到文件上
- 因为软链接只是个快捷方式，所以软链接甚至可以指向不存在的文件

```Kotlin
-> - a symlink (or junction on Windows)

node_modules
├─ foo -> .pnpm/foo/1.0.0/node_modules/foo
└─ .pnpm
   ├─ foo/1.0.0/node_modules
   |  ├─ bar -> ../../bar/2.0.0/node_modules/bar
   |  └─ foo -> <store>/foo
   |     ├─ index.js
   |     └─ package.json
   └─ bar/2.0.0/node_modules
      └─ bar -> <store>/bar
         ├─ index.js
         └─ package.json
        
```

.pnpm称**虚拟存储目录**，以平铺的形式储存着所有的项目依赖包，每个依赖包都可以通过`.pnpm/<name>@<version>/node_modules/<name>`路径找到实际位置。

上面的结构中，`foo -> <store>/foo`和`bar -> <store>/bar`是真实存在node_modules下面的文件，硬链到全局的store中；其余的都是软链，用来构建嵌套依赖的图结构；https://github.com/pnpm/sample-project 这里有一个官方的demo可以参考

在node_modules里文件之间通过**软链接**的方式进行链接，包内的文件再**硬链接**到全局的`pnpm store`中的对应文件。

- **使用硬连接的方式节约磁盘空间利用率**：基于内容寻址，不会重复安装同一个包，对于同一个包的不同版本采取增量写入新文件的策略。
- **采用虚拟存储目录+软连接解决幽灵依赖**



#### 四种模式

事实上pnpm支持四种级别的node_modules结构，从松到严依次为

##### **hoisted模式**

所有的三方库都平铺在根目录的node_modules，这意味着application code能访问所有的依赖代码（无论是否在dependency里），所有的依赖也能互相访问其他依赖的代码（无论是否在dependency)，这也是**npm的默认模式**。

##### **semi strict 模式**

这也是**pnpm的默认模式**，这意味着application code仅能够访问其依赖里的库（types和eslint相关库除外),但是所有的依赖仍然能够互相访问其他依赖的代码。

##### **strict模式**

这种情况下，我们既禁止application code访问依赖外的代码，也禁止三方依赖访问其他非依赖里的三方依赖代码。这个模式也是最推荐业务使用的模式，但是不幸的是，pnpm出于对生态的兼容性，做了妥协，默认并没有设置为该模式，但是作为有追求的业务方的你，应该使用这个模式。这可以保证你的业务不会突然有一天因为依赖问题突然挂掉。

**生态的兼容性 =>允许 vendor与vendor的hoist行为**

vendor和vendor的hoist是指，一个三方库可以访问不在其依赖里的其他三方库代码，这听起来有点不可思议，既然一个库用到了某个依赖，那理所当然应当将其列入其依赖，否则这个库肯定跑不起来啊，然而不幸的是，仍然有大量的三方库，没遵守这个约定。

以webpack-cli为例，其虽然依赖了ts-node等来将ts配置文件翻译为js，但是其并没有将ts-node列入到其dependency和peer-dependency里，这里存在的一个风险就是，如果你之前恰好安装了webpack-cli和另一个库A且另一个库A正好又依赖了ts-node,并且你的webpack配置文件使用了ts文件，那么你很幸运的能够将webpack跑起来，突然有一天库A决定不使用ts-node作为依赖，那么不幸的是你的webpack将无法正常编译。

虽然有这种潜在的风险，奈何整个js的生态库都良莠不齐，导致pnpm也只能默认开启hoist模式，默认所有的vendor都是可以互相蹭的。你如果比较有追求，可以通过设置https://pnpm.io/npmrc#hoist 为false,关闭三方库的vendor的hoist行为。

##### pnp模式

即使pnpm开了最严格的strict模式，但是其只能控制本项目内的node_modules的拓扑结构，项目父目录的node_modules并不受到影响，所以仍然存在幻影依赖的风险，这个根因在于node 的resolve算法是递归向上查找的，因此在不hack node resolve算法的基础上，是无法根除幻影依赖的，所以更激进的方式，就是修改node的resolve的默认行为，这样就**保证了其不会递归向上查找**，pnp即采取了此种方式来根除幻影依赖问题，但是其也会带来新的问题，此处就不再多赘述。



#### 优点

1. **多项目间的复用**

由于绝大部分的依赖包都是通过硬链接串联到 store 里，每次安装依赖的时候，如果是相同的依赖，有好多项目都用到这个依赖，那么这个依赖实际上最优情况(即版本相同)只用安装一次，避免了二次安装带来的**时间消耗**，有效节省了**磁盘空间**



2. **创建非扁平的node_modules目录**

既然打平会让 require 能正确访问哪些它本不该访问的依赖，继续使用嵌套式就可以避免这种情况。

在默认情况下，pnpm 的 node_modules 一级目录只存在 package.json 里显式声明的依赖，其他依赖的依赖都放在.pnpm下，项目无法引用子依赖，**杜绝了隐式依赖的问题**。

乍一眼看，.pnpm下的目录结构和 npmv3 下的 node_modules 很像，但他每个package下有一个node_modules，打开看里面的结构和 npmv1&v2的目录又有点像，有当前package下所有的依赖，实际上这些都是通过 [软连接（符号链接）](https://en.wikipedia.org/wiki/Symbolic_link)串联起来的，保证了依赖的确定性，又不会增加存储空间。



其他：

https://pnpm.io/npmrc#public-hoist-pattern

pnpm默认会提升一些包到`root modules directory`，默认值为

```JavaScript
Default: ['*types*', '*eslint*', '@prettier/plugin-*', '*prettier-plugin-*']
```

因为prettier和eslint的相关设计缺陷，导致其经常强依赖其相关的plugin存放在项目根目录的node_modules里，因此pnpm默认并没有禁止所有的库的hoist行为，而是**给eslint和prettier开了后门**。



3. **monorepo 支持**

https://juejin.cn/post/7316409548994625574#heading-16

- 天然支持 monorepo 项目，`pnpm` 使用 `pnpm-workspace.yaml` 文件来配置和管理工作空间。通过此文件，你可以指定哪些包属于工作空间，并让 `pnpm` 自动处理依赖解析、安装和符号链接等操作。
- `pnpm` 并**不是通过目录名称，而是通过目录下** **`package.json`** **文件的** **`name`** **字段来识别仓库内的包与模块的。**

```
// pnpm-worksapce.yaml 指定工作空间的包位置
packages:
# 所有在 packages/  子目录下的 package
  - ‘packages/*
    # 不包括在 test 文件夹下的 package
  - '!**/test/**'
  
// packages/package-a/package.json
{
  "name": "package-a",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "lodash": "^4.17.21"
  }
}

// packages/package-b/package.json
{
  "name": "package-b",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "package-a": "workspace:*" // pnpm就已经帮我们把包链接起来了
  }
}

// 实际发布时，workspace:* 会被替换成内部模块 b 的对应版本号(对应 package.json 中的 version 字段)。替换规律如下所示：
{
  "dependencies": {
    "a": "workspace:*", // 固定版本依赖，被转换成 x.x.x
    "b": "workspace:~", // minor 版本依赖，将被转换成 ~x.x.x
    "c": "workspace:^"  // major 版本依赖，将被转换成 ^x.x.x
  }
}


```

- 自带支持 monorepo 的一些指令
  - 为每一个 package 都装上 react：`pnpm install react react-dom -w`
  - 只在 web 这个 package 下安装 react：`pnpm i react -r --filter @test/web`
  - 安装本地包之间的依赖：`pnpm intsall @test/http -r --filter @test/web` ( @test/http 是本地包)

- **解决 monorepo 中 yarn 无解的问题**：

 ```
 项目依赖结构：
 | pages
 |   ------home
 |   --------webpack5
 |   --------mini-css-extract-plugin  
 |   ------list
 |   --------webpack5
 |   --------mini-css-extract-plugin  
 |   ------qiankunwebpack4
 |   --------webpack4
 |   --------mini-css-extract-plugin  
 
 依赖包结构：
 | pages
 |   ------qiankunwebpack4
 |   --------|node_modules
 |   -----------|webpack4
 | node_modules
 |   --------|webpack5
 |   --------|mini-css-extract-plugin  
 ```

关键是 mini-css-extract-plugin 是依赖 webpack 的, 那么执行 qiankunwebpack4 里面的build 时，首先会用自己的node_modules下面的webpack4, 然后因为构建过程会用到mini-css-extract-plugin，所以会找到root下面的node_modules的mini-css-extract-plugin，这个时候mini-css-extract-plugin require了webpack，那么安装包查找，会找到同级目录下的webpack5

- ✔️ 用pnpm包管理，依赖的依赖都是列在一起, 不存在奇怪hoist导致的烦恼

  

#### 缺点

少数场景（比如 electron）存在兼容问题。原因：

1. 软链接本身在不同环境存在兼容问题
2. 部分 npm 包在进行软链接之后会产生意料之外的 bug

#### Q&A

1. 为什么 pnpm 对 全局 store 用硬链接，其他地方用软链接

硬链接有个致命问题：不能直接链接文件夹，如果想要硬链接文件夹必须对文件夹里的每个文件单独进行硬链接，会造成很大的性能问题，但是如果要使用软链接链接全局 store，会存在路径解析的错误（https://github.com/nodejs/node-eps/issues/46），所以 pnpm 使用软链接 + 硬链接结合的方式，项目内部**使用软链接维护依赖的嵌套关系，硬链接链接到全局 store**



2. 如何解决 store 越来越大的问题？

官方提供了一个清理的命令`pnpm store prune`，可以帮助我们清理未引用的包。
