# 生命周期

在组件化开发中，生命周期是理解组件行为、把握执行时机的关键。每个组件从创建、挂载、更新到卸载，都会经历一系列生命周期阶段。为了更好地控制这些过程，Qingkuai 提供了多个生命周期钩子函数。通过这些钩子，你可以在合适的时机插入逻辑，实现数据初始化、事件绑定、清理操作等功能，使组件更健壮，也更易于维护：

- 挂载：`onAfterMount`（组件挂载完成后）；
- 卸载：`onBeforeDestroy`（组件卸载前）、`onAfterDestroy`（组件卸载完成后）；
- 更新：`onBeforeUpdate`（每次更新调度前触发）、`onAfterUpdate`（更新调度结束后触发）；

---

## 注册回调

要注册生命周期回调，需要先导入对应的钩子函数：

```js
import { onAfterMount } from "qingkuai"

onAfterMount(() => {
    console.log("组件挂载完毕")
})
```

<div class="custom-block tip">
    注意，运行时 API 中并不存在一个名为 <code>onBeforeMount</code> 的钩子函数，因为整个嵌入脚本块会在组件挂载前执行，直接在其中编写组件挂载前的逻辑即可，无需额外的钩子函数。
</div>

在渲染过程中遇到组件时，Qingkuai 会同步记录当前正在挂载的组件实例。因此，不能在异步逻辑中注册生命周期回调，请不要像下面这样做：

```js
import { onBeforeDestroy } from "qingkuai"

setTimeout(() => {
    onBeforeDestroy(() => {})
})
```

但你可以在外部封装逻辑中注册生命周期回调，例如：

```js
// util.js
import { onBeforeUpdate } from "qingkuai"

export function useBeforeUpdateMiddleware() {
    onBeforeUpdate(() => {
        console.log("before update")
    })
}
```

```qk
<lang-js>
    import { useBeforeUpdateMiddleware } from "./util"

    useBeforeUpdateMiddleware()
    // other logics...
</lang-js>
```
