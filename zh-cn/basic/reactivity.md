# 响应性

在前端开发中，<b>响应性（Reactivity）</b>是一种让数据状态与界面保持自动同步的机制。它的核心思想是：当数据发生变化时，界面会自动更新，无需手动操作 DOM。过去，开发者需要在业务逻辑中显式地操作页面元素以反映数据变化，这不仅繁琐，而且容易出错；而响应性系统通过“追踪依赖”和“自动更新”，极大提升了开发效率和代码可维护性。

---

## 响应性声明

在 Qingkuai 中，无需手动声明响应性变量。编译器会根据 [响应性推导规则](/references/reactivity-infer-rules.html) 为标识符附加响应式能力。下面代码中，`progress` 在脚本里从 “pending” 被修改为 “completed” 后，模板会自动完成更新，这正是响应性的简单应用：

```qk
<lang-js>
    let progress = "pending"

    setTimeout(() => {
        progress = "completed"
    }, 1000)
</lang-js>

<h1>Task status: {progress}</h1>
```

不过在某些情况下，你可能希望阻止这一默认行为。这时，可以使用编译器内建的 `raw` 方法将标识符标记为静态，从而避免为其添加响应式能力。例如下面这段代码中的 `progress` 变更后不会导致页面发生变化：

```qk
<lang-js>
    let progress = raw("pending")

    setTimeout(() => {
        progress = "completed"
    }, 1000)
</lang-js>

<h1>Task status: {progress}</h1>
```

对于未在模板中访问的标识符，还可以使用编译器内建的 `reactive` 或 `shallow` 方法手动标记其需要具有响应性：

```js
let progress = reactive("pending") // 具有响应性
```

<div class="custom-block warning">
    一般情况下，我们并不推荐这样做，因为你很可能只是想在脚本中单独使用响应性能力。Qingkuai 的设计理念是：响应性系统主要用于需要自动更新页面的场景。在脚本中，我们应尽量使用函数组合等方式组织逻辑，而不是过度依赖响应性机制。另一方面，响应式数据的操作本身也有一定开销。使用过多时，变更流程往往不够直观，既难以清晰表达执行逻辑，也不便于通过代码跳转等方式进行代码审查。
</div>

---

## 响应性别名

Qingkuai 的别名绑定提供了一种简洁的响应式访问/写入能力。对于嵌套较深的属性，可以通过编译器内建的 `alias` 方法创建一个更短的标识符别名，从而简化响应式访问代码：

```qk
<lang-js>
    let name = alias(refs.userInfo.detail.information.name)

    // name -> refs.userInfo.detail.information.name
    // 对 name 的写入具有响应性，且等价于写入 refs.userInfo.detail.information.name
    setTimeout(() => {
        name = "Unknown"
    }, 1000)
</lang-js>

<!-- name -> refs.userInfo.detail.information.name -->
<!-- 对 name 的访问具有响应性，且等价于访问 refs.userInfo.detail.information.name -->
<p>User name is: {name}</p>
```

