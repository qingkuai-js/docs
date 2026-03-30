# 异步组件

在现代前端应用中，按需加载是优化加载体验与渲染性能的重要手段。异步组件（Async Component）正是为此而生的一种机制：它允许你在真正需要某个组件时再加载，而不是在初始阶段就将其打包进主应用中。这种方式不仅能有效减少初次加载体积，还能改善资源加载速度，并提升首屏渲染性能；同时也便于配合路由或条件渲染进行更高效的资源管理。在 Qingkuai 中，异步组件通过组合使用 [异步处理](../basic/compilation-directives.html#异步处理) 相关指令实现，整个过程无需特殊配置，使用方式简洁且具备良好的扩展性。

---

## 动态导入

很多构建工具会对 [动态导入](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/import) 的模块进行代码分割优化。对于组件，我们同样可以利用这一点：

```qk
<qk:spread
    #await={import("./Component.qk")}
    #then={{default: Component}}
>
    <Component />
</qk:spread>
```

我们还可以为其添加加载状态，以及加载失败时展示的模板内容：

```qk
<div #await={import("./Component.qk")}>
    Loading...
</div>
<qk:spread #then={{default: Component}}>
    <Component />
</qk:spread>
<div #catch>Fail to load Component.qk</div>
```

---

## 异步返回

我们还可以在自定义的异步逻辑中返回组件：

```qk
<lang-js>
    import Comp1 from "./Comp1.qk"
    import Comp2 from "./Comp2.qk"

    async function getComponent() {
        return (await isOk()) ? Comp1 : Comp2
    }
</lang-js>

<div
    class="comp-box"
    #await={getComponent()}
    #then={ObtainedComponentModule}
>
    <ObtainedComponentModule.default />
</div>
```
