# 编译指令

指令是 qingkuai 的核心组成部分，它们是以 `#` 开头的特殊属性，用于指示 qingkuai 编译器如何生成对应的 JavaScript 代码。qingkuai 提供了一套功能丰富的内置指令系统，涵盖了流程控制、渲染控制和异步处理等多个方面：

-   渲染控制指令：target、html、show 用于控制内容的插入位置与显示；
-   流程控制指令：for 、if、 el-if 、else 用于控制结构性渲染逻辑；
-   异步处理指令：await、then、catch 用于响应异步数据的变化；

另外还有一个用于接收组件插槽参数的指令`slot`，我们会在引申出[组件](../components/basic.html)的概念之后进行介绍。

---

## 条件渲染

在 qingkuai 中，我们通过结合使用 if、elif 和 else 指令来编写条件渲染逻辑，这与 Javascript 中的 if、else if 以及 else 关键字非常类似。设想这样一个场景：当用户未登录时显示登录提示，登录后则展示用户信息 —— 这是前端开发中非常常见的需求。此时，我们就可以借助 qingkuai 的条件渲染功能轻松实现这一逻辑：

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
    上方示例代码中 <code>qk:spread</code> 标签用来作为指令的虚拟挂载点，它不会被渲染到页面中，可以理解为此元素具有的指令会被一一应用到所有子节点上。这样设计的目的是为了避免引入多余的无意义元素，当然这也使得我们为文本节点添加指令变得可能。关于它的更多用法以及使用细节我们会在<a href="../components/builtin-elements.html">内置元素</a>中介绍。
</div>

当然我们还可以在 `if` 和 `else` 之间插入一些 `elif` 指令作为分支节点：

```qk
<p #if={language === "qk"}>QingKuai</p>
<p #elif={language === "js"}>Javascrip</p>
<p #elif={language === "ts"}>Typescrip</p>
<p #else>Language is not Qingkuai, Javascript, or Typescript.</p>
```

另外我们还可以使用 `show` 指令控制元素在页面中是否可见。与上述三个指令搭配使用的不同之处是 show 指令不会从页面中卸载元素，而只是简单地控制是否为元素添加 `display: none;` 这条样式规则，所以对于需要频繁切换可见性的元素，更应该使用 show 指令，因为它的开销更小：

```qk
<div #show={visiable}>
    <!-- some contents> -->
</div>
```

---

## 列表渲染

在 qingkuai 中可以很方便地完成列表渲染，下面就是一个最基础的使用示例，在开发测试时经常这样使用来快速地创建一个列表渲染：

```qk
<p #for={3}>Paragraph in list rendering.<p>
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

如果你使用过 [vue](https://cn.vuejs.org)，可能会问为什么 `for` 指令中使用 `of` 作为迭代关键词而非 `in` ，这是因为 in 关键字可以出现在 Javascript 表达式中，而 of 则不行，例如如果使用 in 话，下面这种情况就变得难以处理：

```qk
<p #for={prop in obj ? 3 : 2}>...</p>
```

---

## key 指令

-   若我们使用`for`指令创建了一个列表渲染，在指令依赖的响应性变量发生变更时，列表会更新，此时的更新逻辑是：

    1. 若新列表长度大于旧列表长度则创建新增的列表元素并插入到列表元素结尾；若新列表长度小于旧列表元素则从列表元素结尾删除多余的元素；
    2. 在 qingkuai 调度更新时逐一更新列表元素的属性及 textContent；

    <img class="large-margin" src="/static/medias/no-key-update.gif" style="width: 90%; margin-left: 5%;">

-   可是这样会导致一个问题，若节点本身带有一些状态（通常为表单标签节点），则会导致状态错乱，因为列表的变更并不总是在结尾添加和删除，此时我们就需要使用`#key`指令为列表的每个元素指定一个唯一的键用于区别身份，此时列表变更后更新逻辑就变成了：

    1. 判断 key 在新列表中是否存在，存在则将其对应的元素放到正确的位置；
    2. 在 qingkuai 调度更新时逐一更新列表元素的属性及 textContent；

    <img class="large-margin" src="/static/medias/has-key-update.gif" style="width: 90%; margin-left: 5%;">

所以如果列表渲染的元素带有状态的话，推荐为使用 for 指令的元素添加 key 指令：

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
    qingkuai 运行时通过 <code>"" + [Interpolation expression]</code>将 key 指令的插值转换为字符串，且检查列表中每一项的 key 指令值是否重复，重复时将抛出运行时错误！也就是说在单个列表中每一项的 key 指令值应保持唯一。
</div>

---

## 异步处理

在某些场景下，你可能需要异步地等待嵌入脚本中的某个状态，并在等待完成后再执行渲染，这时可以借助 qingkuai 的异步处理指令轻松实现。await 指令接受一个 [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 作为值，在该 Promise 解析完成后，可以分别通过 then 和 catch 指令，在成功或失败时渲染不同的内容：

```qk
<p #await={pms}>waiting...</p>
<p #then>pms is resolved.</p>
<p #catch>pms is rejected.</p>
```

如果需要访问 Promise 在 resolved 或 rejected 状态下的返回值，只需将 then 或 catch 指令的值设置为一个 Javascript 标识符：

```qk
<p #await={pms}>waiting...</p>
<p #then={res}>pms is resolved and received {res}.</p>
<p #catch={err}>pms is rejected and received {err}.</p>
```

当然，then 和 catch 指令值也支持[解构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring)语法：

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
    pms is resolved and user id is {userId}, user name is {userName}.
</p>
<p #catch={{msg, code}}>pms is rejected and the error code is {code}, msg: {msg}.</p>
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
    qingkuai 的<a href="../components/async-components.html">异步组件</a>也是基于这三个指令实现的。
</div>

---

## html 指令

有时我们需要将一段文本作为 HTML 片段进行渲染，而普通的插值块只是修改 textContent，会将 HTML 内容转义，无法实现预期效果。此时，就可以使用 html 指令来满足这一需求：

```qk
<div class="dynamic-html-content" #html>{htmlStr}</div>
```

上方代码虽然可以达到目的，但外部元素可能不总是必须的，为防止引入多余无意义元素，可使用 `qk:spread` 作为指令的虚拟挂载点：

```qk
<qk:spread #html>{htmlStr}</qk:spread>
```

此外，我们还可以为 html 指令传入一个值，用来描述哪些标签需要保持转义，这在面对不能完全被信任的 html 片段时可以有效防止[xss](https://baike.baidu.com/item/XSS%E6%94%BB%E5%87%BB?fromModule=lemma_search-box)攻击，html 指令值的类型为：

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
    const htmlDireciveConf = {
        escapeStyle: true,
        escapeScript: true,
        escapeTags: ["link", "iframe", "form"]
    }
</lang-js>

<p #html={htmlDireciveConf}>{htmlStr}</p>
```

<div class="custom-block warning">
    若某个标签使用了html指令那么它只能接受一个文本子节点，否则将导致编译器致命错误。
</div>

---

## target 指令

有些场景下你可能需要手动控制元素需要挂载的父元素，例如全屏的弹窗等，使用 target 指令就可以轻松达到目的，target 指令值类型为一个 css 选择器字符串或 [HTMLElement](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement)，例如下面两段代码都会将 div 挂载到 body 元素上：

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
