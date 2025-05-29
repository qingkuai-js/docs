# TypeScript Support

Qingkuai warmly embraces [TypeScript](https://www.typescriptlang.org/). The framework itself is written in TypeScript and fully considers type system compatibility at the design level. Whether it's component attributes, directives, lifecycle functions, or global APIs, Qingkuai provides comprehensive type hints and inference capabilities to help developers achieve better auto-completion, error detection, and development experience. For projects pursuing type safety and maintainability, TypeScript and Qingkuai make an ideal pair.

<div class="custom-block tip">
    Qingkuai has zero intrusion on TypeScript configurations, allowing you to freely configure tsconfig.json as you would in a pure TypeScript project. Projects created with <a href="https://www.npmjs.com/package/create-qingkuai">create-qingkuai</a> already come with the necessary TypeScript configuration items.
</div>

---

## Component Attribute Types

In component files, the type names `Refs` and `Props` are reserved. Declaring these two types will mark the types for the component's reference attributes and other attributes:

```ts
interface Refs {
    /* ... */
}

interface Props<T> {
    /* ... */
}
```

Using the `type` keyword to declare type aliases is also possible:

```ts
type Refs<T> = {
    /* ... */
}
type Props = Record<string, any>
```

It also supports importing types from external files:

```ts
import type Refs from "./ref-types/Component"
import type { ComponentProps as Props } from "./types"
```

On the language feature level, the `Props` type will be wrapped with an additional `Readonly`, because component attributes do not support direct modification. However, if an attribute value is a reference type, you can modify its property values:

```ts
interface Props {
    name: string
    list: string[]
}

// Cannot assign to 'name' because it is a read-only property.ts(2540)
props.name = "..."

// ok
props.list[0] = "..."
```

<div class="custom-block tip">
    If your embedded script language is TypeScript but you haven't declared these two types, the corresponding built-in variables' types will be an empty object type.
</div>

If your embedded script language is JavaScript, you can add type declarations for component attributes through [jsDoc](https://jsdoc.app):

```js
/**
 * @typedef {{ name: string }} Refs
 */

/**
 * @typedef {Object} Props
 * @property {string} name
 */
```

---

## Event Type Inference Mechanism

In Qingkuai components, events, like other non-reference type attributes, are accessed through the built-in `props` object. This means that within component tags, the prefixes `@` and `!` before attribute names are interchangeable; `@` mainly serves as a semantic identifier indicating that the attribute is a callable method. When adding attributes to a component, typing `@` triggers auto-completion of some attribute names, which are inferred as event attributes. As long as an attribute in the component's `Props` type is of function type, Qingkuai's language server marks it as an event candidate and provides corresponding attribute name completion suggestions when you input `@`. Both attributes of Props in the following code will be inferred as events:

```ts
interface Props {
    event1: () => void
    event2?: (s: string) => boolean
}
```
