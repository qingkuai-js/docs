# 术语参考

为了帮助你更高效地理解和使用 Qingkuai，本节汇总了文档中的常见术语，并给出简短说明。无论你是刚接触该框架，还是正在深入源码，都可以通过本节快速厘清名词含义，减少理解偏差。

<div class="custom-block tip">
    本节主要用于统一术语理解与表达方式，解释以文档中的常见语境为准。
</div>

---

## 组件文件

组件文件是指以 `.qk` 为扩展名的文件，每个组件文件都表示一个组件声明。

参考：[语法介绍](../getting-started/introduction.html#简介)、[组件基础](../components/basic.html)

---

## 事件

事件是指以 `@` 前缀声明的事件属性，用于在模板中绑定交互逻辑，或向组件外部暴露可调用的回调。

参考：[事件处理](../basic/event-handling.html)、[组件事件](../components/attributes.html#事件)

---

## 静态属性

静态属性是指在模板中声明时不依赖插值表达式，其值为固定的属性，通常用于绑定纯字符串数据。

参考：[静态属性](../basic/interpolation.html#静态属性)

---

## 动态属性

动态属性是指以 `!` 前缀声明、其值由插值表达式计算得到的属性，适用于绑定布尔值、对象等非纯字符串数据。

参考：[动态属性](../basic/interpolation.html#动态属性)

---

## 引用属性

引用属性是指以 `&` 前缀声明的可写属性通道。它不仅可用于组件标签，也可用于特定原生 HTML 标签（如 `input`、`textarea`、`select` 等）来建立值同步或引用传递。在组件内部，通常通过 `refs` 访问并更新这类数据。

参考：[引用属性](../basic/reference-attributes.html)、[表单输入处理](../basic/forms.html)、[组件属性](../components/attributes.html#引用属性)

---

## 响应式与响应性

这三个术语相关但侧重点不同，文档中可按下面方式理解：

- 响应式：一种能力或机制，表示值变化可被追踪并触发依赖更新。
- 响应性：对该能力本身的抽象描述，常用于讨论系统行为或设计特性。
- 响应式值：具备响应式能力的具体数据单元（如由编译器推导或通过相关 API 创建的值）。

参考：[响应性](../basic/reactivity.html)

---

## 监视器

监视器是指对响应式值变化进行监听并执行回调的机制，常用于副作用控制、状态对比和清理逻辑。

参考：[监视器](../basic/watchers-and-side-effects.html#监视器)

---

## 副作用

副作用是指依赖响应式状态并在其变化后执行的逻辑，常见于 DOM 交互、异步请求和外部系统同步。

参考：[副作用](../basic/watchers-and-side-effects.html#副作用)

---

## 作用域

作用域表示某段模板或脚本中可访问标识符的范围，尤其在插槽和指令上下文中会影响变量可见性。

参考：[插槽](../components/slots.html#作用域)

---

## qk:spread

`qk:spread` 是 Qingkuai 的内置元素，常作为指令的虚拟挂载点；其本身不会渲染为真实 DOM 元素。

参考：[内置元素](../components/builtin-elements.html)

---

## props

`props` 是编译器内建标识符，用于在组件内部读取外部传入的普通属性与事件属性。

参考：[组件属性](../components/attributes.html)、[内建标识符](./intrinsics.html)

---

## refs

`refs` 是编译器内建标识符，用于在组件内部访问引用属性并执行可写更新。

参考：[组件属性](../components/attributes.html#引用属性)、[内建标识符](./intrinsics.html)

---

## 插值属性

插值属性是对一组特殊属性的统称，包括 `指令`、`动态属性`、`引用属性` 和 `事件`。

参考：[指令](../basic/compilation-directives.html)、[动态属性](../basic/interpolation.html#动态属性)、[引用属性](../basic/reference-attributes.html)、[事件处理](../basic/event-handling.html)、[组件属性](../components/attributes.html)

---

## 插值块

插值块是指模板中所有使用一对花括号包裹、用于嵌入 JS/TS 表达式的位置。它既包括 [插值属性](#插值属性) 的值部分，也包括 [文本插值](../basic/interpolation.html#文本插值) 部分。

---

## 嵌入脚本块

嵌入脚本块是由 `lang-js` 或 `lang-ts` 标签包裹的区域，用于编写会被编译器处理的脚本内容。

参考：[语法介绍](../getting-started/introduction.html#简介)、[设计理念](../getting-started/introduction.html#设计理念)

---

## 嵌入样式块

嵌入样式块是指在组件文件中使用 `lang-css`、`lang-scss`、`lang-sass`、`lang-less`、`lang-stylus`、`lang-postcss` 标签包裹的区域，用于编写会被编译器处理的样式内容。

参考：[语法介绍](../getting-started/introduction.html#简介)、[样式表](../components/stylesheets.html)

---

## 嵌入语言标签

嵌入语言标签是指 `lang-js`、`lang-ts`、`lang-css`、`lang-scss`、`lang-sass`、`lang-less`、`lang-stylus`、`lang-postcss` 这 8 个标签，用于嵌入需要编译的脚本与样式内容。

参考：[语法介绍](../getting-started/introduction.html#简介)、[样式表](../components/stylesheets.html)

---

## 插槽出口

插槽出口是指组件内部通过 `slot` 标签声明的占位位置，用于接收外部传入的插槽内容。

参考：[插槽](../components/slots.html)

---

## 插槽内容

插槽内容是指组件使用方传入的子内容，它会被渲染到对应的[插槽出口](#插槽出口)位置。

参考：[插槽](../components/slots.html)

---

## 编译器内建标识符

编译器内建标识符是指在组件文件中无需声明、可被编译器直接识别并处理的保留标识符，主要包括对象类标识符和方法类标识符。

对象类标识符包括：`refs`、`props`、`slots`。

方法类标识符：即[内建方法](#内建方法)

其中，`refs` 用于访问引用属性，`props` 用于访问普通属性与事件属性，`slots` 用于检查插槽是否传入内容。

参考：[组件属性](../components/attributes.html)、[插槽](../components/slots.html)、[内建方法](#内建方法)、[内建标识符](./intrinsics.html)

---

## 内建方法

内建方法是编译器内建标识符的一部分，指 `reactive`、`shallow`、`alias`、`derived`、`derivedExp`、`watchExp`、`preWatchExp`、`postWatchExp`、`syncWatchExp`、`defaultProps`、`defaultRefs` 这 11 个可直接在组件文件中使用的方法标识符。它们本质是编译标记，会在编译阶段被转换为内部方法调用。

参考：[响应性声明](../basic/reactivity.html#响应性声明)、[监视器](../basic/watchers-and-side-effects.html#监视器)、[内建标识符](./intrinsics.html)
