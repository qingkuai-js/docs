---
description: "Qingkuai optimization: directive-level tree shaking, shared-style reuse rules to avoid duplicated scoped CSS, and code splitting with async components."
keywords: ["optimization", "tree shaking", "code splitting", "style reuse", "bundle size", "优化"]
---

# Optimization

Lower runtime and payload overhead through tree shaking, style reuse discipline, and code splitting.

## Rules

1. **Tree shaking**: every API and directive is tree-shakable — unused directive runtimes (e.g. `#for` when only `#if` is used) never enter the bundle. Prefer ESM versions of third-party libraries for reliable static analysis and shaking (e.g. `lodash-es` over `lodash`); oversized libraries noticeably increase first-load time (especially on mobile), so evaluate bundle impact with tools like bundlejs.
2. **Style reuse**: importing the same shared stylesheet into scoped style blocks of multiple components (`src` or `@import`) compiles to one scoped copy per component with different scope markers — duplicates grow linearly with component count, driving up CSS size and browser style-matching overhead. Instead:
   - Load stable shared styles in a unified way from a global style entry (e.g. app entry CSS or the global styles of layout components).
   - Keep styles that do need to be declared within a component but rely on no scope isolation in a `global` style block or a global file.
   - Keep only rules strongly coupled to the component's structure and dependent on scope isolation in scoped component styles.
3. **Code splitting**: Vite/Rollup split modules automatically based on static dependency analysis and dynamic imports, and also support manual chunking strategies (e.g. separating third-party libraries); use dynamic `import()` for on-demand modules and lazy-load route components with async components instead of bundling all routes into the main application.

## Examples

Only `#if` runtime code is bundled here:

```qk
<!-- Except for the #if directive, the runtime code of other directives will not be bundled into the final output -->
<lang-js>
    let visible = true
</lang-js>

<div #if={visible}>...</div>
```

Lazy-loading a route component:

```qk
<qk:spread
    #await={import("./Component.qk")}
    #then={{ default: Component }}
>
    <Component />
</qk:spread>
```

## See also

- [Async Components](../components/async-components.md)
- [Component Stylesheets](../components/stylesheets.md)
