# Optimization

In building modern web applications, performance remains a key focus for developers. Whether it's initial load speed, efficiency of reactive updates, or component rendering granularity, proper optimization techniques lead to smoother user experiences. By reducing unnecessary dependency tracking, implementing on-demand rendering, delaying updates, and operating on raw values, we can effectively lower overhead and improve overall runtime efficiency, keeping applications responsive even with complex features.

---

## Tree Shaking

Projects created with [create-qingkuai](https://www.npmjs.com/package/create-qingkuai) use [Vite](https://vite.dev) as the default build tool, which leverages [Rollup](https://rollupjs.org) under the hood. This provides excellent [Tree-shaking](https://developer.mozilla.org/en-US/docs/Glossary/Tree_shaking) capabilities thanks to the static import nature of [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) syntax.

From its initial design, qingkuai fully considered Tree-shaking advantages - all APIs and even directives are tree-shakable. For example, if you don't use the `#for` directive in your code, its related code won't be included in the final bundle. Other directives and features follow the same principle, preventing unused code inclusion at the source and further improving build performance and output size.

```qk
<!--
    Except for the #show directive, runtime code for
    other directives will not be bundled into the final output
-->
<div #show={visible}>...</div>
```

For better Tree-shaking results, prefer ESM (ES Module) format when importing third-party libraries. Compared to CommonJS, ESM supports static analysis, allowing build tools to accurately identify and remove unused module code, reducing bundle size. For example, when using libraries offering both CommonJS and ESM versions, prioritize ESM as this significantly improves final bundle leanness and execution performance:

```js
// Recommended: ESM modules – Tree-shaking friendly
// and results in smaller bundle size
import { debounce } from "lodash-es"

// Not Recommended: CommonJS modules – Cannot be reliably
// tree-shaken and may import the entire lodash library
import { debounce } from "lodash"
```

<div class="custom-block tip">
    When selecting third-party libraries, consider not just feature requirements but also their impact on bundle size. Large libraries may significantly increase initial load times, especially on mobile. Tools like <a href="https://bundlejs.com">bundlejs</a> help evaluate libraries by visually showing the actual size impact of importing specific packages or exports. This information helps make cost-effective choices between features and size, such as using lighter alternatives or importing only needed modules.
</div>

---

## Code Splitting

Code-splitting is a crucial frontend optimization technique that breaks applications into on-demand loaded modules, improving first-screen load speed and reducing resource waste. Build tools like Vite and Rollup automatically split modules based on static dependency analysis and [dynamic imports](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import), supporting manual chunking strategies (like separating third-party libraries) to enhance loading efficiency and browser caching:

```js
// module.js and its dependencies will be split into a
// separate file, and the module will only be loaded
// when the loadModule method is called
function loadModule() {
    return import("./module.js")
}
```

In multi-route applications, avoid bundling all route components into the main app. Instead, implement route-based lazy loading through code splitting, significantly improving loading efficiency and user experience - the core purpose of [Async Components](../components/async-components.html) we introduced earlier:

```qk
<lang-js>
    const ComponentModule = import ("./Component.qk")
</lang-js>

<qk:spread
    #await={ComponentModule}
    #then={{ default: Component }}
>
    <Component />
</qk:spread>
```

---

## Reactive Updates

Frequent reactive state modifications cause performance waste since each change triggers reactive computations. The `updateWithRaw` method optimizes this by allowing direct raw data modifications with a single reactive update after all changes complete:

```js
import { updateWithRaw } from "qingkuai"

const length = Math.pow(10, 5) // 100,000
const arr = Array.from({ length }, (_, i) => i)

// Microsecond-level updates, almost indistinguishable
// from ordinary array modifications
updateWithRaw(arr, raw => {
    for (let i = 0; i < length; i++) {
        raw[i]++
    }
})
```

This method also accepts async functions as updaters, triggering only one reactive update when the entire async operation finishes:

```js
updateWithRaw(arr, async raw => {
    for (let i = 0; i < length; i++) {
        await update(arr, i)
    }
})
```

---

## Shared Reactive State

Avoid combining unrelated shared reactive states, as property updates may trigger unnecessary reactive computations for unrelated properties, for example:

```js
import { createStore } from "qingkuai"

export const store = {
    userInfo: {
        /* ... */
    },
    articleInfo{
        /* ... */
    }
}
```

The recommended approach is separating unrelated states into multiple shared reactive declarations:

```js
import { createStore } from "qingkuai"

export const userInfo = createStore({
    /* ... */
})

export const articleInfo = createStore({
    /* ... */
})
```
