# lerna-project

```
生成一个目录，其中包含 lerna.json 和 pacakge.json，以及一个空的 packages 文件夹

lerna init
```

```
在子项目运行 npm install xxx 依赖后会生成 node_modules 文件夹， 在 lerna repo 中只需要子项目的 package.json 中有依赖的声明就行了。
在 lerna 中提供共享 node_modules 的能力，所以子项目中不需要有 node_modules
通过 lerna clean 指令可以一下子删除所有子项目中的 node_modules 文件夹

lerna clean
```
```
创建一个包名为leona-project
lerna create leona-project
```
```
scope 可以理解为作用域，代表我的react 依赖只安装在leona-project 包名下面， 而不是全部都安装，如果需要全部安装的话， 则 lerna add react 
lerna add react --scope=xxx
```
```
//查看当前包名有哪些
lerna list
```
```
/*
lerna add 时也发现了，为某个 package 安装的包被放到了这个 package 目录下的 node_modules 目录下。这样对于多个 package 都依赖的包，会被多个 package 安装多次，并且每个 package 下都维护 node_modules ，也不清爽。于是我们使用 --hoist 来把每个 package 下的依赖包都提升到工程根目录，来降低安装以及管理的成本。
经过依赖提升后，repo 目录结构如下：如果子项目的依赖包版本有不同，那么相同版本的包会提升到最外层。子项目下的 node_modules 会保留自己独有的依赖版本。
并不是所以依赖都会提升到外层，例如 devDependencies 提供的可执行文件仍然会留在子项目的 node_modules 下面，以保证子项目下能正确运行可执行文件
*/
lerna bootstrap --hoist
```
```
//执行所有子项目中定义在 package.json 中的同名的命令
lerna run <script>
```
```
//会把当前的包名以依赖的形式进行本地联调，而不会影响到全局的包， 类型npm link
lerna link
```
```
//运行 lerna version 后命令行会提示新版版本号进行选择（为本次所有的修改自增一个版本号）， 选择对应的版本号后，lerna version 命令会将自动创建对应版本版本号的 tag，并自动 push 到 git 远端服务器
git add . 
git commit -m "commit"
lerna version
```
```
//lerna publish 会自动在内部运行 lerna version 进行版本号递增、打 tag 、push 远程 git 服务器等操作。
//此外，lerna publish 还会在内部自动运行 npm publish， 将新产生的版本推送到 npm registry （这些能工作的前提是我们已经配置好了 npm 和 git 的认证信息）
lerna publish
```

# 使用lerna + yarn workspace
```
yarn install # 等价于 lerna bootstrap --npm-client yarn --use-workspaces

lerna clean # 清理所有的node_modules 
yarn workspaces run clean # 执行所有package的clean操作

//将packageA作为packageB的依赖进行安装、删除
yarn workspace packageB add packageA
yarn workspace packageB remove packageA 


//给所有的package安装依赖
yarn workspaces add lodash
yarn workspaces remove lodash 


//给root 安装依赖
yarn add -W -D typescript
yarn remove -W -D typescript
```

- yarn的workspace暂时并未支持按照拓扑排序规则执行命令,lerna支持按照拓扑排序规则执行命令
```
lerna run --stream --sort build
```
- 规范提交
  - commitizen 和 cz-lerna-changelog 
- 信息验证
  - commitlint && husky
