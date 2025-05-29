# Async Components

In modern frontend applications, on-demand loading is crucial for performance optimization. Async Components provide this capability by allowing components to load only when actually needed, rather than bundling them in the main application initially. This approach not only reduces initial bundle size but also enables more efficient resource management when combined with routing or conditional rendering. In qingkuai, async components are implemented using [async processing](../basic/compilation-directives.html#async-processing) directives, requiring no special configuration while maintaining simplicity and extensibility.

---

## Dynamic Import

Many build tools optimize [dynamic imports](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) through code splitting, which we can leverage for components:

```qk
<lang-js>
    const ComponentModule = import("./Component.qk")
</lang-js>

<qk:spread
    #await={ComponentModule}
    #then={{default: Component}}
>
    <Component />
</qk:spread>
```

We can also add loading states and fallback templates for failures:

```qk
<div #await={ComponentModule}>Loading...</div>
<qk:spread #then={{default: Component}}>
    <Component />
</qk:spread>
<div #catch>Fail to load Component.qk</div>
```

---

## Async Return

Components can also be returned from custom async logic:

```qk
<lang-js>
    import Comp1 from "./Comp1.qk"
    import Comp2 from "./Comp2.qk"

    async function getComponent(){
        return await isOk() ? Comp1 : Comp2
    }
</lang-js>

<div
    class="comp-box"
    #await={getComponent()}
    #then={ObtainedComponent}
>
    <ObtainedComponent />
</div>
```
