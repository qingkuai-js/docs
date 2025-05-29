# API Reference

Qingkuai's API is organized into multiple entry packages to allow developers to import on demand and improve bundling efficiency: the runtime main package (`qingkuai`), the internal package (`qingkuai/internal`), and the compiler package (`qingkuai/compiler`).

<div class="custom-block tip">
    This section does not cover the usage details of the internal package (`qingkuai/internal`) as it is primarily intended for internal framework use, and developers typically do not need to import it manually. This package contains low-level rendering logic and implementations of various directives, mainly used by the compiler when generating code. To avoid misuse, the internal package does not provide type declarations, clearly indicating its internal module status. To understand its usage in depth, you usually need to read the <a href="https://github.com/qingkuai-js/qingkuai">source code</a>.
</div>

---

## Runtime

This package provides core methods required at runtime (especially during development) and is the most commonly used entry point when writing modules. It focuses on improving the development experience and does not include build or compilation-related logic, making fast development and debugging easier.

### mountApp

A method used to mount an application. It accepts a component and a [selector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector#selectors) string as the mounting target:

```js
import App from "App.qk"
import { createApp } from "qingkuai"

mountApp(App, "#app")
```

### nextTick

A method used to execute a callback after the current call stack is cleared and before the next event loop tick. It is an alias for `Promise.resolve().then`.

```js
import { nextTick } from "qingkuai"

nextTick(() => {
    console.log("micro task")
})
```

Its return value is a Promise, so you can also use the `await` keyword to wait for its resolution:

```js
import { nextTick } from "qingkuai"

async function fn() {
    await nextTick()
}
```

### raw

A method used to retrieve the raw value of a reactive state. See: [Getting Raw Values](../basic/reactivity.html#getting-raw-values).

### watch

A method used to register a post watcher for a reactive state. See: [Watchers](../basic/watchers-and-side-effects.html#watchers).

### preWatch

A method used to register a pre watcher for a reactive state. See: [Pre Watchers](../basic/watchers-and-side-effects.html#pre-watcherss).

### syncWatch

A method used to register a synchronous watcher for a reactive state. See: [Synchronous Watchers](../basic/watchers-and-side-effects.html#synchronous-watchers).

### effect

A method used to register a post side effect for a reactive state. See: [Side Effects](../basic/watchers-and-side-effects.html#side-effects).

### preEffect

A method used to register a pre side effect for a reactive state. See: [Side Effects](../basic/watchers-and-side-effects.html#side-effects).

### syncEffect

A method used to register a synchronous side effect for a reactive state. See: [Side Effects](../basic/watchers-and-side-effects.html#side-effects).

### onBeforeMount

A method that triggers the provided callback before the component is mounted. See: [Lifecycle](../components/lifecycle.html).

### onAfterMount

A method that triggers the provided callback after the component has been mounted. See: [Lifecycle](../components/lifecycle.html).

### onBeforeUpdate

A method that triggers the provided callback before a reactive update is scheduled. See: [Lifecycle](../components/lifecycle.html).

### onAfterUpdate

A method that triggers the provided callback after a reactive update has been completed. See: [Lifecycle](../components/lifecycle.html).

### onBeforeDestroy

A method that triggers the provided callback before the component is destroyed. See: [Lifecycle](../components/lifecycle.html).

### onAfterDestroy

A method that triggers the provided callback after the component has been destroyed. See: [Lifecycle](../components/lifecycle.html).

### createStore

A method used to register a shared reactive state store. See: [Reactive State Store](../basic/reactivity.html#reactive-state-store).

### updateWithRaw

A method used to update a reactive state using its raw value. This method is often used when optimizing [reactive updates](../misc/optimization.html#reactive-updates) performance.

---

## Compiler

This package is used to transpile component files into minimal and high-performance JavaScript code. It is typically invoked by bundler plugins or language servers during the build process. This package is suitable for building custom bundling workflows, developing IDE plugins, or integrating into toolchains. It provides AST parsing, code generation, and utility methods for component files. It does not participate in actual execution within the browser:

### isCompileError

A method used to determine whether an error is a compilation error.

### isCompileWarning

A method used to determine whether a warning is a compilation warning.

### commonMessage

An object used to record common compilation error messages for tools like language servers. Developers generally do not need to use this directly.

### parseTemplate

A method used to parse component files. Its type signature is as follows:

<!-- prettier-ignore -->
```ts
interface TemplateAttribute {
    loc: ASTLocation;
    key: AttributeKeyValue;
    value: AttributeKeyValue;
    quote: AttributeQuoteKinds;
}
interface TemplateNode {
    tag: string;
    range: NumNum;
    pure: boolean;
    content: string;
    loc: ASTLocation;
    pref: boolean;
    isEmbedded: boolean;
    isSelfClosing: boolean;
    componentTag: string;
    children: TemplateNode[];
    startTagEndPos: ASTPosition;
    endTagStartPos: ASTPosition;
    parent: TemplateNode | null;
    prev: TemplateNode | undefined;
    next: TemplateNode | undefined;
    attributes: TemplateAttribute[];
}
export function parseTemplate(source: string, recover?: boolean): TemplateNode[]
```

<div class="custom-block tip">
    The second parameter <code>recover</code> indicates whether to attempt to recover from errors during parsing.
</div>

### compile

A method used to compile component files. This is its type signature:

<!-- prettier-ignore -->
```ts
type CompileOptions = Partial<{
    componentName: string
    hashId: string
    check: boolean
    debug: boolean
    comment: boolean
    sourcemap: boolean
    typeRefStatement: string
    reserveTemplateComment: boolean
    convenientDerivedDeclaration: boolean
}>

// The CompileResult type is too complex to show here;
// please refer to the package files for the specific type definition.
export function compile(source: string, options: CompileOptions): CompileResult
```

<div class="custom-block tip">
    <code>check</code> in CompileOptions indicates whether to enable check mode. When set to true, compilation will not be interrupted on errors, and error messages will be placed into the <code>messages</code> property of the compilation result; <code>typeRefStatement</code> is a parameter needed when generating intermediate code format for the language server, and should not be passed in normal compilation.
</div>

### util

An object containing utility methods shared between packages. Its type definition is as follows:

```ts
export const util: {
    isSelfClosingTag: (tag: string) => boolean;
    isBannedIdentifier: (name: string) => boolean;
    isEmbededLanguageTag: (name: string) => boolean;
    mustDirectiveHasValue: (name: string) => boolean;
    getContextIdentifiers: (node: TemplateNode) => string[];
    camel2Kebab: (str: string, allowFullLower?: boolean) => string;;
    kebab2Camel: (str: string, startWithUppercase?: boolean) => string;
    findEndBracket: (str: string, startIndex: number, char?: StartBracket) => number;
    findOutOfString: {
        (str: string, pattern: string | RegExp): number;
        (str: string, pattern: string | RegExp, startIndex?: number): NumNum;
    };
    findOutOfComment: {
        (str: string, pattern: string | RegExp): number;
        (str: string, pattern: string | RegExp, startIndex?: number): NumNum;
    };
    findOutOfStringComment: {
        (str: string, pattern: string | RegExp): number;
        (str: string, pattern: string | RegExp, startIndex?: number): NumNum;
    };
};
```

<div class="custom-block tip">
    The <code>findOutOf</code> series of methods are used to skip specified ranges while searching within a JavaScript source string; the purpose of other methods can be understood intuitively from their names.
</div>

### PositionFlag

An object used to mark special index positions in the source code. Its type declaration is as follows:

```ts
export const PositionFlag: {
    inScript: number
    inStyle: number
    isAttributeStart: number
    isComponentStart: number
    inNormalTagInlineEvent: number
    isInterpolationAttributeStart: number
}
```
