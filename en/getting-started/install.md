# Installation

If you just want to try out qingkuai quickly, you can experiment with the [Online Playground](https://qingkuai.dev).

For the optimal development experience, we strongly recommend installing the project locally. Using [create-qingkuai](https://www.npmjs.com/package/create-qingkuai), you can swiftly set up a local project - just run the following command in your terminal:

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

If you want to install the `typescript` version, simply pass the `-ts` option:

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

After the project is created, you need to enter the project directory and install the required packages, then start the local development server:

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
