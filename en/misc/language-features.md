# Language Features

Qingkuai does not rely on complex syntax extensions in the compiler. Instead, it provides rich editor support through a Language Server based on the [LSP](https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/) (Language Server Protocol). These features include type inference, intelligent completion, diagnostics, code navigation, and semantic highlighting, covering framework capabilities such as component attributes, slots, scoped styles, directives, and reference attributes. With LSP, Qingkuai keeps syntax simple while significantly improving development experience and type safety.

---

## IDE Extensions

Currently, the extension is available for [VS Code](https://code.visualstudio.com). You can search for `Qingkuai` in the marketplace to install it.

<img src="/static/medias/extension.png" />

<div class="custom-block tip">
    If you encounter IDE-related issues, please submit an issue in Qingkuai's <a href="https://github.com/qingkuai-js/language-features">language-features</a> repository.
</div>

---

## Emmet

The Qingkuai language server provides good [Emmet](https://emmet.io) support. However, dynamic attributes conflict with Emmet's default attribute-removal syntax, so component files use `-` to remove attributes. The following example creates an `input` tag without the `type` attribute:

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

Document formatting is built into the Qingkuai language server and implemented by [prettier-plugin-qingkuai](https://www.npmjs.com/package/prettier-plugin-qingkuai). If a component file contains syntax errors, formatting may fail. In that case, check the IDE `output` panel:

<img src="/static/medias/format-error.png" />

---

## Code Navigation

Code navigation is a frequently used feature during development. This feature has some additional usages in component files:

-   Find slot definitions: Hold down the meta key and click the left mouse button on the slot attribute of the first-level child element of the component;
-   Find component definitions: Hold down the meta key and click the left mouse button on the component tag or the component identifier in embedded scripts;
-   Find slot references: Right-click on the slot tag and select “Go to References”, or enable the “Code Lens” feature;
-   Find component references: Right-click on the embedded language tag and select “Go to references”, or enable the “Code Lens” feature;
