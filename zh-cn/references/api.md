# API 参考

qingkuai 的 API 被有序地划分为多个入口包，以便于开发者按需引入、提升打包效率：运行时主包（qingkuai）、内部包（qingkuai/internal）以及编译器包（qingkuai/compiler）。

<div class="custom-block tip">
    本节不介绍内部包（qingkuai/internal）的使用细节，因为它主要面向框架内部，开发者通常无需手动导入。该包包含底层的渲染逻辑以及各类指令的实现，主要供编译器在生成代码时调用。为了避免误用，内部包不提供类型声明，以此明确其内部模块的定位。若要深入了解其用途，通常需要结合<a href="https://github.com/qingkuai-js/qingkuai">源码</a>进行阅读。
</div>

---

## 运行时

此包提供框架在运行时（尤其是开发过程中）所需的核心方法，是开发者编写模块时最常用的入口。它专注于提升开发体验，不包含构建或编译相关逻辑，便于快速开发和调试。

### mountApp

一个方法，用于挂载应用，它接受一个组件和挂载目标的 [selector](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelector#selectors) 字符串作为参数：

```js
import App from "App.qk"
import { createApp } from "qingkuai"

mountApp(App, "#app")
```

### nextTick

一个方法，用于在当前调用栈清空后、下一次事件循环开始前执行回调函数，它是 Promise.resolve().then 的别名。

```js
import { nextTick } from "qingkuai"

nextTick(() => {
    console.log("micro task")
})
```

它的返回值是一个 Promise，所以你还可以用 await 关键字等待它的返回值：

```js
import { nextTick } from "qingkuai"

async function fn() {
    await nextTick()
}
```

### raw

一个方法，用于获取响应性状态的原始值，参考：[获取原始值](../basic/reactivity.html#获取原始值)。

### watch

一个方法，用于为响应性状态注册后置监视器，参考：[监视器](../basic/watchers-and-side-effects.html#监视器)。

### preWatch

一个方法，用于为响应性状态注册前置监视器，参考：[前置监视器](../basic/watchers-and-side-effects.html#前置监视器)

### syncWatch

一个方法，用于为响应性状态注册同步监视器，参考：[同步监视器](../basic/watchers-and-side-effects.html#同步监视器)

### effect

一个方法，用于注册响应性状态的后置副作用，参考：[副作用](../basic/watchers-and-side-effects.html#副作用)

### preEffect

一个方法，用于注册响应性状态的前置副作用，参考：[副作用](../basic/watchers-and-side-effects.html#副作用)

### syncEffect

一个方法，用于注册响应性状态的同步副作用，参考：[副作用](../basic/watchers-and-side-effects.html#副作用)

### onBeforeMount

一个方法，挂载组件前触发传入的回调函数，参考：[生命周期](../components/lifecycle.html)。

### onAfterMount

一个方法，组件挂载完成后触发传入的回调函数，参考：[生命周期](../components/lifecycle.html)。

### onBeforeUpdate

一个方法，响应性更新调度前触发传入的回调函数，参考：[生命周期](../components/lifecycle.html)。

### onAfterUpdate

一个方法，响应性更新调度完成后触发传入的回调函数，参考：[生命周期](../components/lifecycle.html)。

### onBeforeDestroy

一个方法，卸载组件前触发传入的回调函数，参考：[生命周期](../components/lifecycle.html)。

### onBeforeDestroy

一个方法，组件卸载完成后触发传入的回调函数，参考：[生命周期](../components/lifecycle.html)。

### createStore

一个方法，用于注册共享响应性状态存储，参考：[响应性状态存储](../basic/reactivity.html#响应性状态存储)。

### updateWithRaw

一个方法，用于使用原始值更新响应性状态，通常在优化[响应性更新](../misc/optimization.html#响应性更新)性能时需要用到此方法。

---

## 编译器

此包用于将组件文件转译为极简且高性能 JavaScript 代码，它通常在构建阶段由打包工具插件或语言服务器调用。该包适用于构建自定义打包流程、开发 IDE 插件或工具链集成，提供了组件文件 AST 解析、代码生成以及一些工具类方法，它不会参与浏览器中的实际执行：

### isCompileError

一个方法，用于判断是否是编译错误。

### isCompileWarning

一个方法，用于判断是否是编译警告。

### commonMessage

一个对象，用于记录常见编译错误信息，供语言服务器等相关工具使用，开发者通常无需直接使用。

### parseTemplate

一个方法，用于解析组件文件，其类型签名如下：

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
    第二个参数 <code>recover</code> 表示遇到错误时是否恢复解析。
</div>

### compile

一个方法，用于编译组件文件，这是它的类型签名：

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

// CompileResult类型过于复杂，这里不做展示，可在包文件中查看具体类型
export function compile(source: string, options: CompileOptions): CompileResult
```

<div class="custom-block tip">
    CompileOptions中的 <code>check</code> 表示是否启用检查模式，设置为true时遇到错误不会中断编译流程，错误信息会被放至到编译结果的 messages 属性中；<code>typeRefStatement</code> 是在生成语言服务器所需的中间代码格式是需要传递的参数，普通编译不要传入。
</div>

### util

一个对象，关联包间的通用方法，其类型定义如下：

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
    <code>findOutOf</code> 系列方法的作用是从 JavaScript 源码字符串中跳过指定范围查找内容；其余方法的功能可从名称直观理解。
</div>

### PositionFlag

一个对象，用来标记源码中特殊索引位置的标志，其类型声明如下：

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
