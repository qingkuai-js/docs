# Basics

In qingkuai, components are the basic units for building user interfaces. Each component represents an independent, reusable piece of the UI, which can be a simple button or a complex page. Components are inherently encapsulated and composable, making UI development clearer and more efficient.

The image below shows a component-based representation of a blog page, where each area represents a component. Areas with the same color indicate component reuse:

<img width="70%" src="https://qingkuai-js.oss-cn-beijing.aliyuncs.com/docs/components.png" style="margin-left: 15%;" />

---

## Definition and Usage

Component definition in qingkuai follows the same pattern as [vue](https://vuejs.org) and [svelte](https://svelte.dev), namely file-based component definition. Each component is defined in a file with the `.qk` extension. After being processed by the compiler, this file is converted into a [JavaScript module](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) with a [default export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#%E9%BB%98%E8%AE%A4%E5%AF%BC%E5%87%BA%E4%B8%8E%E5%85%B7%E5%90%8D%E5%AF%BC%E5%87%BA).

Assuming we have defined a component via a `Component.qk` file, we can import it into another component file using [import syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), and reuse it in the template section by adding a tag with the same name as the import identifier:

```qk
<lang-js>
    import Component from "./path/to/Component.qk"
</lang-js>

<Component />
<Component />
```

<div class="custom-block tip">From here on, we will use the term <code>component file</code> to refer to files with the <code>.qk</code> extension.</div>

Component names support kebab-case format. The following usages are equivalent:

```qk
<MyComponent />
<my-component />
```

By default, when formatting component files, all component names will be transformed into camelCase format. However, you can add a `.prettierrc` file in the component file's directory or its parent directories and include the following content to change the component name preference to kebab-case:

```json
{
    "qingkuai": {
        "componentTagFormatPreference": "kebab"
    }
}
```

<div class="custom-block tip">When using this configuration, the qingkuai language server will also prioritize suggesting kebab-case component tags during auto-completion.</div>
