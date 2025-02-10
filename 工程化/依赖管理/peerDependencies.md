https://juejin.cn/post/7170716245762048036#heading-5

`peerDependencies` 是 `package.json` 中的一种依赖关系，用于指定一个包所依赖的其他包版本。这种依赖关系通常用于插件、工具库或组件库，它们依赖于主应用的某些库或框架，并且需要确保这些库或框架的版本与其兼容。

换句话说，`peerDependencies` 通常用于声明那些你的包**在使用时期望主应用或其他库已经安装的依赖，并且需要特定的版本范围**。这样可以确保插件和其对应的主框架版本匹配。

依赖版本号的前缀代表的意思：

- `~` 波浪号，匹配最新补丁版本号，即版本号的第三个数字，例如 `~5.0.0` 就会匹配 5.0.x 版本，将在 5.1.0 停止
- `^` 插入符号，匹配次要的版本号，即版本号的第二个数字，例如 `^5.2.0` 就会匹配等于或大于设置的版本(这里就是 >= 5.2.0 < 6.0.0 )，将在 6.0.0 停止
- `>、<、>=、<=` 比较运算符，匹配的就是这个区间的版本，例如 `>2.0.0 <= 2.1.4 `，就会匹配这个区间的版本号



### 为什么使用 `peerDependencies`？

1. **保持兼容性**：一些库需要与特定版本的框架或其他库一起使用，`peerDependencies` 可以确保版本一致性，避免潜在的兼容性问题。

2. **插件和框架的依赖关系**：对于那些作为插件或扩展要与特定框架或库一起使用的包，`peerDependencies` 是展示这种依赖关系的一个好工具。

   

### `peerDependencies` 用法

#### 在 `package.json` 中定义 `peerDependencies`

**示例 1**：假设我们有一个 React 组件库需要依赖于特定版本的 React：

```json
{
  "name": "my-component-library",
  "version": "1.0.0",
  "main": "index.js",
  "peerDependencies": {
    "react": "^16.0.0 || ^17.0.0",
    "react-dom": "^16.0.0 || ^17.0.0"
  }
}
```

在这个示例中，`my-component-library` 期望主应用已经安装了 `react` 和 `react-dom`，而且版本必须是 `16.x` 或 `17.x`。

#### 安装时的行为

当你安装一个含有 `peerDependencies` 的包时，npm 将不会自动安装 `peerDependencies` 中的依赖，而是会发出警告提示，告诉你需要手动安装这些依赖。

```bash
npm install my-component-library
```

输出警告提示：

```plaintext
npm WARN my-component-library@1.0.0 requires a peer of react@^16.0.0 || ^17.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN my-component-library@1.0.0 requires a peer of react-dom@^16.0.0 || ^17.0.0 but none is installed. You must install peer dependencies yourself.
```

因此，开发者需要手动安装 `react` 和 `react-dom`：

```bash
npm install react@^17.0.0 react-dom@^17.0.0
```

或者在 `package.json` 中事先定义好 `react` 和 `react-dom` 的版本。



### 解决常见问题

#### 1. 一起安装 `peerDependencies`

为了便捷，可以使用辅助工具自动安装 `peerDependencies`，例如 `npm` 有内置方法：

```bash
npm install --legacy-peer-deps
```

或者使用第三方工具如 `install-peerdeps`：

```bash
npx install-peerdeps my-component-library
```

#### 2. 处理 `peerDependencies` 冲突

在某些情况下，可能会遇到 `peerDependencies` 冲突问题，比如两个包依赖的库版本不兼容。此时，需要手动调整项目的版本依赖，或者寻找兼容的版本，甚至有时需要重构代码以适配不同版本。

#### 3. `peerDependencies` 与 `devDependencies` 的关系

在开发插件或库时，`peerDependencies` 和 `devDependencies` 通常都会包含特定库。例如，一个 `React` 组件库的开发者可能会将 `react` 和 `react-dom` 同时列在 `peerDependencies` 和 `devDependencies` 中：

```json
{
  "name": "my-component-library",
  "version": "1.0.0",
  "main": "index.js",
  "peerDependencies": {
    "react": "^17.0.0",
    "react-dom": "^17.0.0"
  },
  "devDependencies": {
    "react": "^17.0.0",
    "react-dom": "^17.0.0",
    "jest": "^26.6.0"
  }
}
```

在 `peerDependencies` 中指定 `react` 和 `react-dom` 是为了表明**此组件库与特定版本的 React 配套使用**。而在 `devDependencies` 中列出相同的包则是为了在开发和测试时确保环境一致。

如果在一个 npm 包的 dependencies 中定义依赖，执行 npm install 时当子依赖的依赖版本在已经构建的依赖树中匹配不到时，就会重复下载。而 peerDependencies 则可以解决这个问题，**在 peerDependencies 中定义的依赖，不会重复下载，但会要求宿主环境提供 peer 依赖一致的版本，否则在安装的时候提示警报亦或报错中断命令的执行。**



### `npm` 对 `peerDependencies` 的支持

- npm4～6：安装插件时，当宿主环境中没有子依赖 peerDependencies 中声明的依赖时，会按照 peerDependencies 的声明将相关依赖安装到宿主项目的根目录的 `node_modules` 中；同时如果发生了宿主环境依赖和 `peerDependencies` 的声明**依赖发生冲突会有版本不兼容的警告，以宿主环境的依赖为准继续安装插件，但是不一定能运行**

- npm7：在宿主环境没有 `peerDependencies` 没有冲突的情况下会正常安装，并且把 `peerDependencies` 中的相关依赖都安装到根目录的 `node_modules` 下，**如果存在无法自动解决的依赖冲突，将会阻止插件安装并抛出错误** => 为了确保一个项目中的依赖版本是唯一。。官方建议我们使用 --force 或 --legacy-peer-deps 命令进行重新安装。
  - --legacy-peer-deps：安装的时候忽略所有对等依赖（peerDependencies），以 npm v4 ~ v6 的方式安装
  - --force 或 -f： 强制安装，会有警告



### `pnpm` 对 `peerDependencies` 的支持

使用 pnpm 方式安装插件时，是不会主动安装 `peerDependencies` 中声明的依赖的：在依赖没有冲突时，会报 `peerDependencies` 中声明的依赖缺失；在出现冲突时仍然可以安装，但是会报错

如果要自动安装 `peerDependencies` 中的依赖，还需要在 `.npmrc` 文件中进行以下配置：

```ini
auto-install-peers=true // 将自动安装任何缺少的非可选同级依赖关系。
strict-peer-dependencies=false // 如果启用了此选项，那么在依赖树中存在缺失或无效的 peer 依赖关系时，命令将执行失败。
```

使用 `install-peerdeps` 工具：

```bash
pnpm install-peerdeps my-component-library
```



### 结论

`peerDependencies` 是在构建 JavaScript 库、插件和框架时用于**确保依赖版本兼容性**的一个重要机制。通过在 `package.json` 中正确配置 `peerDependencies`，开发者可以声明其代码库对外部依赖的接口和版本要求，确保项目在不同环境下的稳定性和兼容性。

在实际开发中，合理使用 `peerDependencies` 能够帮助开发者更好地管理依赖关系，避免潜在的版本冲突和兼容性问题。同时，结合 `npm`、`Yarn` 等工具提供的选项和方法，可以更方便地处理依赖安装和管理问题。