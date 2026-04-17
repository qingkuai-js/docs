# 编译指令

指令是 Qingkuai 的核心组成部分，它们是以 `#` 开头的特殊属性，用于指示编译器如何生成对应的 JavaScript 代码。Qingkuai 提供了一套功能丰富的内置指令系统，涵盖流程控制、渲染控制和异步处理等多个方面：

- 渲染控制指令：`target`、`html` 用于控制内容的插入位置与显示；
- 流程控制指令：`for`、`if`、`el-if`、`else` 用于控制结构性渲染逻辑；
- 异步处理指令：`await`、`then`、`catch` 用于响应异步数据的变化；

另外还有一个用于接收组件插槽上下文的指令 `slot`，我们会在引申出[组件](../components/basic.html)以及[插槽](../components/slots.html)的概念之后进行介绍。

---

## 条件渲染

在 Qingkuai 中，通过结合使用 `if`、`elif` 和 `else` 指令来编写条件渲染逻辑，这与 JavaScript 中的 `if`、`else if` 以及 `else` 关键字非常类似。设想这样一个场景：当用户未登录时显示登录提示，登录后展示用户信息 —— 这是前端开发中非常常见的需求，借助条件渲染可以轻松实现：

```qk
<qk:spread #if={userInfo}>
    <p>View after logging in.</p>
    <button
        class="login-btn"
        @click={handleLogin}
    >
        Login
    </button>
</qk:spread>
<p #else>Hello {userInfo.name}!</p>
```

<div class="custom-block tip">
    这里使用的 <code>qk:spread</code> 标签用来作为指令的虚拟挂载点，它不会被渲染到页面中，可以理解为此元素具有的指令会被一一应用到所有子节点上。这样设计的目的是为了避免引入多余的无意义元素，当然这也使得我们为文本节点添加指令变得可能。关于它的更多用法以及使用细节我们会在<a href="../components/builtin-elements.html">内置元素</a>中介绍。
</div>

当然我们还可以在 `if` 和 `else` 之间插入一些 `elif` 指令作为分支节点：

```qk
<p #if={language === "qk"}>QingKuai</p>
<p #elif={language === "js"}>Javascrip</p>
<p #elif={language === "ts"}>Typescrip</p>
<p #else>Language is not Qingkuai, Javascript, or Typescript.</p>
```

---

## 列表渲染

在 Qingkuai 中可以很方便地完成列表渲染，下面是一个最基础的使用示例，在开发测试时经常用来快速创建列表渲染：

```qk
<p #for={3}>Paragraph in list rendering.</p>
```

| 这会被渲染为三个连续的 p 标签：

```html
<p>Paragraph in list rendering.</p>
<p>Paragraph in list rendering.</p>
<p>Paragraph in list rendering.</p>
```

当然，for 指令的值不仅可以是一个数字，还可以是数组、对象、字符串、集合（[Set](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)）以及映射（[Map](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)），或是计算结果为这些类型的表达式。此外，还可以使用类似 `for...of` 的语法，为每次遍历的项和索引指定名称。

```qk
<p #for={item, index of [1, 2 , 3]}>{index}: {item}</p>
```

| 此时得到的渲染结果是：

```html
<p>0: 1</p>
<p>1: 2</p>
<p>2: 3</p>
```

依赖 Map 渲染列表：

```qk
<lang-js>
    const languages = new Map([
        ["qk", "Qingkuai"],
        ["js", "Javascript"],
        ["ts", "Typescript"]
    ])
</lang-js>

<p #for={item, index of languages}>{index}: {item}</p>
```

| 此时得到的渲染结果是：

```html
<p>qk: QingKuai</p>
<p>js: Javascript</p>
<p>ts: Typescript</p>
```

为 for 指令的遍历项和索引指定名称时，还可以在遍历项或索引标识符名称处使用[解构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring)语法：

```qk
<lang-js>
    const languageInfos = {
        qk: {
            age: 1,
            name: "QingKuai"
        },
        js: {
            age: 30,
            name: "Javascript"
        },
        ts: {
            age: 13,
            name: "Typescript"
        }
    }
</lang-js>

<p #for={{ name, age }, extension of languageInfos}>
    {name}: file extension is {extension}, released in {2025 - age}.
</p>
```

| 此时得到的渲染结果是：

```html
<p>QingKuai: file extension is qk, released in 2024.</p>
<p>Javascript: file extension is js, released in 1995.</p>
<p>Typescript: file extension is ts, released in 2012.</p>
```

