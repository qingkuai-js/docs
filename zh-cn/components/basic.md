# 基础

在 qingkuai 中，组件是构建用户界面的基本单位，每一个组件都代表着一个独立、可复用的界面片段，既可以是一个简单的按钮，也可以是一个复杂的页面。组件天然具有封装性和组合性，使界面开发更加清晰、高效。

下面的图片展示了一个博客页面的组件化表示，其中每个区域都表示一个组件，相同颜色的区域表示组件的复用：

<img width="70%" src="/static/medias/components.png" style="margin-left: 15%;" />

---

## 定义及使用

qingkuai 的组件定义采取了与 [vue](https://cn.vuejs.org) 和 [svelte](https://svelte.dev) 相同的方式，即文件级组件定义。每个组件被定义在一个扩展名为 `.qk` 文件中，经编译器处理后，这个文件会被转换为一个具有[默认导出](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules#%E9%BB%98%E8%AE%A4%E5%AF%BC%E5%87%BA%E4%B8%8E%E5%85%B7%E5%90%8D%E5%AF%BC%E5%87%BA)的 [Javascript 模块](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules)。

假设我们已经通过 `Component.qk` 文件定义了一个组件，在其他组件文件中，可以通过 [import 语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import) 导入，并在模板部分添加与导入标识符名称相同的标签来重复使用它：

```qk
<lang-js>
    import Component from "./path/to/Component.qk"
</lang-js>

<Component />
<Component />
```

<div class="custom-block tip">之后我们都用 <code>组件文件</code> 这一术语指代扩展名为 <code>.qk</code> 的文件。</div>

组件名称支持使用串型（kebab）格式，下面的写法是等效的：

```qk
<MyComponent />
<my-component />
```

默认情况下，格式化组件文件时会将所有组件名称整理为驼峰格式，但你可以向组件文件所在目录或其上级目录中添加 `.prettierrc` 文件并输入以下内容将组将名称偏好修改为 kebab 格式：

```json
{
    "qingkuai": {
        "componentTagFormatPreference": "kebab"
    }
}
```

<div class="custom-block tip">使用此配置时 qingkuai 语言服务器在提供组件标签的补全建议时也会优先提示串型组件标签。</div>
