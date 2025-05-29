# 属性

在 qingkuai 中，我们还可以像普通 HTML 标签那样为组件标签添加属性来传递参数，这一行为称为组件的属性传递，用于向组件传递外部数据或配置。通过组件属性，我们可以让组件在不同的场景下表现出不同的行为或样式，从而提升组件的复用性和灵活性。

---

## 普通属性

普通属性会作为字符串传递给组件，在组件内部通过内建的 `props` 对象访问外部传递的属性值：

|js|ts|

```qk
<!-- Outter.qk -->
<Inner attr="value" >

<!-- Inner.qk -->
<lang-js>
    console.log(props.attr) // value
</lang-js>
```

```qk
<!-- Outter.qk -->
<Inner attr="value" >

<!-- Inner.qk -->
<lang-js>
    interface Props {
        attr: string
    }
    console.log(props.attr) // value
</lang-js>
```

<div class="custom-block tip">
    如果你的嵌入脚本语言为 Typescript 或者你想获得组件属性补全建议，可以先阅读完 <a href="../misc/typescript.html">Typescript 支持</a> 再阅读本节内容。
</div>

在组件标签上添加某个属性名称但未给定属性值时，组件内部接收到的是布尔值 `true`：

|js|ts|

```qk
<!-- Outter.qk -->
<Inner attr />

<!-- Inner.qk -->
<lang-js>
    console.log(props.attr) // true
</lang-js>
```

```qk
<!-- Outter.qk -->
<Inner attr />

<!-- Inner.qk -->
<lang-js>
    interface Props{
        attr?: boolean
    }
    console.log(props.attr) // true
</lang-js>
```

<div class="custom-block tip">
    在 typescript 类型系统中，这种没有值的属性也会被认作传递的布尔值。
</div>

---

## 动态属性

传递给组件的动态属性在组件内部同样通过 `props` 内建对象访问，但动态属性支持传递更多的数据类型，而不只局限于字符串，并且数据源发生变更时，组件内部使用该属性的 DOM 元素会被更新：

|js|ts|

```qk
<!-- Outter.qk -->
<lang-js>
    const list = ["js", "ts", "qk"]
    setTimeout(list.pop, 1000)
</lang-js>

<Inner !list />

<!-- Inner.qk -->
<p>The length of list is: {props.list.length}</p>
```

```qk
<!-- Outter.qk -->
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

组件事件与其他非引用属性一样，在组件内部通过内建的 `props` 对象访问：

|js|ts|

```qk
<!-- Outter.qk -->
<Inner @someThingHappened={console.log($arg)} />

<!-- Inner.qk -->
<lang-js>
    setTimeout(() => props.someThingHappened("event is triggered."), 1000)
</lang-js>
```

```qk
<!-- Outter.qk -->
<Inner @someThingHappened={console.log($arg)} />

<!-- Inner.qk -->
<lang-ts>
    interface Props {
        someThingHappened: (msg: string) => void
    }
    setTimeout(() => props.someThingHappened("event is triggered."), 1000)
</lang-ts>
```

---

## 引用属性

引用属性是组件中非常重要的功能，它使得组件内部可以修改外部传入的值。由于 `props` 对象中的属性实际上是只读的 `getter`，因此无法直接修改其值。此时，就需要通过引用属性提供的引用传递机制来实现这一目的。在 qingkuai 中，组件文件内置了一个 `refs` 对象，用于访问外部传入的引用属性。通过修改 `refs` 中的属性值，不仅可以同步改变外部传入的数据，还能触发其响应性更新。下面是一个简单的使用示例：

|js|ts|

```qk
<!-- Outter.qk -->
<lang-js>
    import Inner from "./Inner.qk"

    let name = "Javascript"
</lang-js>

<p>name is: {name}</p>
<Inner &attr={name} />

<!-- Inner.qk -->
<p>refs.attr is: {refs.attr}</p>
<button @click={refs.attr = "QingKuai"}>Change refs.attr</button>
```

```qk
<!-- Outter.qk -->
<lang-ts>
    import Inner from "./Inner.qk"

    let name = "Javascript"
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
<button @click={refs.attr = "QingKuai"}>Change refs.attr</button>
```

<div class="custom-block tip">
    如果 <code>props</code> 中的某个属性值本身是引用类型（如对象或数组），那么你可以修改它的内部属性。例如，当 <code>props.obj</code> 是一个对象时，<code>props.obj.xxx</code> 是可以被修改的。
</div>

---

## 属性解构

直接解构 `props` 或 `refs` 内建对象的得到的值本身不具有响应性。因为访问内建对象属性时会触发 `getter` 调用以收集依赖的响应性状态：

```js
const { str } = props
```

要解构内建对象就需要搭配我们之前介绍的衍生响应性状态的[解构响应性声明](../basic/reactivity.html#解构响应性声明)语法：

```js
const { value } = der(refs)
const { str, arr } = der(props)
```

<div class="custom-block tip">
    当对 <code>refs</code> 进行解构时，如果解构出的属性是值类型（如字符串、数字、布尔值），那么对它的修改不会影响原始引用的值。因为此时得到的是一个值拷贝，而不是对原值的引用，这与 JavaScript 的默认行为一致。
</div>

---

## 属性名称格式

qingkuai 组件的属性名称同组件名称一样，支持 kebab 格式和驼峰格式，下面的写法是等效的：

```qk
<Component myAttr />
<Component my-attr />
```

默认情况下，格式化组件文件时会将所有 kebab 格式的组件属性或事件名称整理为驼峰格式。但你可以通过向组件文件所在目录或其上级目录中添加 `.prettierrc` 文件，并输入以下内容将组件名称偏好修改为 kebab 格式：

```json
{
    "qingkuai": {
        "componentAttributeFormatPreference": "kebab"
    }
}
```

<div class="custom-block tip">
    使用此配置时 qingkuai 语言服务器在提供组件属性的补全建议时也会优先提示kebab格式的属性名称。
</div>
