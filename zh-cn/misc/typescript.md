# Typescript 支持

qingkuai 积极拥抱 [TypeScript](https://www.typescriptlang.org/)，框架本身就是使用 TypeScript 编写的，并在设计层面充分考虑了类型系统的兼容性。无论是组件属性、指令、生命周期函数还是全局 API，qingkuai 都提供了完善的类型提示与推导能力，帮助开发者在编写代码时获得更好的自动补全、错误检测与开发体验。对于追求类型安全和可维护性的项目，TypeScript 与 qingkuai 是一对理想搭档。

<div class="custom-block tip">
    qingkuai 对 TypeScript 配置零侵入，你可以像在纯 TypeScript 项目中一样，自由配置 tsconfig.json。通过 <a href="https://www.npmjs.com/package/create-qingkuai">create-qingkuai</a> 创建的项目已经附带了必要的 typescript 配置项。
</div>

---

## 组件属性类型

在组件文件中，`Refs` 和 `Props` 这两个类型名称是保留的，声明这两个类型即可为组件的引用属性和其他属性标记类型：

```ts
interface Refs {
    /* ... */
}

interface Props<T> {
    /* ... */
}
```

使用 `type` 关键字声明类型别名也是可以的：

```ts
type Refs<T> = {
    /* ... */
}
type Props = Record<string, any>
```

也支持从外部文件导入类型：

```ts
import type Refs from "./ref-types/Component"
import type { ComponentProps as Props } from "./types"
```

在语言功能层面，`Props` 类型会被添加一层 `Readonly` 包装，因为组件 props 是不支持直接修改的。但如果某个 props 属性值是引用类型，你可以修改它的属性值：

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
    如果你的嵌入脚本语言为 Typescript，但未声明这两个类型时，对应的内建变量的类型为空对象类型。
</div>

如果你的嵌入脚本语言为 Javascript，可以通过 [jsDoc](https://jsdoc.app) 为组件属性添加类型声明：

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

## 事件类型推断机制

在 qingkuai 的组件中，事件与其他非引用类型的属性一样，都是通过内建的 `props` 对象进行访问的。也就是说，在组件标签上，属性名前的 `@` 和 `!` 前缀是可以互换的，`@` 主要起到语义标识的作用，用来表示该属性是一个可调用的方法。当你为组件添加属性时，输入 `@` 会触发一些属性名的自动补全，这些被提示的名称正是被推断为事件的属性。只要组件的` Props` 类型中某个属性的值是函数类型，qingkuai 的语言服务器就会将其标记为事件候选项，并在你输入 `@` 时提供相应的属性名补全提示。下面代码中 Props 的两个属性都会被推断为事件：

```ts
interface Props {
    event1: () => void
    event2?: (s: string) => boolean
}
```
