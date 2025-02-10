### 概念

Git hooks 是 Git 提供的一种机制，允许你在 Git 的特定事件（如提交、推送、合并等）发生时自动执行指定的脚本。自定义 Git hooks 可以帮助开发者自动化某些开发流程，如代码格式化、代码质量检查、自动化测试等，从而提升代码质量和开发效率。

#### 客户端 hook

- `pre-commit` hook, 在运行 `git commit` 命令时且在 commit 完成前被触发
- `commit-msg` hook, 在编辑完 commit-msg 时被触发，并且接受一个参数，这个参数是存放当前 commit-msg 的临时文件的路径
- `pre-push` hook, 在运行 `git push` 命令时且在 push 命令完成前被触发

- ...

#### 服务端 hook

- `pre-receive` 在服务端接受到推送时且在推送过程完成前被触发
- `post-receive` 在服务端接收到推送且推送完成后被触发
- ...

#### Husky

https://www.cnblogs.com/dtux/p/16419271.html

`husky` 是一个用于在 Git 仓库中添加和管理 Git hooks 的工具。Husky 通过提供简单的 API 和命令行工具，使开发者可以轻松地在项目中添加和管理 Git hooks。

主要特性：

1. 简化 Git hooks 的配置和维护。
2. 可以与各种代码质量工具（如 ESLint、Prettier）和测试工具（如 Jest、Mocha）结合使用。
3. 能够在团队环境中统一 Git hooks 行为。

```
// package.json
{
  "scripts": {
    "prepare": "husky install",// 确保 Husky 可以正确安装
    "lint": "eslint .",
    "format": "prettier --write \"src/**/*.{js,jsx,ts,tsx}\""
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write",
      "git add"
    ]
  }
}

// pre-commit

#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# gen DISPATCH_APIS.md
npm run gen-dispatch-apis
git add --a

# lint
npx lint-staged --allow-empty
```