别名绑定在表现上与其他语言中的[引用传递](https://baike.baidu.com/item/%E5%BC%95%E7%94%A8%E4%BC%A0%E9%80%92?fromModule=lemma_search-box)非常相似，但并不完全等价于传统意义上的引用传递。其实现原理是：编译器会将对别名标识符的访问和写入重写为对原始标识符的访问和写入，从而获得响应式读写能力。这一点与我们后续要介绍的[引用属性](../basic/reference-attributes.html)也有相通之处。

<div class="custom-block warning">
    别名能力也可以用于非响应式值，但不建议滥用。它的设计初衷是简化深层嵌套属性的响应式访问，因此建议仅在这类场景中使用。最佳实践是优先将此能力用于组件 <a href="../components/attributes.html">props</a> 和 <a href="../components/attributes.html#引用属性">refs</a>，其他场景请谨慎评估后再使用。
</div>

---

## 响应性模式

Qingkuai 支持两种响应性模式：深度响应性和浅层响应性。默认情况下，编译器会为所有标识符附加深度响应式能力。即使其属性是复杂类型（如对象或数组），也会被递归地添加响应性能力；而浅层响应性只会为标识符本身添加响应式能力，其属性即使是复杂类型也不会被添加响应性。

要修改默认的响应性模式，可以在当前目录或上级目录中添加 `.qingkuairc` 配置文件，修改其内容为：

```json
{
    "reactivityMode": "shallow"
}
```

通过配置文件设置的响应性模式会对当前目录及其所有子目录生效，直到遇到另一个配置文件为止。如果想要在单一组件文件中使用不同的响应性模式，可以在嵌入脚本标签上添加 `reactive` 或 `shallow` 属性来覆盖默认设置：

```qk
<lang-js shallow>
    // 编译器推导标识符是否具有浅层响应性
</lang-js>

<lang-js reactive>
    // 编译器推导标识符是否具有深度响应性
</lang-js>
```

---

## 获取原始值

当一个复杂类型的标识符被推导为响应性时，它的属性也会被递归地推导为响应性。这意味着当我们访问该值或其属性时，拿到的通常不是原始值，而是编译器包装后的响应式代理对象。在某些场景下，我们可能需要获取原始值来进行比较或其他操作，此时可以使用 `qingkuai` 包中导出的 `toRaw` 方法：

```js
import { toRaw } from "qingkuai"

const inner = {}
const outer = reactive({ inner })
console.log(outer.inner === inner) // false
console.log(toRaw(outer.inner) === inner) // true
console.log(toRaw(outer).inner === inner) // true
```

---

## 获取响应式值

Qingkuai 还提供了 `toReactive` 和 `toShallowReactive` 方法，用于获取某个值对应的响应式代理对象：

```js
import { toReactive } from "qingkuai"

const obj = { count: 0 }
const reactiveObj = toReactive(obj)
```

<div class="custom-block warning">
    需要注意，<code>toReactive</code> 并不会为传入值新增响应式能力，它只负责返回该值的响应式代理对象。因此，如果传入值本身未被编译器推导或明确标记为响应式，那么通过 <code>toReactive</code> 获取到的代理对象同样不具备响应式能力。
</div>

---

## 衍生响应式状态

衍生响应式状态是指依赖其他响应性值的运算过程。当这些被依赖的响应性值发生变化时，相关运算会自动重新执行，以生成最新结果。在 Qingkuai 中，我们提供了两种方式来声明衍生响应式状态：

1. 使用以 `$` 开头的变量标识符；
2. 使用编译器内建方法 `derived` 或 `derivedExp`；

```js
let number = 10
const $double = number * 2
const double = derived(() => number * 2)
```

使用简写声明（以 `$` 开头的变量标识符）时，若计算逻辑比较复杂，也可以将标识符的初始值设置为一个函数表达式，这个函数表达式的返回值会被编译器自动推导为衍生响应式状态：

```js
const $result = () => {
    const double = number * 2
    return isSpecial(double) ? Math.abs(double) : double
}
```

与 `derived` 方法不同，`derivedExp` 方法允许我们直接传入一个表达式来声明衍生响应式状态（和简写声明且初始值非函数时的行为类似），对于一些简单的计算逻辑，这种方式会更简洁：

```js
const double = derivedExp(number * 2)
```

实际开发中，我们常常需要在模板的插值块中编写 JS/TS 表达式，其中不乏较为复杂的逻辑。如果模板中充斥大量复杂表达式，往往会导致代码混乱、可读性下降。此时，使用衍生响应式状态来提取和表示这些复杂表达式，会是一个更清晰、高效的做法：

```qk
<lang-js>
    const $result = () => {
        const normalized = number < 0 ? Math.abs(number) : number
        return normalized * 2
    }
</lang-js>

<p>the calculation result is: {$result}</p>
```

如果你不需要使用衍生响应式状态的简写声明功能，可以在当前目录或上级目录中添加 `.qingkuairc` 配置文件，并写入以下内容：

```json
{
    "convenientDerivedDeclaration": false
}
```

---

## 响应性状态存储

很多时候，我们不只需要在组件内部声明响应性变量，还需要在组件外部声明，甚至需要在多个组件之间共享它们。此时可以使用 `qingkuai` 的响应性状态存储 API 在外部创建并导出响应性变量：

```js
// store.js
import { createStore } from "qingkuai"

export const store = createStore({
    isLogin: false,
    userInfo: null
    // other properties ...
})
```

在多个组件中分别导入它，即可共享响应性状态：

```qk
<!-- Header.qk -->
<lang-js>
    import { store } from "./store"

    function handleLogin(){
        /* ... */
    }
</lang-js>

<header>
    <button
        #if={!store.isLogin}
        @click={handleLogin}
    >
        Login
    </button>
    <p #else>Hello {store.userInfo.name}</p>
</header>
```

```qk
<!-- UserCard.qk -->
<lang-js>
    import { store } from "./store"
</lang-js>

<qk:spread #if={store.isLogin}>
    <p>{store.userInfo.name}</p>
    <p>{store.userInfo.gender}</p>
</qk:spread>
```

<div class="custom-block tip">
    这里使用到的 <a href="../basic/compilation-directives.html#条件渲染">#if</a> 是一个<a href="../basic/compilation-directives.html">编译指令</a>，我们会在后续章节中进行介绍。它的作用是根据条件控制元素的渲染与否，在上方示例中我们通过它来实现了登录状态的条件渲染。
</div>

---

## 解构响应性声明

当我们需要从一个响应性对象中提取多个属性时，通常会使用解构赋值的语法来简化代码。在 Qingkuai 中，如果你想要解构一个响应性对象，并且希望解构后的变量也具有响应性能力，那么你可以直接使用 JavaScript 的解构赋值语法，编译器会自动为解构后的变量添加响应性能力：

```js
// 交由编译器推导的解构响应性声明
const { code, msg } = obj
const [start, end] = range

// 主动标记的解构响应性声明
const { code, msg } = reactive(obj)
const [start, end] = derivedExp(range.map(Math.ceil))
```

此外，`alias` 方法同样支持解构语法：

```js
const { code, msg } = alias(refs.response)
```

需要注意的是，衍生响应式状态简写声明不支持解构语法：

```js
const { $code } = obj
```
