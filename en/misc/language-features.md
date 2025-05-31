# Language Features

Qingkuai does not have complex syntax extensions built into the compiler. Instead, it provides rich language feature support through a Language Server based on the [LSP](https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/) (Language Server Protocol). These features include type inference, intelligent completion, error hints, quick navigation, semantic highlighting, etc., covering framework features such as component properties, slots, scoped styles, directive systems, and reference passing. With LSP, qingkuai significantly enhances development experience and type safety while maintaining simple syntax.

---

## IDE Extensions

Currently, we have only released an extension for [vscode](https://code.visualstudio.com). You can search for `QingKuai` in the marketplace to install it.

<img src="/static/medias/extension.png" />

<div class="custom-block tip">
    When you encounter bugs in your IDE, you should submit an issue in the <a href="https://github.com/qingkuai-js/language-features">language-features</a> repository of qingkuai.
</div>

---

## Emmet

The qingkuai language server provides good [Emmet](https://emmet.io) support. However, due to conflicts between dynamic attributes and the Emmet syntax for removing attributes, the `-` character is used instead to remove attributes in component files. The following example code demonstrates how to create an input tag without the type attribute using Emmet syntax in component files:

```txt
input[-type]
```

The original syntax:

```txt
input[!type]
```

would create a dynamic attribute:

```qk
<input !type={}>
```

---

## Formatting

The document formatting function is built into the qingkuai language server, implemented by [prettier-plugin-qingkuai](https://www.npmjs.com/package/prettier-plugin-qingkuai). If there are syntax errors in the component file, formatting may fail. In this case, you can view the failure information in the IDE's `output` window:

<img src="/static/medias/format-error.png" />

---

## Code Navigation

Code navigation is a frequently used feature during development. This feature has some additional usages in component files:

-   Find slot definitions: Hold down the meta key and click the left mouse button on the slot attribute of the first-level child element of the component;
-   Find component definitions: Hold down the meta key and click the left mouse button on the component tag or the component identifier in embedded scripts;
-   Find slot references: Right-click on the slot tag and select “Go to References”, or enable the “Code Lens” feature;
-   Find component references: Right-click on the embedded language tag and select “Go to references”, or enable the “Code Lens” feature;
