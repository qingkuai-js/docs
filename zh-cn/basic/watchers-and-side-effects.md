# 监视器与副作用

监视器与副作用 API 是框架响应性系统的一部分，用于监听响应性变量的变化：通过 preWatch、preEffect、syncWatch，syncEffect、watch 以及 effect，你可以将监听逻辑插入到调度流程的前、中、后阶段。

-   syncWatch，syncEffect：在调度过程中触发，适用于常规的副作用逻辑；
-   watch，effect：在更新任务调度完成之后触发，适用于需要等待状态稳定之后的处理逻辑；
-   preWatch，preEffect：在变更被侦测到后、更新任务调度尚未开始前触发，适用于日志记录、变更预处理等操作；

<div class="custom-block warning">监视器与副作用 API 是为熟悉 <a href="https://cn.vuejs.org">Vue</a> 等其他框架的开发者提供的一种过渡式工具，旨在降低上手门槛。然而，我们并不推荐在正式项目中广泛使用这些 API。其原因在于：副作用通常是以回调形式注册的，被触发的位置不在调用栈中直接体现，调用关系不直观、不易跟踪，也难以借助 IDE 的跳转、查找引用等语言服务工具进行有效的代码审查与维护。若对可维护性、可读性有较高要求，建议优先使用显式的数据流和函数组合调用方式来组织响应逻辑。</div>

---

## 监视器

观察下面的代码，我们为 `name` 变量注册了一个监视器，当它的值被修改时，注册监视器时提供的回调方法会被调用，这个回调方法接受两个参数：修改前的值以及修改完成后的值（当前值）：

|js|ts|

```qk
<lang-js>
    import { watch } from "qingkuai"

    let paragraph
    let name = "Javascript"
    watch(
        () => name,
        (pre, cur) => {
            console.log(pre, cur) // Javascript QingKuai
            console.log(paragraph.textContent) // name is: QingKuai
        }
    )
</lang-js>

<p &dom={paragraph}>name is: {name}</p>
<button @click={name = "QingKuai"}>Change Name</button>
```

```qk
<lang-ts>
    import { watch } from "qingkuai"

    let name = "Javascript"
    let paragraph!: HTMLParagraphElement
    watch(
        () => name,
        (pre, cur) => {
            console.log(pre, cur) // Javascript QingKuai
            console.log(paragraph.textContent) // name is: QingKuai
        }
    )
</lang-ts>

<p &dom={paragraph}>name is: {name}</p>
<button @click={name = "QingKuai"}>Change Name</button>
```

### 前置监视器

`watch` 方法的回调会在更新任务调度完成后触发（页面更新完成后），如果你想在更新任务调度前触发回调，可以使用 `preWatch`：

|js|ts|

```qk
<lang-js>
    import { preWatch } from "qingkuai"

    let paragraph
    let name = "Javascript"
    preWatch(
        () => name,
        (pre, cur) => {
            console.log(pre, cur) // Javascript QingKuai
            console.log(paragraph.textContent) // name is: Javascript
        }
    )
</lang-js>

<p &dom={paragraph}>name is: {name}</p>
<button @click={name = "QingKuai"}>Change Name</button>
```

```qk
<lang-ts>
    import { preWatch } from "qingkuai"

    let name = "Javascript"
    let paragraph!: HTMLParagraphElement
    preWatch(
        () => name,
        (pre, cur) => {
            console.log(pre, cur) // Javascript QingKuai
            console.log(paragraph.textContent) // name is: Javascript
        }
    )
</lang-ts>

<p &dom={paragraph}>name is: {name}</p>
<button @click={name = "QingKuai"}>Change Name</button>
```

### 同步监视器

无论是 `watch` 还是 `preWatch`，它们的回调都是异步触发的，然而有些时候你可能希望同步触发回调，此时可以使用 `syncWatch`：

```qk
<lang-js>
    import { syncWatch } from "qingkuai"

    let name = "Javascript"

    function handleChangeName() {
        name = "QingKuai"
        console.log("---")
        // log: Javascript Qingkuai \n ---
    }

    syncWatch(
        () => name,
        (pre, cur) => {
            console.log(pre, cur)
        }
    )
</lang-js>

<p>name is: {name}</p>
<button @click={handleChangeName}>Change Name</button>
```

### 便捷注册

qingkuai 内建了三个便捷注册监视器的辅助函数：wat、Wat 和 waT，分别对应 syncWatch、preWatch 和 watch。其中 wat 是 watch 的缩写，另外两个方法名称中大写字母的位置表示回调的触发时机 —— 大写在前表示在调度更新前触发，大写在后表示在调度更新后触发。使用辅助函数注册监视器时，你可以直接传入要监视的目标作为第一个参数，qingkuai 编译器会将其转换为一个 getter。例如下面的写法都是两两等效的：

```js
// 注册同步监视器
syncWatch(
    () => xxx,
    (p, c) => console.log(p, c)
)
wat(xxx, (p, c) => console.log(p, c))

// 注册更新调度前置监视器
preWatch(
    () => xxx,
    (p, c) => console.log(p, c)
)
Wat(xxx, (p, c) => console.log(p, c))

// 注册更新调度后置监视器
watch(
    () => xxx,
    (p, c) => console.log(p, c)
)
waT(xxx, (p, c) => console.log(p, c))
```

---

## 副作用

与监视器 API 的不同之处是，副作用 API 会自动收集回调中的依赖项（响应性变量），并在这些依赖项发生变化时触发回调：

```qk
<lang-js>
    import { effect } from "qingkuai"

    let [n1, n2] = [10, 20]
    effect(() => console.log(n1, n2))
</lang-js>

<p>n1: {n1}; n2: {n2}</p>
<button @click={n1++}>Change n1</button>
<button @click={n2++}>Change n2</button>
```

副作用 API 的回调方法会在注册时立即执行一次以进行依赖项收集，有很多场景可以利用这一特性，例如当我们需要请求某个应用 API 地址获取用户信息并展示时：

|js|ts|

```qk
<lang-js>
    import { effect } from "qingkuai"

    let userId = 0
    let userInfo = null
    effect(async () => {
        const response = await fetch(`https://example.com/user/info/${userId}`)
        userInfo = await response.json()
    })
</lang-js>

<qk:spread #if={userInfo}>
    <p>User id: {userInfo.id}</p>
    <p>User name: {userInfo.name}</p>
</qk:spread>
```

```qk
<lang-ts>
    import { effect } from "qingkuai"

    interface UserInfo {
        id: number
        name: string
    }

    let userId = 0
    let userInfo: UserInfo | null = null
    effect(async () => {
        const response = await fetch(`https://example.com/user/info/${userId}`)
        userInfo = await response.json()
    })
</lang-ts>

<qk:spread #if={userInfo}>
    <p>User id: {userInfo.id}</p>
    <p>User name: {userInfo.name}</p>
</qk:spread>
```

| effect 回调在更新任务调度完成后触发，若要注册前置或同步副作用可改用：`preEffect` 和 `syncEffect`。

---

## 清理监视器与副作用

监视器 API 及副作用 API 的注册方法都会返回一个清理函数，调用它可以清理已注册的监视器或副作用：

```js
const unwatch = watch(
    () => xxx,
    () => {}
)
const unwat = wat(xxx, () => {})
const uneffect = effect(() => {})

// 清理（停止）监视器与副作用
unwat()
unwatch()
uneffect()
```

清理函数还可以接受一个函数作为参数，以执行额外的清理逻辑：

```js
const unwatch = waT(xxx, () => {})
unwatch(() => {
    /* 额外的清理逻辑 */
})
```
