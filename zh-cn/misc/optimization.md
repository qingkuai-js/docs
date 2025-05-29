# 性能优化

在构建现代 web 应用时，性能始终是开发者关注的重点之一。无论是首次加载速度、响应性更新的效率，还是组件的渲染粒度，合理的优化手段都能带来更流畅的用户体验。通过减少不必要的依赖追踪、按需渲染、延迟更新以及原始值操作等方式，可以有效降低开销，提升整体运行效率，让应用在保持复杂功能的同时依然快速响应。

---

## 摇树优化

使用 [create-qingkuai](https://www.npmjs.com/package/create-qingkuai) 创建的项目默认采用 [Vite](https://cn.vite.dev) 作为构建和打包工具，而 Vite 底层基于 [Rollup](https://cn.rollupjs.org)，它具备优秀的摇树优化（[Tree-shaking](https://developer.mozilla.org/zh-CN/docs/Glossary/Tree_shaking)）能力，这得益于 [import](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import) 语法的静态导入特性。

在设计之初，qingkuai 就充分考虑了 Tree-shaking 的优势 —— 所有 API 甚至指令都是可摇树优化的。比如，如果你在代码中未使用 `#for` 指令，那么与其相关的代码将不会被打包进最终产物。其他指令和功能也遵循相同的原则，从源头上避免无用代码的引入，进一步提升构建性能和输出体积。

```qk
<!-- 除#show指令外的其他指令的运行时代码不会被打包到最终产物 -->
<div #show={visible}>...</div>
```

为了获得更好的 Tree-shaking 效果，建议在导入第三方库时尽量使用 ESM（ES Module）格式的版本。相比于 CommonJS，ESM 支持静态分析，使构建工具能够准确识别和移除未使用的模块代码，从而减小打包体积。例如，在使用一些既提供 CommonJS 又提供 ESM 的库时，应优先选择其 ESM 版本，这种导入方式将显著提升最终构建产物的精简度与执行性能：

```js
// 推荐：ESM模块，Tree-shaking友好，打包体积更小
import { debounce } from "lodash-es"

// 不推荐：CommonJS模块，无法可靠地进行Tree-shaking，可能引入整个lodash
import { debounce } from "lodash"
```

<div class="custom-block tip">
    在选择使用第三方库时，不应只关注功能是否满足需求，还应关注其引入后对打包体积的影响。体积庞大的库可能会显著拉高页面首次加载时间，尤其在移动端环境中影响更为明显。你可以使用 <a href="https://bundlejs.com">bundlejs</a> 这类工具对库进行评估，它可以直观显示引入某个包或某个导出成员后的实际体积。借助这些信息，可以在功能和体积之间做出更具性价比的选择，例如使用更轻量的替代库，或者仅引入所需的部分模块。
</div>

---

## 代码分割

代码分割（Code-spliting）是前端性能优化的重要手段，用于将应用拆分成多个按需加载的模块，从而加快首屏加载速度并减少资源浪费。像 Vite 和 Rollup 这样的构建工具会根据静态依赖分析和[动态导入](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/import)自动进行模块拆分，并支持手动配置分块策略（如将第三方库分离），提升加载效率和浏览器缓存利用率：

```js
// module.js及其依赖会被拆分到一个单独的文件中，
// 另外只有loadModule方法被调用时才会加载该模块
function loadModule() {
    return import("./module.js")
}
```

在多路由应用中，不应将所有路由组件打包进主应用，而应借助代码分割机制，实现路由懒加载，显著提升加载效率和用户体验，这正是我们之前介绍的[异步组件](../components/async-components.html)的核心作用：

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

## 响应性更新

频繁修改响应性状态会导致性能浪费，因为每次修改都会触发响应性计算。此时可以使用 `updateWithRaw` 方法进行优化：该方法允许直接修改原始数据，并在所有变更完成后触发一次响应性更新：

```js
import { updateWithRaw } from "qingkuai"

const length = Math.pow(10, 5) // 十万
const arr = Array.from({ length }, (_, i) => i)

// 微秒级更新，与普通的修改数组几乎无差异
updateWithRaw(arr, raw => {
    for (let i = 0; i < length; i++) {
        raw[i]++
    }
})
```

此方法还可接受异步函数作为更新方法，此时只会在整个异步更新函数完成时触发一次响应性更新：

```js
updateWithRaw(arr, async raw => {
    for (let i = 0; i < length; i++) {
        await update(arr, i)
    }
})
```

---

## 共享响应性状态

不要将多个无关的响应性共享状态放在一起，否则属性更新可能会导致一些无关属性的响应性更新运算，例如：

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

更推荐的做法是将无关状态拆分以声明多个共享响应性状态：

```js
import { createStore } from "qingkuai"

export const userInfo = createStore({
    /* ... */
})

export const articleInfo = createStore({
    /* ... */
})
```
