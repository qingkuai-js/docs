# TypeScript 支持

Qingkuai 提供了完整的 [TypeScript](https://www.typescriptlang.org/) 支持，框架本身就使用 TypeScript 编写，并在设计层面充分考虑了类型系统的兼容性。无论是组件属性、指令、生命周期函数还是全局 API，Qingkuai 都提供了完善的类型提示与推导能力，帮助开发者在编写代码时获得更好的自动补全、错误检测与开发体验。对于追求类型安全和可维护性的项目，TypeScript 与 Qingkuai 是一对理想搭档。

<div class="custom-block tip">
    Qingkuai 对 TypeScript 配置零侵入，你可以像在纯 TypeScript 项目中一样，自由配置 tsconfig.json。通过 <a href="https://www.npmjs.com/package/create-qingkuai">create-qingkuai</a> 创建的项目已经附带了必要的 TypeScript 配置项。
</div>

---

## 组件属性

在组件文件中，`Refs` 和 `Props` 这两个类型名称是保留的，声明这两个类型即可为组件的引用属性和其他属性标记类型：

```ts
interface Refs {
    // ...
}

interface Props {
    // ...
}
```

使用 `type` 关键字声明类型别名也是可以的：

```ts
type Refs = {
    // ...
}
type Props = Record<string, any>
```

也支持从外部文件导入类型：

```ts
import type Refs from "./ref-types/Component"
import type { ComponentProps as Props } from "./types"
```

在语言服务层面，`Props` 类型会被添加一层 `Readonly` 包装，因为组件 props 不支持直接修改：

```ts
interface Props {
    name: string
    list: string[]
}

// Cannot assign to 'name' because it is a read-only property.ts(2540)
props.name = "..."
```

<div class="custom-block tip">
    如果你的嵌入脚本语言为 TypeScript，但未声明这两个类型，对应内建变量的类型会是空对象类型。
</div>

如果你的嵌入脚本语言为 JavaScript，可以通过 [JSDoc](https://jsdoc.app) 为组件属性添加类型声明：

```js
/**
 * @typedef {{ name: string }} Refs
 */
```

或者：

```js
/**
 * @typedef {Object} Props
 * @property {string} name
 */
```

---

## 泛型参数

Qingkuai 组件支持泛型类型参数，直接将你需要定义的泛型参数添加到 `Props` 或 `Refs` 类型声明中即可：

```ts
type Refs<T> = {
    value: T
}

interface Props<T> {
    list: T[]
}
```

如果嵌入脚本语言为 JavaScript，也支持通过 JSDoc 声明泛型类型：

```js
/**
 * @template T
 * @typedef {Object} Props
 * @property {T[]} list
 */
```

Qingkuai 语言服务会根据组件属性推导泛型参数类型。例如在下面的示例中，`Outer.qk` 中 `Inner` 组件的 `list` 属性被推导为 `string[]`，因此 `T` 会被推导为 `string`：

```qk
<!-- Inner.qk -->
<lang-ts>
    interface Props<T> {
        list: T[]
    }
</lang-ts>

<!-- Outer.qk -->
<Inner !list={["a", "b", "c"]} />
```

当然，你也可以手动指定泛型实参以约束属性类型：

```qk
<!-- list 属性必须是数字数组 -->
<Inner<number> !list={[1, 2, 3]} />

<!-- list 属性必须是字符串数组 -->
<Inner<string> !list={["a", "b", "c"]} />
```

当某个外部属性包含泛型参数时，你不能直接导入它们作为全局类型声明，因为这些导入的类型在使用时必须为其传递泛型实参，这和普通 `TypeScript` 文件的限制是一致的。此时你需要在组件文件中重新声明全局类型，并为这个导入的类型传递泛型实参，例如：

```ts
import type { ComponentProps } from "./types"

type Props = ComponentProps<string>

// 或者
interface Props extends ComponentProps<string> {
    // ...
}
```

---

## 插槽上下文

在组件文件中你无需关心插槽类型声明，Qingkuai 语言服务会根据 `slot` 标签自动推导插槽上下文的类型：

```qk
<!-- DataList.qk -->
<lang-ts>
    const rows = [
        {
            id: 1,
            name: "Row 1"
        },
        {
            id: 2,
            name: "Row 2"
        }
    ]
</lang-ts>

<qk:spread #for={row of rows}>
    <slot !row>
        <!-- default content -->
    </slot>
</qk:spread>
```

导入上面的组件使用时，`row` 的类型会被正确推导为 `{ id: number, name: string }`：

```qk
<DataList>
    <p #slot={row from "default"}>
        {row.id}: {row.name}
    </p>
</DataList>
```

---

## 事件类型推断机制

在 Qingkuai 组件中，事件与其他非引用类型属性一样，都通过内建的 `props` 对象访问。也就是说，在组件标签上，属性名前的 `@` 和 `!` 前缀可以互换，`@` 主要用于语义标识，表示该属性是可调用的方法。当你为组件添加属性时，输入 `@` 会触发属性名自动补全，这些被提示的名称正是被推断为事件的属性。只要组件 `Props` 类型中的某个属性值是函数类型，Qingkuai 语言服务就会将其标记为事件候选项，并在你输入 `@` 时提供相应的属性名补全提示。下面代码中，`Props` 的两个属性都会被推断为事件：

```ts
interface Props {
    event1: () => void
    event2?: (s: string) => boolean
}
```
