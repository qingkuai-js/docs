# 事件处理

在前端开发中，用户与页面的交互往往通过事件来驱动。qingkuai 提供了简洁直观的事件绑定语法，使你可以轻松地为元素添加点击、输入、键盘等各种事件处理逻辑，帮助实现响应性的用户交互体验。

在 qingkuai 中，以 `@` 字符开头的属性用于绑定事件处理器，例如 `@click` 表示监听点击事件。这种语法与 HTML 中的 `onclick` 属性类似，但它更强大，支持使用插值块编写任意表达式作为事件处理逻辑，不仅更简洁，也更具表现力。

---

## 绑定事件

下面这段代码为 button 元素绑定了一个点击事件，按钮被点击时，`handleAddCount` 方法会被调用：

```qk
<lang-js>
    let count = 0

    function handleAddCount(){
        count++ // 修改响应性变量，p元素内容会被自动更新
    }
</lang-js>

<p>current count: {count}</p>
<button @click={handleAddCount}>Add Count</button>
```

像 `handleAddCount` 这样的函数被称为事件处理器，它们可以声明参数来接收事件对象，这一点与原生调用 `addEventListener` 时的行为是一致的：

|js|ts|

```js
function handleAddCount(e) {
    count++
    console.log(e.target === this) // true，均指向被点击的button元素
}
```

```ts
function handleAddCount(this: HTMLButtonElement, e: MouseEvent) {
    count++
    console.log(e.target === this) // true，均指向被点击的button元素
}
```

与动态属性一样，事件与变量名称一致时可省略插值块，所以下面这两种写法是等效的：

```qk
<button @click></button>
<button @click={click}></button>
```

<div class="custom-block warning">若事件名称是嵌入脚本语言中的关键字或保留字，则不支持这种语法，如 <code>class</code> 或 <code>for</code> 属性等。</div>

---

## 内联事件处理器

在上方示例中，事件处理器仅包含一行代码，因此我们似乎没有必要单独为其声明一个方法。对其我们有两种方式简化代码：

1.  直接在插值块中使用箭头函数：

    ```qk
    <button @click={() => count++}>Add Count</button>
    ```

2.  使用内联事件处理器语法，即在插值块中直接编写 JS/TS 表达式：

    ```qk
    <button @click={count++}>Add Count</button>
    ```

第二种方法中的内联事件处理器会被编译器处理为类似下面的代码：

```qk
<button @click={$args => count++}>Add Count</button>
```

| 所以我们还可以在内联事件处理器中通过 `$args` 访问原生事件对象：

```qk
<button @click={$args => console.log($args.target)}>Add Count</button>
```

<div class="custom-block tip">
    从原生事件的角度来看，将 <code>$args</code> 命名为 <code>$event</code> 可能更合理，但从语义一致性的角度出发，使用 $args 更能涵盖我们之后要介绍到的<a href="../components/basic.html">组件</a>内联事件处理器中所传入的任意参数。因此，在 qingkuai 中我们统一使用 $args 作为事件处理器的默认参数名，以体现其在组件和原生事件中的通用性——它不仅可以表示原生事件对象，也可以表示组件传入的任意参数。
</div>

如果你在内联事件处理器中调用了其他方法，qingkuai 会自动将这些被调用方法中的 this 绑定为当前元素：

|js|ts|

```qk
<lang-js>
    let count = 0

    function handleAddCount(e) {
        count++
        console.log(e.target === this) // true，均指向被点击的button元素
    }
</lang-js>

<p>current count: {count}</p>
<button @click={handleAddCount($args)}>Add Count</button>
```

```qk
<lang-ts>
    let count = 0

    function handleAddCount(this: HTMLButtonElement, e: MouseEvent) {
        count++
        console.log(e.target === this) // true，均指向被点击的button元素
    }
</lang-ts>

<p>current count: {count}</p>
<button @click={handleAddCount($args)}>Add Count</button>
```

<div class="custom-block tip">
    如果你的嵌入脚本语言类型为 <a href="https://www.typescriptlang.org">Typescript</a>，<code>$args</code> 的类型是严格的，例如：对于 <code>@keydown</code> 事件它的类型就是 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/KeyboardEvent">KeyboardEvent</a>；对于 <code>@click</code> 事件它的类型就是 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent">MouseEvent</a> 等等
</div>

---

## 处理器类型判定

看到这里你可能会有一个疑问：什么情况下是内联事件处理器，什么情况下又是普通事件处理器？在 qingkuai 编译器的视角中，内联事件处理器指的是那些需要被编译为包裹在函数中的插值表达式；而当插值表达式是一个单独的标识符、属性访问表达式、一个函数声明或一个箭头函数时，编译器会认为它已经是一个完整的函数，因而无需额外包裹，属于普通事件处理器：

```qk
<!-- 普通事件处理器 -->
<tag @click={()=>{}}></tag>
<tag @click={identifier}></tag>
<tag @click={handlers.click}></tag>
<tag @click={function anonymous(){}}></tag>

<!-- 内联事件处理器 -->
<tag @click={count++}></tag>
<tag @click={handlers?.click()}></tag>
<tag @click={n > 10? yes(): no()}></tag>
```

---

## 事件处理器标志

qingkuai 允许在事件名称后附加一些标志，用于简化常见交互需求的代码编写。目前支持的标志类型包括以下几种：

1.  功能性标志：

    -   self：仅当 `event.target` 是自身时才执行绑定的事件处理器（不阻止触发）；
    -   stop：在事件处理器的最后调用 [stopPropagation](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopPropagation) 以阻止冒泡传播；
    -   prevent：在事件处理器的最后调用 [preventDefault](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault) 以阻止默认行为；
    -   compose：在输入合成阶段（中文等 IME 输入法选词时）触发绑定的事件处理器（仅对 input 事件生效）；
    -   once：事件处理器在第一次被触发并执行后会被移除，效果同 [addEventListener options.once](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#options)；
    -   capture：是否在捕获阶段触发绑定的事件处理器，效果同 [addEventListener options.capture](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#options)；
    -   passive：告知客户端事件处理器中永远不会调用 `event.preventDefault`，多用于移动端性能优化，效果同[addEventListener options.passive](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#options)；

2.  按键标志，只有相关按键保持按下才会触发绑定的事件处理器，这些标志只能用于键盘相关事件（keyup、keydown 等）：

    -   普通按键标志：enter、tab、del、esc、up、down、left、right、space；
    -   系统按键标志：meta、alt、ctrl、shift；

3.  其他标志：
    -   compose：是否在输入合成阶段（例如中文、日文、韩文输入法选词时）触发绑定的事件处理器，此标志仅可用于 @input；

以下是一些为事件处理器传递标志的示例：

```qk
<!-- 仅点击Click部分才触发执行事件处理器，且只触发执行一次 -->
<button @click|self|once={console.log("ok")}>
    Click <span>Me</span>
</button>

<!-- 仅当alt键和shift键被同时保持按下且点击按钮时才触发执行事件处理器 -->
<button @click|alt|shift={console.log("ok")}> Click Me </button>
```
