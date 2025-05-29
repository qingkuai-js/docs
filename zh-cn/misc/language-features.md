# 语言功能

qingkuai 并不在编译器中内建复杂的语法扩展，而是通过基于[LSP](https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/)（语言服务协议）的语言服务器，提供了丰富的语言功能支持。这些功能包括类型推导、智能补全、错误提示、快速跳转、语义高亮等，覆盖组件属性、插槽、样式作用域、指令系统、引用传递等框架特性。借助 LSP，qingkuai 在保持语法简洁的同时，显著提升了开发体验与类型安全。

---

## IDE 扩展

目前我们仅在 [vscode](https://code.visualstudio.com) 中发布了扩展，你可以在扩展市场中搜索 `QingKuai` 来安装它。

<img src="https://qingkuai-js.oss-cn-beijing.aliyuncs.com/docs/extension.png" />

<div class="custom-block tip">
    当你在IDE中遇到的bug时，应该在 qingkuai 的 <a href="https://github.com/qingkuai-js/language-features">language-features</a> 仓库中提交issue。
</div>

---

## Emmet

qingkuai 语言服务器提供了良好的 [emmet](https://emmet.io) 支持，但由于动态属性和 emmet 去除属性的语法冲突，所以在组件文件中改用 `-` 字符来去除属性。下面的示例代码用于在组件文件中通过 emmet 语法创建一个不带 type 属性的 input 标签：

```txt
input[-type]
```

而原有的语法：

```txt
input[!type]
```

会创建一个动态属性：

```qk
<input !type={}>
```

---

## 格式化

qingkuai 语言服务器内置了格式化文档功能，该功能由 [prettier-plugin-qingkuai](https://www.npmjs.com/package/prettier-plugin-qingkuai) 实现。当组件文件中存在语法错误时格式化可能会失败，这种情况下你可以在 IDE 的 `output` 窗口查看失败信息：

<img src="https://qingkuai-js.oss-cn-beijing.aliyuncs.com/docs/format-error.png" />

---

## 代码跳转

代码跳转时开发时经常需要用到的功能，这项功能在组件文件中有一些额外的用法：

-   查找插槽定义：按住 meta 键并在组件一级子元素的 slot 属性上单击鼠标左键；
-   查找组件定义：按住 meta 键并在组件标签或嵌入脚本中的组件标识符上单击鼠标左键；
-   查找插槽引用：在 slot 标签上单击鼠标右键并选择 “Go to References”，或开启 “代码镜头” 功能；
-   查找组件引用：在嵌入语言标签上单击鼠标右键并选择 “Go to references”，或开启 “代码镜头” 功能；
