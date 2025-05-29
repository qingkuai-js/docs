# 术语参考

为了帮助你更高效地理解和使用 qingkuai，本节收录了文档中常见的术语与概念说明。无论你是刚接触框架的新手，还是深入源码的进阶用户，查阅本节都能快速厘清名词含义，避免歧义和误解。

<div class="custom-block tip">
    本节旨在说明术语的常见用法，而非对其含义作出严格规定，在不同上下文中，某些术语的含义可能会有所差异。
</div>

---

## 组件文件

组件文件是指以 `.qk` 为扩展名的文件，每个组件文件表示一个组件声明。

参考：[语法介绍](../getting-started/introduction.html#简介)、[组件基础](../components/basic.html)

---

## 插值属性

插值属性是对一系列特殊属性的统称，这包括 `指令`、`动态属性`、`引用属性` 以及 `事件`。

参考：[指令](../basic/compilation-directives.html)、[动态属性](../basic/interpolation.html#动态属性)、[引用属性](../basic/reference-attributes.html)、[事件处理](../basic/event-handling.html)、[组件属性](../components/attributes.html)

---

## 插值块

插值块是指所有模板内容中所有用一对花括号包裹来嵌入 JS/TS 表达式的位置。这包括所有[插值属性](#插值属性)的值部分以及[文本插值](../basic/interpolation.html#文本插值)部分。

---

## 嵌入脚本块

嵌入脚本块表示由 `lang-js` 或 `lang-ts` 标签包裹的部分，它们用于嵌入需要编译的脚本内容。

参考：[语法介绍](../getting-started/introduction.html#简介)、[设计理念](../getting-started/introduction.html#设计理念)

---

## 嵌入语言标签

嵌入语言标签是指 `lang-js`、`lang-ts`、`lang-css`、`lang-scss`、`lang-sass`、`lang-less`、`lang-stylus`、`lang-postcss` 这 8 个标签。它们用于嵌入需要编译的脚本和样式内容。

参考：[语法介绍](../getting-started/introduction.html#简介)、[样式表](../components/stylesheets.html)

---

## 内建辅助方法

内建辅助方法是指 `rea`、`stc`、`der`、`wat`、`Wat`、`waT` 这 6 个在组件文件中无需声明就能使用的方法，前三个方法用于创建响应性状态声明，后三个方法用于为响应性状态便捷注册监视器。它们都是一个编译标记，qingkuai 编译器会其转换为内部方法调用。

参考：[响应性声明](../basic/reactivity.html#响应性声明)、[响应深度](../basic/reactivity.html#响应深度)、[衍生响应性状态](../basic/reactivity.html#衍生响应性状态)、[解构响应性声明](../basic/reactivity.html#解构响应性声明)、[监视器](../basic/watchers-and-side-effects.html#监视器)、[前置监视器](../basic/watchers-and-side-effects.html#前置监视器)、[同步监视器](../basic/watchers-and-side-effects.html#同步监视器)

<div class="custom-block warning">内建方法标识符在嵌入脚本块的顶级作用域中不能被重复声明。</div>

---

## 内建对象

内建对象是指 `refs` 和 `props` 这两个在组件文件中无需声明就能访问的对象标识符，它们分别用于存储及访问外部传递给组件的引用属性和其他属性值。

参考：[组件属性](../components/attributes.html)

<div class="custom-block warning">内建对象标识符在嵌入脚本块的顶级作用域中不能被重复声明。</div>
