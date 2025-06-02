# 响应性

在前端开发中，<b>响应性（Reactivity）</b>是一种让数据状态与界面保持自动同步的机制。它的核心思想是：当数据发生变化时，界面会自动更新，无需手动操作 DOM。

过去，开发者需要在业务逻辑中显式地操作页面元素以反映数据的变化，这不仅繁琐，而且容易出错。响应性系统通过“追踪依赖”和“自动更新”，极大地提升了开发效率和代码的可维护性。

主流前端框架（如 Vue、React、Svelte 等）都在不同层面实现了响应性机制。而 qingkuai 所采用的响应性系统，则进一步强调性能与最小更新粒度，让更新可以精确到 DOM 的最小单位，带来更快的渲染速度和更低的运行时开销。

---

## 响应性声明

在 qingkuai 中，无需手动声明响应性变量。只要变量在模板中被访问，编译器就会自动识别并将其在编译阶段转化为响应性声明，下面代码中的 name 变量就是具有响应性的：

```qk
<lang-js>
    let name = "world"

    setTimeout(() => {
        name = "QingKuai"
    }, 1000)
</lang-js>

<h1>Hello {name}!</h1>
```

不过在某些情况下，你可能希望阻止这一默认行为。这时，可以使用 `stc`（static 的缩写）内建辅助函数将变量标记为静态，从而避免其被处理为响应性变量，此时 name 的变更不会导致页面发生变化：

```qk
<lang-js>
    let name = stc("world")

    setTimeout(() => {
        name = "QingKuai"
    }, 1000)
</lang-js>

<h1>Hello {name}!</h1>
```

对于未在模板中访问的变量，还可以调用 `rea`（react 的缩写）手动为其添加响应性：

```js
let name = rea("world") // 具有响应性
```

<div class="custom-block warning">
    一般情况下我们并不推荐这样做，因为你很可能只是想在脚本中单独使用响应性系统。然而 qingkuai 的设计哲学是：响应性系统应仅用于需要自动更新页面的场景。在脚本中，我们应尽量使用函数组合等方式来组织副作用逻辑，而不是过度依赖响应性机制。因为响应性的变更流程在脚本中往往不够直观，既难以清晰表达执行逻辑，也不便于通过代码跳转等方式进行代码审查。
</div>

---

## 响应深度

对于复杂类型的变量，响应深度默认是 `Infinity` 的，也就是说嵌套 对象 / 数组 / Set / Map 的每一级都具有响应性，下面代码在延迟一秒后页面内容会更新：

```qk
<lang-js>
    const languages = {
        qk: {
            age: 1,
            name: "QingKuai"
        }
    }

    setTimeout(() => {
        languages.qk.age++
    }, 1000)
</lang-js>

<h1>qk: name is {languages.qk.name}, and released in {2025 - languages.qk.age}.</h1>
```

然而，在面对大型复杂对象时，这种响应性转换模式可能造成一定性能浪费，此时我们可以通过 `rea` 内建辅助函数调用并传入第二个参数来手动控制响应深度：

```js
const obj = {
    outter: {
        inner: [1, 2, 3]
    }
}

let v0 = rea(obj, 0) // 响应深度为0，与stc内建辅助函数等效，v0不具有响应性
let v1 = rea(obj, 1) // 响应深度为1，只有修改v1变量本身的值才会触发响应性更新
let v2 = rea(obj, 2) // 相应深度为2，修改v2变量本身或v2.outter时会触发响应性更新
let v3 = rea(obj, 3) // 响应深度为3，此时若修改v3.outter.inner[index]不会触发响应性更新
let v4 = rea(obj, 4) // 响应深度为4，此时修改v4变量任意路径的任意属性均会触发响应性更新，更高的深度以此类推...
```

---

## 获取原始值

当某个复杂对象被编译器转换为响应性声明后，如果它的某个属性也是复杂类型（如对象或数组），那么每次访问该属性时，都会返回一个新的 [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 包装器。这种行为可能会导致一些意料之外的问题或怪异现象：

```js
const obj = rea({
    inner: {}
})
consle.log(obj.inner == obj.inner) // false
```

想要得到预期结果，我们可以使用 qingkuai 包中导出的`raw`方法获取其原始值并进行比较：

```js
import { raw } from "qingkuai"

const obj = rea({
    inner: {}
})
console.log(raw(obj).inner === raw(obj).inner) // true
```

---

## 衍生响应性状态

衍生响应性状态是指那些依赖其他响应性值的运算过程。当这些被依赖的响应性值发生变化时，相关的运算会自动重新执行，以生成最新的结果。在 qingkuai 中，我们提供了两种方式来声明衍生响应性状态：

1. 使用以 `$` 开头的变量标识符；
2. 使用 `der`（derived 的缩写）内建辅助函数；

```js
let number = 10
const $double = number * 2
const double = der(number * 2)
```

对于复杂的运算过程，可以使用函数作为状态初始值或 der 方法的参数：

```js
let number = 10

const $result = () => {
    const double = number * 2
    return isSpecial(double) ? Math.abs(double) : double
}

const result = der(() => {
    const triple = (number * 3).toString()
    return triple.length >= 4 ? triple : triple.padStart(4, "0")
})
```

实际开发中，我们常常需要在模板的插值块中编写 JS/TS 表达式，其中不乏较为复杂的逻辑。如果模板中充斥大量复杂表达式，往往会导致代码混乱、可读性下降。此时，使用衍生响应性状态来提取和表示这些复杂表达式，会是一个更清晰、高效的做法：

```qk
<lang-js>
    const number = -1
    const $result = (number < 0 ? Math.abs(number) : number) * 2
</lang-js>

<p>the calculation result is: {$result}</p>
```

如果你不需要使用衍生响应性状态的便捷声明功能（通过以 `$` 开头的变量标识符），可以在当前目录或上级目录中添加 `.qingkuairc` 配置文件，修改其内容为：

```json
{
    "convenientDerivedDeclaration": false
}
```

---

## 响应性状态存储

很多时候我们不止需要再组件内部声明响应性变量，还需要在组件外部声明，甚至可能需要再多个组件间共享它们，此时我们可以使用 qingkuai 的响应性状态存储 API 在外部创建响应性变量并导出：

```js
// store.js
import { createStore } from "qingkuai"

export const store = createStore({
    isLogin: false,
    userInfo: null
    // other properties ...
})
```

在多个组件间分别导入它可共享响应性状态：

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
    <p #else>Hello {store.userInfo.name}<p>
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

---

## 解构响应性声明

除了状态存储 API 和衍生响应性状态便捷声明语法，其他响应性声明都支持解构声明，下面实例中的声明语句都会被编译为响应性声明：

```js
const { code, msg } = obj
const { code, msg } = rea(obj)

const [start, end] = range
const [start, end] = der(range.map(Math.ceil))
```

注意，衍生响应性状态便捷声明不支持结构语法：

```js
const { $code } = obj
```
