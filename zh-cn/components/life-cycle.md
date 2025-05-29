# 生命周期

在组件化开发中，生命周期是理解组件行为与掌握组件时机的关键。每个组件从创建、挂载、更新到卸载，都会经历一系列生命周期阶段。为了更好地控制这些过程，qingkuai 提供了多个生命周期钩子函数，通过这些钩子，开发者可以在合适的时间插入逻辑，实现如数据初始化、事件绑定、清理操作等功能，使组件更加健壮且易于维护：

-   挂载：onBeforeMount（准备挂载组件）、onAfterMount（组件挂载完毕）；
-   卸载：onBeforeDestroy(准备卸载组件)、onAfterDestroy（组件卸载完毕）；
-   更新：onBeforeUpdate（组件存在响应性更新，每次更新调度前）、onAfterUpdate(更新调度结束后)；

---

## 注册回调

要注册生命周期回调，我们需要先导入它们：

```js
import { onAfterMount } from "qingkuai"

onAfterMount(() => {
    console.log("组件挂载完毕")
})
```

在渲染过程中遇到组件时 qingkuai 会同步记录当前挂载的组件实例，所以不能在异步逻辑中注册生命周期回调，请不要像下面这样做：

```js
import { onBeforeMount } from "qingkuai"

setTimeout(() => {
    onBeforeMount(() => {})
})
```

但你可以在外部逻辑中注册生命周期回调，例如：

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
