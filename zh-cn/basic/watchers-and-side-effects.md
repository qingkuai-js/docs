# 监视器与副作用

监视器与副作用 API 是 Qingkuai 响应性系统的一部分，允许你在更新调度器的不同阶段注册回调，以便在响应式值发生变化时执行相应逻辑。根据触发时机的不同，这些 API 分为以下几类：

- watch、effect：普通注册，不能确定与更新调度器的执行顺序先后，先注册先触发；
- syncWatch、syncEffect：被依赖的响应式值发生变化后立即触发，优先于更新调度（异步）器执行；
- preWatch、preEffect：优先于更新调度器执行，适用于需要在状态变更后、更新调度前执行的逻辑；
- postWatch、postEffect：在更新调度完成后触发，适用于需要等待状态稳定或 DOM 更新之后的处理逻辑；

<div class="custom-block warning">监视器与副作用 API 主要面向熟悉 <a href="https://cn.vuejs.org">Vue</a> 等框架的开发者，作为迁移阶段的过渡工具，用于降低上手门槛。我们不建议在正式项目中大范围使用这类 API。原因在于，副作用通常以回调形式注册，其触发位置不会直接体现在调用栈中，调用链不够直观、跟踪成本也更高；同时，这种模式也不利于借助 IDE 的跳转、查找引用等语言服务进行高效的代码审查与维护。若项目对可维护性和可读性要求较高，建议优先采用显式数据流与函数组合来组织响应逻辑。</div>

---

## 监视器

下面的示例为 `name` 变量注册了一个监视器，当该变量的值被修改时，回调方法将被调用。回调接受两个参数：修改前的值和当前值。由于监视器的注册时机早于模板渲染副作用，因此在回调中访问到的 DOM 仍是更新前的状态：

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
            console.log(paragraph.textContent) // name is: Javascript
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
            console.log(paragraph.textContent) // name is: Javascript
        }
    )
</lang-ts>

<p &dom={paragraph}>name is: {name}</p>
<button @click={name = "QingKuai"}>Change Name</button>
```

### 前置监视器

在嵌入语言标签中通过 `watch` 同步注册的监视器会优先于模板渲染副作用注册，其回调在模板更新前触发。但若监视器是在异步逻辑中注册的，则无法保证这一顺序，此时可改用 `preWatch` 以确保回调在模板更新前触发：

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

### 后置监视器

与前置监视器相反，后置监视器会在更新调度完成后触发，适用于需要等待状态稳定或 DOM 更新之后的处理逻辑：

|js|ts|

```qk
<lang-js>
    import { postWatch } from "qingkuai"

    let paragraph
    let name = "Javascript"
    postWatch(
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
    import { postWatch } from "qingkuai"

    let name = "Javascript"
    let paragraph!: HTMLParagraphElement
    postWatch(
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

### 同步监视器

`watch`、 `preWatch` 和 `postWatch` 的回调均为异步触发。若需要同步触发，可以使用 `syncWatch`：

```qk
<lang-js>
    import { syncWatch } from "qingkuai"

    let name = "Javascript"

    function handleChangeName() {
        name = "QingKuai" // logs: Javascript QingKuai
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

标准监视器注册时，第一个参数必须是返回被监听值的 getter 函数，对于简单表达式而言略显冗长。为此，编译器内建了一组与 [derivedExp](/basic/reactivity.html#衍生响应式状态) 作用类似的便捷注册方法：`watchExp`、`preWatchExp`、`postWatchExp`、`syncWatchExp`。这些方法的第一个参数会被编译器自动转换为 getter 函数，可以直接传入表达式：

```js
// 普通注册
watchExp(identifier, (pre, cur) => {
    console.log(pre, cur)
})

// 注册前置监视器
preWatchExp(identifier, (pre, cur) => {
    console.log(pre, cur)
})

// 注册后置监视器
postWatchExp(identifier, (pre, cur) => {
    console.log(pre, cur)
})

// 注册同步监视器
syncWatchExp(identifier, (pre, cur) => {
    console.log(pre, cur)
})
```

---

## 副作用

与监视器不同，`effect` 只接受一个回调函数，依赖追踪与响应逻辑合二为一：回调执行时访问到的响应式值会被自动收集为依赖，任意一个依赖发生变化时该回调都会重新执行。下面的示例中，`effect` 的回调访问了 `userId`，因此每当 `userId` 变化时都会重新发起网络请求并更新用户信息：

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

副作用 API 同样提供了对应不同触发时机的注册方法：

```js
preEffect(() => {})
postEffect(() => {})
syncEffect(() => {})
```

---

## 清理监视器与副作用

监视器及副作用 API 的注册方法都会返回控制句柄对象，这个句柄对象的类型定义如下：

```ts
type EffectHandlers = Record<"stop" | "pause" | "resume", () => void>
```

其中的三个方法分别用于停止、暂停和恢复监视器或副作用的触发：

```js
const effectHandlers = effect(() => {
    // effect logic ...
})
effectHandlers.stop() // 停止并清理副作用
effectHandlers.pause() // 暂停副作用
effectHandlers.resume() // 恢复被暂停的副作用

const watchHandlers = watchExp(identifier, (pre, cur) => {
    // watch logic ...
})
watchHandlers.stop() // 停止并清理监视器
watchHandlers.pause() // 暂停监视器
watchHandlers.resume() // 恢复被暂停的监视器
```

某些情况下，监视器或副作用在重新触发前需要执行清理逻辑。例如，若其中注册了定时器，就需要在下一次触发前将其清除，以避免内存泄漏或逻辑错误。此时可以将清理逻辑封装为函数，并在回调中通过 `return` 语句返回：

|js|ts|

```js
let timer

watchExp(identifier, (pre, cur) => {
    timer = setTimeout(() => {
        // do something ...
    }, 1000)

    return () => clearTimeout(timer) // 监视器重新触发前会先执行这个清理函数
})
```

```ts
let timer: number

watchExp(identifier, (pre, cur) => {
    timer = window.setTimeout(() => {
        // do something ...
    }, 1000)

    return () => clearTimeout(timer) // 监视器重新触发前会先执行这个清理函数
})
```
