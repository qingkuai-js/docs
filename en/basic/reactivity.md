# Reactivity

In frontend development, <b>Reactivity</b> is a mechanism that keeps data state and interface automatically synchronized. Its core idea is: when data changes, the interface updates automatically without manual DOM manipulation.

In the past, developers needed to explicitly manipulate page elements in business logic to reflect data changes, which was not only tedious but also error-prone. The reactivity system, through "dependency tracking" and "automatic updates," significantly improves development efficiency and code maintainability.

Mainstream frontend frameworks (such as Vue, React, Svelte, etc.) have all implemented reactivity mechanisms at different levels. The reactivity system adopted by qingkuai further emphasizes performance and minimal update granularity, enabling updates to be precise down to the smallest unit of the DOM, resulting in faster rendering speeds and lower runtime overhead.

---

## Reactivity Declaration

In qingkuai, there's no need to manually declare reactive variables. As long as a variable is accessed in the template, the compiler will automatically recognize it and transform it into a reactive declaration during the compilation phase. The name variable in the following code is reactive:

```qk
<lang-js>
    let name = "world"

    setTimeout(() => {
        name = "QingKuai"
    }, 1000)
</lang-js>

<h1>Hello {name}!</h1>
```

However, in certain cases, you may want to prevent this default behavior. In such scenarios, you can use the built-in `stc` (short for static) helper function to mark variables as static, thereby avoiding their treatment as reactive variables. In this case, changes to name will not trigger page updates:

```qk
<lang-js>
    let name = stc("world")

    setTimeout(() => {
        name = "QingKuai"
    }, 1000)
</lang-js>

<h1>Hello {name}!</h1>
```

For variables not accessed in templates, you can also call `rea` (short for react) to manually add reactivity to them:

```js
let name = rea("world") // reactive
```

<div class="custom-block tip">
    Generally we don't recommend doing this, as you likely just want to use the reactivity system separately in scripts. However, qingkuai's design philosophy is: the reactivity system should only be used for scenarios requiring automatic page updates. In scripts, we should try to use function composition and other methods to organize side effect logic, rather than over-relying on reactivity mechanisms. Because reactivity change flows in scripts are often not intuitive enough - they neither clearly express execution logic nor facilitate code review through methods like code jumping.
</div>

---

## Reactive Depth

For variables of complex types, the default reactive depth is `Infinity`, meaning every level of nested objects / arrays / Sets / Maps will be reactive. The following code will update the page content after a one-second delay:

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

However, when dealing with large complex objects, this reactivity conversion pattern may cause some performance overhead. In such cases, we can manually control the reactive depth by calling the built-in `rea` helper function and passing a second parameter:

```js
const obj = {
    outter: {
        inner: [1, 2, 3]
    }
}

let v0 = rea(obj, 0) // Reactive depth 0, equivalent to stc helper function, v1 is not reactive
let v1 = rea(obj, 1) // Reactive depth 1, only modifying v1 itself will trigger reactive updates
let v2 = rea(obj, 2) // Reactive depth 2, modifying v2 itself or v2.outter will trigger reactive updates
let v3 = rea(obj, 3) // Reactive depth 3, modifying v3.outter.inner[index] won't trigger reactive updates
let v4 = rea(obj, 4) // Reactive depth 4, modifying any property at any path of v4 will trigger reactive updates, higher depths follow the same pattern...
```

---

## Getting Raw Values

When a complex object is converted into a reactive declaration by the compiler, if any of its properties are also complex types (like objects or arrays), each access to that property will return a new [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) wrapper. This behavior may lead to some unexpected issues or peculiar phenomena:

```js
const obj = rea({
    inner: {}
})
consle.log(obj.inner == obj.inner) // false
```

To get the expected result, we can use the `raw` method exported from the qingkuai package to obtain its raw value for comparison:

```js
import { raw } from "qingkuai"

const obj = rea({
    inner: {}
})
console.log(raw(obj).inner === raw(obj).inner) // true
```

---

## Derived Reactive State

Derived reactive state refers to computational processes that depend on other reactive values. When these dependent reactive values change, the related computations automatically re-execute to produce the latest results. In qingkuai, we provide two ways to declare derived reactive state:

1. Using variable identifiers prefixed with `$`;
2. Using the built-in `der` (short for derived) helper function;

```js
let number = 10
const $double = number * 2
const double = der(number * 2)
```

For complex computational processes, you can use functions as state initial values or parameters for the `der` method:

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

In actual development, we often need to write JS/TS expressions in template interpolation blocks, some of which contain relatively complex logic. If templates are filled with numerous complex expressions, it often leads to code clutter and reduced readability. In such cases, using derived reactive state to extract and represent these complex expressions would be a clearer and more efficient approach:

```qk
<lang-js>
    const number = -1
    const $result = (number < 0 ? Math.abs(number) : number) * 2
</lang-js>

<p>the calculation result is: {$result}</p>
```

If you don't need the convenience declaration feature of derived reactive state (through variable identifiers prefixed with `$`), you can add a `.qingkuairc` configuration file in the current or parent directory and modify its content to:

```json
{
    "convenientDerivedDeclaration": false
}
```

---

## Reactive State store

Often we need to declare reactive variables not just inside components, but also outside them, and may even need to share them across multiple components. In such cases, we can use qingkuai's reactive state store API to create reactive variables externally and

```js
// store.js
import { createStore } from "qingkuai"

export const store = createStore({
    isLogin: false,
    userInfo: null
    // other properties ...
})
```

Importing it across multiple components allows sharing reactive state:

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

## Destructuring Reactive Declarations

Besides the state store API and the convenient declaration syntax for derived reactive state, other reactive declarations support destructuring. The following declaration statements in this example will all be compiled into reactive declarations:

```js
const { code, msg } = obj
const { code, msg } = rea(obj)

const [start, end] = range
const [start, end] = der(range.map(Math.ceil))
```

Note, the convenient declaration of derived reactive state does not support destructuring syntax:

```js
const { $code } = obj
```
