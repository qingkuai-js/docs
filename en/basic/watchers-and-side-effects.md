# Watchers and Side Effects

Watchers and Side Effects APIs are part of the framework's reactivity system, used to monitor changes in reactive variables: through preWatch, preEffect, syncWatch, syncEffect, watch, and effect, you can insert monitoring logic at the pre-, mid-, and post-scheduling phases.

-   syncWatch, syncEffect: Triggered during the scheduling process, suitable for regular side effect logic;
-   watch, effect: Triggered after update task scheduling completes, suitable for processing logic that requires waiting for state stabilization;
-   preWatch, preEffect: Triggered after changes are detected but before update task scheduling begins, suitable for logging, change preprocessing, etc.;

<div class="custom-block warning">
    The Watchers and Side Effects APIs are transitional tools provided for developers familiar with frameworks like <a href="https://cn.vuejs.org">Vue</a>, aiming to lower the learning curve. However, we don't recommend widespread use of these APIs in production projects. The reason is: side effects are typically registered as callbacks, triggered at locations not directly reflected in the call stack, making call relationships non-intuitive and hard to trace. They also make it difficult to use IDE features like jump-to-definition or find-references for effective code review and maintenance. For better maintainability and readability, we recommend prioritizing explicit data flow and function composition to organize reactive logic.
</div>

---

## Watchers

Observe the following code where we register a watcher for the `name` variable. When its value changes, the provided callback method will be invoked with two parameters: the previous value and the new (current) value:

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

### Pre-Watchers

The `watch` callback triggers after update task scheduling completes (after page updates). If you need it to trigger before scheduling, use `preWatch`:

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

### Synchronous Watchers

Both `watch` and `preWatch` callbacks trigger asynchronously. For synchronous triggering, use `syncWatch`:

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

### Convenience Registration

qingkuai provides three helper functions for convenient watcher registration: wat, Wat, and waT, corresponding to syncWatch, preWatch, and watch respectively. 'wat' is short for watch, while the capitalized letters in the other two indicate trigger timing - capital first means pre-scheduling, capital last means post-scheduling. When using these helpers, you can directly pass the monitoring target as the first parameter, and qingkuai's compiler will convert it to a getter. For example, these pairs are equivalent:

```js
// Register sync watcher
syncWatch(
    () => xxx,
    (p, c) => console.log(p, c)
)
wat(xxx, (p, c) => console.log(p, c))

// Register pre-scheduler update watcher
preWatch(
    () => xxx,
    (p, c) => console.log(p, c)
)
Wat(xxx, (p, c) => console.log(p, c))

// Register post-scheduler update watcher
watch(
    () => xxx,
    (p, c) => console.log(p, c)
)
waT(xxx, (p, c) => console.log(p, c))
```

---

## Side Effects

Unlike watcher APIs, side effect APIs automatically collect dependencies (reactive variables) in callbacks and trigger when these dependencies change:

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

Side effect callbacks execute immediately upon registration for dependency collection. This feature enables many use cases, like fetching user information from an API endpoint:

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

| effect callbacks trigger after update scheduling. For pre- or synchronous side effects, use `preEffect` and `syncEffect` instead.

---

## Cleaning Up Watchers and Side Effects

Both watcher and side effect registration methods return a cleanup function to remove registered watchers/effects:

```js
const unwatch = watch(
    () => xxx,
    () => {}
)
const unwat = wat(xxx, () => {})
const uneffect = effect(() => {})

// Clean up watchers and side effects
unwat()
unwatch()
uneffect()
```

The cleanup function can also accept another function for additional cleanup logic:

```js
const unwatch = waT(xxx, () => {})
unwatch(() => {
    /* Additional cleanup logic */
})
```
