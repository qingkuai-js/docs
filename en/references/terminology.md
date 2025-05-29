# Terminology Reference

To help you understand and use Qingkuai more efficiently, this section collects common terms and concepts used throughout the documentation. Whether you're a beginner just getting started with the framework or an advanced user diving into the source code, this reference will help clarify terminology and avoid ambiguity and misunderstanding.

<div class="custom-block tip">
    This section aims to explain the common usage of terms rather than strictly define them. In different contexts, some terms may carry slightly different meanings.
</div>

---

## Component File

A component file is a file with the `.qk` extension. Each component file represents a component declaration.

See: [Introduction](../getting-started/introduction.html#introduction), [Component Basics](../components/basic.html)

---

## Interpolation Attribute

Interpolation attributes refer collectively to a set of special attributes, including `directives`, `dynamic attributes`, `reference attributes`, and `events`.

See: [Compilation Directives](../basic/compilation-directives.html), [Dynamic Attributes](../basic/interpolation.html#dynamic-attributes), [Reference Attributes](../basic/reference-attributes.html), [Event Handling](../basic/event-handling.html), [Component Attributes](../components/attributes.html)

---

## Interpolation Block

An interpolation block refers to any place in the template content where JavaScript/TypeScript expressions are embedded within a pair of curly braces. This includes the value parts of all [interpolation attributes](#interpolation-attribute) and the text interpolation sections.

See: [Text Interpolation](../basic/interpolation.html#text-interpolation)

---

## Embedded Script Block

An embedded script block refers to sections enclosed by `lang-js` or `lang-ts` tags. They are used to embed script content that requires compilation.

See: [Introduction](../getting-started/introduction.html#introduction), [Design Philosophy](../getting-started/introduction.html#design-philosophy)

---

## Embedded Language Tags

Embedded language tags refer to the following 8 tags: `lang-js`, `lang-ts`, `lang-css`, `lang-scss`, `lang-sass`, `lang-less`, `lang-stylus`, `lang-postcss`. These are used to embed script and style content that needs to be compiled.

See: [Introduction](../getting-started/introduction.html#introduction), [Stylesheets](../components/stylesheets.html)

---

## Built-in Helper Methods

Built-in helper methods refer to the six identifiers — `rea`, `stc`, `der`, `wat`, `Wat`, `waT` — that can be used directly in component files without prior declaration. The first three are used to create reactive state declarations, and the latter three are used to conveniently register watchers for reactive states. All of them are compile-time markers; the Qingkuai compiler translates them into internal method calls.

See: [Reactive Declarations](../basic/reactivity.html#reactivity-declaration), [Reactivity Depth](../basic/reactivity.html#reactive-depth), [Derived Reactive State](../basic/reactivity.html#derived-reactive-state), [Destructuring Reactive Declarations](../basic/reactivity.html#destructuring-reactive-declarations), [Watchers](../basic/watchers-and-side-effects.html#watchers), [Pre Watchers](../basic/watchers-and-side-effects.html#pre-watchers), [Sync Watchers](../basic/watchers-and-side-effects.html#synchronous-watchers)

<div class="custom-block warning">The identifiers of built-in helper methods cannot be redeclared in the top-level scope of an embedded script block.</div>

---

## Built-in Objects

Built-in objects refer to the two identifiers — `refs` and `props` — that are accessible without prior declaration in component files. They are used respectively to store and access reference attributes and other property values passed externally to the component.

See: [Component Attributes](../components/attributes.html)

<div class="custom-block warning">The identifiers of built-in objects cannot be redeclared in the top-level scope of an embedded script block.</div>
