# Lifecycle

In component-based development, lifecycle is key to understanding component behavior and mastering component timing. Each component goes through a series of lifecycle phases from creation, mounting, updating to unmounting. To better control these processes, qingkuai provides multiple lifecycle hook functions. These hooks allow developers to insert logic at appropriate times for tasks like data initialization, event binding, and cleanup operations, making components more robust and maintainable:

-   Mounting: onBeforeMount (preparing to mount), onAfterMount (mounting completed);
-   Unmounting: onBeforeDestroy (preparing to unmount), onAfterDestroy (unmounting completed);
-   Updating: onBeforeUpdate (before each reactive update scheduling), onAfterUpdate (after update scheduling completes);

---

## Registering Hook Callbacks

To register lifecycle hook callbacks, we first need to import them:

```js
import { onAfterMount } from "qingkuai"

onAfterMount(() => {
    console.log("组件挂载完毕")
})
```

qingkuai synchronously records the currently mounted component instance during rendering, so lifecycle hook callbacks cannot be registered in async logic. Avoid doing this:

```js
import { onBeforeMount } from "qingkuai"

setTimeout(() => {
    onBeforeMount(() => {})
})
```

But you can register lifecycle hook callbacks in external logic, for example:

```js
// util.js
import { onBeforeUpdate } from "qingkuai"

export function updatePreMiddleWare() {
    console.log("before update")
}
```

```qk
<lang-js>
    import { updatePreMiddleWare } from "./util"

    updatePreMiddleWare()
    // other logics...
</lang-js>
```
