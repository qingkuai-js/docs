# 属性

在 Qingkuai 中，我们可以像普通 HTML 标签那样，为组件标签添加属性来传递参数。这一行为称为组件属性传递，用于向组件传递外部数据或配置。通过组件属性，组件可以在不同场景下表现出不同的行为或样式，从而提升复用性和灵活性。

---

## 静态属性

静态属性会作为字符串传递给组件，在组件内部通过内建的 `props` 标识符访问外部传递的属性值：

|js|ts|

```qk
<!-- Outer.qk -->
<Inner attr="value" >

<!-- Inner.qk -->
<lang-js>
    console.log(props.attr) // logs: value
</lang-js>
```

```qk
<!-- Outer.qk -->
<Inner attr="value" >

<!-- Inner.qk -->
<lang-ts>
    interface Props {
        attr: string
    }
    console.log(props.attr) // logs: value
</lang-ts>
```

<div class="custom-block tip">
    如果你的嵌入脚本语言为 TypeScript，或希望获得组件属性补全建议，可以先阅读 <a href="../misc/typescript.html">TypeScript 支持</a> 再阅读本节内容。
</div>

在组件标签上添加某个属性名称但未给定属性值时，组件内部接收到的是布尔值 `true`：

|js|ts|

```qk
<!-- Outer.qk -->
<Inner attr />

<!-- Inner.qk -->
<lang-js>
    console.log(props.attr) // logs: true
</lang-js>
```

```qk
<!-- Outer.qk -->
<Inner attr />

<!-- Inner.qk -->
<lang-ts>
    interface Props {
        attr?: boolean
    }
    console.log(props.attr) // logs: true
</lang-ts>
```

---

## 动态属性

传递给组件的动态属性在组件内部同样通过 `props` 内建标识符访问，但动态属性支持传递更多的数据类型，而不只局限于字符串，并且数据源发生变更时，组件内部使用该属性的 DOM 元素会被更新：

|js|ts|

```qk
<!-- Outer.qk -->
<lang-js>
    const list = ["js", "ts", "qk"]
    setTimeout(list.pop, 1000)
</lang-js>

<Inner !list />

<!-- Inner.qk -->
<p>The length of list is: {props.list.length}</p>
```

```qk
<!-- Outer.qk -->
<lang-ts>
    const list = ["js", "ts", "qk"]
    setTimeout(list.pop, 1000)
</lang-ts>

<Inner !list />

<!-- Inner.qk -->
<lang-ts>
    interface Props {
        list: string[]
    }
</lang-ts>

<p>The length of list is: {props.list.length}</p>
```

---

## 事件

组件事件与其他非引用属性一样，在组件内部通过内建的 `props` 标识符访问：

|js|ts|

```qk
<!-- Outer.qk -->
<Inner @someThingHappened={console.log($arg)} />

<!-- Inner.qk -->
<lang-js>
    setTimeout(() => {
        props.someThingHappened("event is triggered.")
        // logs: event is triggered.
    }, 1000)
</lang-js>
```

```qk
<!-- Outer.qk -->
<Inner @someThingHappened={console.log($arg)} />

<!-- Inner.qk -->
<lang-ts>
    interface Props {
        someThingHappened: (msg: string) => void
    }
    setTimeout(() => {
        props.someThingHappened("event is triggered.")
        // logs: event is triggered.
    }, 1000)
</lang-ts>
```

<div class="custom-block tip">
    组件事件在传递和使用上与其他非引用属性没有区别，唯一的不同在于它们的语义：组件事件通常表示组件内部发生的某些行为或状态变化，而组件属性则更倾向于表示组件的配置或数据输入。因此，在设计组件接口时，我们建议将需要传递给组件的回调函数命名为事件，并使用 @ 前缀来标识它们，以便更清晰地表达它们的用途和语义。
</div>
<div class="custom-block tip">
    Qingkuai 语言服务器在提供补全建议时，只有属性值为函数类型的属性才会被提示为事件。
</div>

---

## 引用属性

引用属性是组件中的重要能力，它使组件内部可以修改外部传入的值。由于 `props` 对象中的属性本质上是只读 `getter`，因此无法直接修改。此时，需要通过引用属性提供的引用传递机制来实现该目的。在 Qingkuai 中，组件文件内建了 `refs` 标识符，用于访问外部传入的引用属性。通过修改 `refs` 中的属性值，不仅可以同步改变外部数据，还能触发其响应式更新。下面是一个简单示例：

|js|ts|

```qk
<!-- Outer.qk -->
<lang-js>
    import Inner from "./Inner.qk"

    let name = "JavaScript"
</lang-js>

<p>name is: {name}</p>
<Inner &attr={name} />

<!-- Inner.qk -->
<p>refs.attr is: {refs.attr}</p>
<button @click={refs.attr = "Qingkuai"}>Change refs.attr</button>
```

```qk
<!-- Outer.qk -->
<lang-ts>
    import Inner from "./Inner.qk"

    let name = "JavaScript"
</lang-ts>

<p>name is: {name}</p>
<Inner &attr={name} />

<!-- Inner.qk -->
<lang-ts>
    interface Refs {
        attr: string
    }
</lang-ts>

<p>refs.attr is: {refs.attr}</p>
<button @click={refs.attr = "Qingkuai"}>Change refs.attr</button>
```

<div class="custom-block warning">
    如果 <code>props</code> 中某个属性值本身是复杂类型（如对象或数组），其内部数据在技术上仍可被修改。例如，当 <code>props.userInfo</code> 是对象时，<code>props.userInfo.name</code> 依然可以被改写。但不建议这样做，因为这会让组件状态变得更难追踪和维护。
</div>

---

## 属性解构

直接解构 `props` 或 `refs` 内建对象得到的值本身不具有响应性。如下例所示，访问 `str` 不具有响应性，因为这不会触发对 `props` 属性访问的 `getter`：

```js
const { str } = props
```

因此，若需要解构组件属性并保持响应性，可以搭配使用编译器内建的 `alias` 方法：

```js
const { str } = alias(props)

// 访问 str 具有响应性，且等价于访问 props.str
```

同样地，使用 `alias` 方法解构 `refs` 内建对象得到的值也具有响应性：

```js
let { str } = alias(refs)

// 访问/写入 str 具有响应性，且等价于访问/写入 refs.str
```

---

## 指定默认值

组件属性支持默认值，当父组件未传递某个属性时，组件内部可以指定默认值来保证组件的正常运行。通过编译器内建的 `defaultProps` 或 `defaultRefs` 方法，我们可以为组件属性指定默认值：

```js
defaultRefs({
    checked: false
})

defaultProps({
    age: 0,
    name: "Unknown",
    description: "This is a default user info."
})
```

---

## 属性名称格式

Qingkuai 组件的属性名称与组件名称一样，支持 kebab 格式和驼峰格式，下面两种写法等效：

```qk
<Component myAttr />
<Component my-attr />
```

默认情况下，格式化组件文件时会将所有 kebab 格式的组件属性或事件名称整理为驼峰格式。但你可以通过在组件文件所在目录或其上级目录中添加 `.prettierrc` 文件，并输入以下内容将属性名称偏好修改为 kebab 格式：

```json
{
    "qingkuai": {
        "componentAttributeFormatPreference": "kebab"
    }
}
```

<div class="custom-block tip">
    使用此配置时，Qingkuai 语言服务器在提供组件属性补全建议时，也会优先提示 kebab 格式的属性名称。
</div>
