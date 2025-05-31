# 安装

如果你只是想简单体验一下 qingkuai，可以试试[在线演练场](https://qingkuai.dev)

当然我们更推荐将项目安装在本地以获得最佳的开发体验，通过执行[create-qingkuai](https://www.npmjs.com/package/create-qingkuai)，你可以快速地在本地创建一个项目，仅需在终端执行下面的代码即可：

|npm|pnpm|yarn|

```shell
➜ npm create qingkuai -- "your project name"
```

```shell
➜ pnpm create qingkuai@latest "your project name"
```

```shell
# For Yarn (v1+)
➜ yarn create qingkuai "your project name"

# For Yarn Modern (v2+)
➜ yarn create qingkuai@latest "your project name"

# For Yarn ^v4.11
➜ yarn dlx create-qingkuai@latest "your project name"
```

如果你想要安装使用`typescript`的版本，只需传递`-ts`选项:

|npm|pnpm|yarn|

```shell
➜ npm create qingkuai -- "your project name" -ts
```

```shell
➜ pnpm create qingkuai@latest "your project name" -ts
```

```shell
# For Yarn (v1+)
➜ yarn create qingkuai "your project name" -ts

# For Yarn Modern (v2+)
➜ yarn create qingkuai@latest "your project name" -ts

# For Yarn ^v4.11
➜ yarn dlx create-qingkuai@latest "your project name" -ts
```

项目创建完成后，你需要进入项目目录并安装所需要的包，然后在本地启动开发服务器：

|npm|pnpm|yarn|

```shell
➜ npm install && npm run dev
```

```shell
➜ pnpm install && pnpm run dev
```

```shell
➜ yarn && yarn dev
```