如果你使用过 [Vue](https://cn.vuejs.org)，可能会问为什么 `for` 指令中使用 `of` 作为迭代关键词而非 `in`，这是因为 `in` 关键字可以出现在 JavaScript 表达式中，而 `of` 则不行。例如，如果使用 `in`，下面这种情况就变得难以处理，因为它具有二义性：

- `prop` 是上下文标识符，`in` 关键字之后是表达式
- 整个指令值是一个 JavaScript 表达式，`in` 关键字是表达式的一部分

```qk
<p #for={prop in obj ? 3 : 2}>...</p>
```

---

## key 指令

当我们使用 `for` 指令创建列表渲染时，列表数据发生变化，框架需要更新对应的 DOM 元素。默认情况下，框架采用位置匹配的方式来关联新旧元素：即根据列表索引来对应元素。这种做法在列表仅在末尾添加或删除时工作良好。但当列表中间插入、删除或重新排序项目时，就会导致问题——因为节点的 DOM 状态（如表单输入的值）会被错误地关联到其他数据项。下面的动图展示了这一问题：

为了解决这个问题，可以使用 `#key` 指令为列表中的每个元素指定一个唯一的身份标识，框架就能根据这个 key 准确追踪每个元素，确保即使列表重新排序、插入或删除，元素的状态也能正确跟随其对应的数据项。因此，如果列表渲染的元素带有状态，推荐添加 `#key` 指令：

```qk
<form>
    <input
        #for={user of users}
        #key={user.id}
        !value={user.name}
        placeholder="user name"
    />
</form>
```

<div class="custom-block warning">
    运行时会将 key 指令的值转换为字符串，并检查同一列表中是否存在重复值，重复时将抛出运行时错误。因此，同一列表中每一项的 key 值必须保持唯一。
</div>

---

## 异步处理

在某些场景下，你可能需要异步地等待嵌入脚本中的某个状态，并在等待完成后再执行渲染，这时可以使用异步处理指令来实现。await 指令接受一个 [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 作为值，在该 Promise 完成后，可以分别通过 then 和 catch 指令，在成功或失败时渲染不同的内容：

```qk
<p #await={pms}>waiting...</p>
<p #then>pms is resolved.</p>
<p #catch>pms is rejected.</p>
```

如果需要访问 Promise 在 resolved 或 rejected 状态下的返回值，只需将 then 或 catch 指令的值设置为一个 JavaScript 标识符：

```qk
<p #await={pms}>waiting...</p>
<p #then={res}>pms is resolved and received {res}.</p>
<p #catch={err}>pms is rejected and received {err}.</p>
```

当然，then 和 catch 指令的上下文也支持[解构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring)语法：

```qk
<p #await={pms}>waiting...</p>
<p
    #then={
        {
            id: userId,
            name: userName
        }
    }
>
    pms is resolved and the user id is: {userId}, user name is: {userName}.
</p>
<p #catch={{msg, code}}>pms is rejected and the error code is: {code}, msg: {msg}.</p>
```

如果你不需要在等待时渲染任何元素，只需将 await 指令与 then 或 catch 指令写在同一个标签即可：

```qk
<p
    #await={pms}
    #then={res}
>
    pms is resolved with: {res}
</p>
```

<div class="custom-block tip">
    qingkuai 的<a href="../components/async-components.html">异步组件</a>也是基于异步处理指令组合实现的。
</div>

---

## html 指令

有时我们需要将一段文本作为 HTML 片段进行渲染，而普通的插值块只是修改 textContent，会将 HTML 内容转义，无法实现预期效果。此时，就可以使用 html 指令来满足这一需求：

```qk
<div class="dynamic-html-content" #html>{htmlStr}</div>
```

外部元素可能不总是必须的，为防止引入多余的无意义元素，可使用 `qk:spread` [内置元素](../components/builtin-elements.html) 作为指令的虚拟挂载点：

```qk
<qk:spread #html>{htmlStr}</qk:spread>
```

此外，我们还可以为 html 指令传入一个值，用来描述哪些标签需要保持转义，这在面对不完全可信的 HTML 片段时可以有效防止 [XSS](https://baike.baidu.com/item/XSS%E6%94%BB%E5%87%BB?fromModule=lemma_search-box) 攻击，html 指令值的类型为：

```ts
type HTMLDirectiveValueType = Partial<{
    escapeTags: string[] // 需要保持转义的标签列表
    escapeStyle: boolean // 是否保持转义style标签
    escapeScript: boolean // 是否保持转义script标签
}>
```

我们推荐按照以下 html 指令的使用示例处理不完全受信任的内容：

```qk
<lang-js>
    // 与 qingkuai 包中导出的 DESTRUCT_HTML 常量一致
    const htmlDireciveConf = {
        escapeStyle: true,
        escapeScript: true,
        escapeTags: ["link", "iframe", "form"]
    }
</lang-js>

<p #html={htmlDireciveConf}>{htmlStr}</p>
```

<div class="custom-block warning">
    若某个标签使用了 html 指令，则只能包含一个文本子节点，否则将导致编译器致命错误。
</div>

---

## target 指令

有些场景下你可能需要手动控制元素挂载的父元素，例如全屏弹窗等场景，使用 target 指令可以轻松实现。target 指令的值为一个 CSS 选择器字符串或 [HTMLElement](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement)，例如下面两段代码都会将 div 挂载到 body 元素上：

```qk
<div
    class="page-modal"
    #target={"body"}
></div>
```

```qk
<div
    class="page-modal"
    #target={document.body}
></div>
```
