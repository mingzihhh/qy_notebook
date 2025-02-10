| 场景               | 具体修改                                                     |
| ------------------ | ------------------------------------------------------------ |
| 重新 install       | 以`pnpm`为安装方法重新生成`node_modules`和`pnpm-lock.yaml`。为部分不需要的`peer dependencies` 设置 `ignoreMissing` ，避免报错（Miss peer dependencies）影响感官。对比`npm`安装速度，发现`pnpm` 卡壳在安装`ttf2woff2`，对`.npmrc`加上`side-effects-cache=true`规则，后续安装时将不会重复执行编译。 |
| 运行项目，解决报错 | 项目中不可避免的使用了一些`隐形依赖包` ,找到并修复。确保`scripts`中所有的命令都能正常执行，`gitHooks` 可以正常工作。 |
| GitLab CI 修改     | 基于现有GitLab CI的运行镜像，增加pnpm包修改CI文件            |
| SCM 修改镜像       | 基于现有的编译镜像，增加pnpm包                               |



### 检查项目的Phantom dependency

- 可以用`[rush scan](https://rushjs.io/pages/commands/rush_scan/)`处理
- 可以用[dependency-check](https://www.npmjs.com/package/dependency-check)这个工具去检查校验这种Phantom dependency的场景（家新同学用这个工具踩到了坑，建议用rush scan
- To avoid this problem, we can use the [eslint-plugin-import](https://github.com/benmosher/eslint-plugin-import) package, which has a rule `no-extraneous-dependencies` that can warn when an import is made from an unspecified package. It is enabled by default in the 'recommended' config. Otherwise, we should check by hand that all new imports come from packages specified in `package.json`.



### 项目改造流程

1. **全局安装** **pnpm**

```
npm install -g pnpm
```

2. **根据现有的package-lock.json生成pnpm-lock.yaml**

```
pnpm import
```

3. **如果项目使用了** **monorepo** **，需配置pnpm-workspace.yaml**

```go
// 本项目的自定义包模块位于packages目录下，故pnpm-workspace.yaml有如下配置

packages:

  # all packages in subdirs of packages/ and components/

  - 'packages/**'
```

4. **在根目录package.json创建需要的 npm script**

```javascript
{

  "name": "***",

  "version": "1.0.0",

  "private": true,

  "scripts": {

    "pnpm-init": "npm run pnpm-clean && pnpm install && npm run prepare",

    "pnpm-clean": "rm -rf node_modules && rm -rf output && pnpm -r exec -- rm -rf node_modules",

    "branch-check": "echo 'branch-check-begin' && pnpm install && echo 'branch-check-end'",

  },

}

// pnpm-init: 项目初始化

// pnpm-clean: 删除依赖等内容

// branch-check: 在分支切换时执行的script
```

5. **为项目安装husky**

```
npm install husky --save-dev
```

6. **husky添加分支切换，在post-checkout时要执行的命令**

```
npx husky add .husky/post-checkout "npm run branch-check"
```

#### 监控指标

**本地安装时间**

计算规则：10次中，去除最大值和最小值后取平均值。

- npm：48.409s
- pnpm：19.725s

**SCM 下载安装速度**

`pnpm`缩短了`install`的时间，但整体`build`并没有太大的提升。