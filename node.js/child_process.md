**子进程是 Nodejs 核心API，可以用来执行 shell 命令，编写前端工程化工具，处理 cpu 密集型应用** 

Nodejs 创建子进程共有7个API，Sync是同步API，不加Sync是异步API

- `exec`执行命令：异步方法，回调函数返回buffer，可以执行shell命令，或者跟软件交互
- `execSync`同步执行命令：只执行较小的shell命令，想要立即拿到结果的shell
- `spawn`执行命令
- `spawnSync`同步执行命令
- `execFile`执行可执行文件
- `execFileSync`同步执行可执行文件
- `fork`创建node子进程，只能接收js模块，适合大量的计算，或者容易阻塞主进程操作的一些代码



### 更高效的子进程管理：Execa 模块

Execa 模块用于**执行外部命令**，构建在 child_process 模块之上，提供了简洁的API和更好的错误处理机制。但它属于第三方模块，需要 npm 额外安装

使用 Promise 封装，默认支持 Promise API； 提供了更好的错误处理机制，能够捕获并处理命令执行过程中的错误；提供了直接访问 `stdout` 和 `stderr` 的简化选项，支持将子进程输出重定向到主进程的控制台

```
const execa = require('execa');

async function runCommand() {
    try {
    	 // 类似 spawn
        const { stdout } = await execa('ls', ['-l', '-a'], { cwd: '/path/to/directory' });
        console.log('命令执行结果：', stdout);
    } catch (error) {
        console.error('命令执行失败：', error);
    }
}

runCommand();
```

参数包括：

- command(String)：要执行的外部命令，例如‘ls’、‘echo’等
- args(Array)：传递给外部命令的参数，以数组形式传入
- option(Object 可选)：见上面



`execaCommand()` 方法执行命令时只接收一个字符串，将整个命令行指令当作字符串处理。