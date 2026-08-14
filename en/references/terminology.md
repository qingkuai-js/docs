# Terminology Reference

To help you understand and use Qingkuai more efficiently, this section collects common terms used throughout the documentation and gives a short explanation for each one. Whether you are just getting started with the framework or already reading the source code, this reference can help you clarify terminology and reduce misunderstandings.

<div class="custom-block tip">
    This section is mainly used to keep terminology and phrasing consistent. Explanations follow the most common meanings used across the documentation.
</div>

---

## Component File

A component file is a file with the `.qk` extension. Each component file represents a component declaration.

See: [Introduction](../getting-started/introduction.md), [Component Basics](../components/basic.md)

---

## Component Instance

A component instance is the runtime object created after a component file is compiled. It carries the members exported by the component and its internal state. In a parent component, you can obtain the instance of a child component through the `&handle` reference attribute on the component tag and use it to access the exported members; it is also the binding argument of the watcher and side effect methods imported from the `qingkuai` runtime package.

See: [Component Exports](../components/exports.md), [Component Reference Attributes](../components/attributes.md#reference-attributes)

---

## Event

An event is an event attribute declared with the `@` prefix. It is used to bind interaction logic in templates or expose callable callbacks to the outside of a component.

See: [Event Handling](../basic/event-handling.md), [Events](../components/attributes.md#events)

---

## Static Attribute

A static attribute is an attribute whose value does not depend on an interpolation expression when declared in a template. It is usually used to bind plain string data.

See: [Interpolation](../basic/interpolation.md)

---

## Dynamic Attribute

A dynamic attribute is an attribute declared with the `!` prefix whose value is computed from an interpolation expression. It is suitable for binding non-string data such as booleans and objects.

See: [Dynamic Attributes](../basic/interpolation.md#dynamic-attributes)

---

## Reference Attribute

A reference attribute is a writable attribute channel declared with the `&` prefix. It can be used not only on component tags, but also on specific native HTML tags such as `input`, `textarea`, and `select` to establish value synchronization or reference passing. Inside a component, this kind of data is usually accessed and updated through `refs`.

See: [Reference Attributes](../basic/reference-attributes.md), [Form Handling](../basic/forms.md), [Reference Attributes](../components/attributes.md#reference-attributes)

---

## Reactive, Reactivity, and Reactive Values

These three terms are related, but they emphasize different things in the documentation:

- Reactive: a capability or mechanism that allows value changes to be observed and dependency updates to be triggered.
- Reactivity: an abstract description of that capability itself, often used when discussing system behavior or design characteristics.
- Reactive value: a concrete unit of data that has reactive capability, such as a value inferred by the compiler or created through a related API.

See: [Reactivity](../basic/reactivity.md)

---

## Watcher

A watcher is a mechanism that listens for changes in reactive values and executes a callback. It is commonly used for side-effect control, state comparison, and cleanup logic.

See: [Watchers](../basic/watchers-and-side-effects.md#watchers)

---

## Side Effect

A side effect is logic that depends on reactive state and runs after that state changes. Typical examples include DOM interaction, asynchronous requests, and synchronization with external systems.

See: [Side Effects](../basic/watchers-and-side-effects.md#side-effects)

---

## Scope

Scope describes the range in a template or script where identifiers can be accessed. It especially affects variable visibility in slot and directive contexts.

See: [Scope](../components/slots.md#scope)

---

## qk:spread

`qk:spread` is a built-in element in Qingkuai. It is commonly used as a virtual mounting point for directives and is not rendered as a real DOM element.

See: [Built-in Elements](../misc/builtin-elements.md)

---

## props

`props` is a compiler intrinsic used to read normal attributes and event attributes passed into a component.

See: [Attributes](../components/attributes.md), [Compiler Intrinsics](./intrinsics.md)

---

## refs

`refs` is a compiler intrinsic used to access reference attributes inside a component and perform writable updates.

See: [Reference Attributes](../components/attributes.md#reference-attributes), [Compiler Intrinsics](./intrinsics.md)

---

## Interpolation Attribute

Interpolation attributes are a collective term for a group of special attributes, including `directives`, `dynamic attributes`, `reference attributes`, and `events`.

See: [Compilation Directives](../basic/compilation-directives.md), [Dynamic Attributes](../basic/interpolation.md#dynamic-attributes), [Reference Attributes](../basic/reference-attributes.md), [Event Handling](../basic/event-handling.md), [Attributes](../components/attributes.md)

---

## Interpolation Block

An interpolation block is any place in a template where a JavaScript or TypeScript expression is embedded inside a pair of curly braces. It includes both the value part of [interpolation attributes](#interpolation-attribute) and [text interpolation](../basic/interpolation.md#text-interpolation).

---

## Embedded Script Block

An embedded script block is a region wrapped by `lang-js` or `lang-ts` tags, used for writing script content that will be processed by the compiler.

See: [Introduction](../getting-started/introduction.md), [Design Philosophy](../getting-started/introduction.md#design-philosophy)

---

## Embedded Style Block

An embedded style block is a region wrapped by `lang-css`, `lang-scss`, `lang-sass`, `lang-less`, `lang-stylus`, or `lang-postcss` tags inside a component file, used for writing style content that will be processed by the compiler. These tags support a static `src` attribute for external style files and a boolean `global` attribute for global style blocks.

See: [Introduction](../getting-started/introduction.md), [Stylesheets](../components/stylesheets.md)

---

## Embedded Language Tags

Embedded language tags refer to the eight tags `lang-js`, `lang-ts`, `lang-css`, `lang-scss`, `lang-sass`, `lang-less`, `lang-stylus`, and `lang-postcss`, which are used to embed script and style content that needs compilation. Style tags support static attributes such as `src` and `global`.

See: [Introduction](../getting-started/introduction.md), [Stylesheets](../components/stylesheets.md)

---

## Slot Outlet

A slot outlet is the placeholder location declared with the `slot` tag inside a component. It is used to receive slot content passed in from outside.

See: [Slots](../components/slots.md)

---

## Slot Content

Slot content is the child content passed in by the component consumer. It is rendered at the corresponding [slot outlet](#slot-outlet).

See: [Slots](../components/slots.md)

---

## Compiler Intrinsics

Compiler intrinsics are reserved identifiers that do not need to be declared in component files and can be recognized and handled directly by the compiler. They mainly include object-like intrinsics and method-like intrinsics.

Object-like intrinsics include `refs`, `props`, and `slots`.

Method-like intrinsics are the [built-in methods](#built-in-methods).

Among them, `refs` is used to access reference attributes, `props` is used to access normal attributes and event attributes, and `slots` is used to check whether slot content has been passed in.

See: [Attributes](../components/attributes.md), [Slots](../components/slots.md), [Built-in Methods](#built-in-methods), [Compiler Intrinsics](./intrinsics.md)

---

## Built-in Methods

Built-in methods are part of the compiler intrinsics. They refer to the method identifiers that can be used directly in component files, including the reactivity-marking methods `raw`, `reactive`, `shallow`, `alias`, `derived`, and `derivedExp`, the default-value declaration method `defaults`, the watcher convenience registration methods `watchExp`, `preWatchExp`, `postWatchExp`, and `syncWatchExp`, and the watcher and side effect methods `watch`, `preWatch`, `postWatch`, `syncWatch`, `effect`, `preEffect`, `postEffect`, and `syncEffect`. They are essentially compile-time markers that are transformed into internal method calls during compilation.

See: [Reactivity Declaration](../basic/reactivity.md#reactivity-declaration), [Watchers](../basic/watchers-and-side-effects.md#watchers), [Compiler Intrinsics](./intrinsics.md)
