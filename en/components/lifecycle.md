# Lifecycle

In component-based development, lifecycle is the key to understanding component behavior and controlling execution timing. Every component goes through a series of lifecycle phases, from creation to mounting, updating, and unmounting. To give developers better control over these phases, Qingkuai provides a number of lifecycle hook functions. Through these hooks, you can insert logic at the right time for data initialization, event binding, cleanup, and more, making components more robust and easier to maintain:

- Mounting: `onBeforeMount` (before the component is mounted), `onAfterMount` (after the component is mounted)
- Unmounting: `onBeforeDestroy` (before the component is unmounted), `onAfterDestroy` (after the component is unmounted)
- Updating: `onBeforeUpdate` (before each update scheduling), `onAfterUpdate` (after update scheduling is complete)

---

## Registering Callbacks

To register a lifecycle callback, first import the corresponding hook function:

```js
import { onAfterMount } from "qingkuai"

onAfterMount(() => {
    console.log("component mounted")
})
```

During rendering, Qingkuai records the currently mounting component instance synchronously. Because of that, you cannot register lifecycle callbacks in async logic. Do not do this:

```js
import { onBeforeMount } from "qingkuai"

setTimeout(() => {
    onBeforeMount(() => {})
})
```

However, you can register lifecycle callbacks inside external encapsulated logic, for example:

```js
// util.js
import { onBeforeUpdate } from "qingkuai"

export function useUpdatePreMiddleWare() {
    console.log("before update")
}
```

```qk
<lang-js>
    import { useUpdatePreMiddleWare } from "./util"

    useUpdatePreMiddleWare()
    // other logics...
</lang-js>
```
