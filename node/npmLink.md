### npm link命令解析

假设我们开发了一个模块叫 `test` ，然后我们在 `test-example` 这个项目里引用这个模块 ，每次 `test` 模块的变动我们都需要反映到 `test-example` 项目里。不要担心，有了 `npm link` 命令一切变的非常容易。

首先我们需要把 `test` 链接到全局模式下：

```bash
cd ~/work/node/test # 进入test模块目录

npm link # 创建链接到$PREFIX/lib/node_modules
```
那么 `test` 的模块将被链接到 `$PREFIX/lib/node_modules` 下，就像我的机器上 `$PREFIX` 指到 `/usr/local` ，那么 `/usr/local/lib/node_modules/test` 将会链接到 `~/work/node/test` 下。执行脚本 `bin/test.js` 被链接到 `/usr/local/bin/test` 上。

接下来我们需要把 `test` 引用到 `test-example` 项目中来：

```bash
cd ~/work/node/test-example # 进入test-example模块目录
npm link test # 把全局模式的模块链接到本地
```
`npm link test` 命令会去 `$PREFIX/lib/node_modules` 目录下查找名叫 `test` 的模块，找到这个模块后把 `$PREFIX/lib/node_modules/test` 的目录链接到 `~/work/node/test-example/node_modules/test` 这个目录上来。

现在任何 `test` 模块上的改动都会直接映射到 `test-example` 上来。