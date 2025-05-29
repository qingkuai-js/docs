# 引用属性

在 qingkuai 中，引用属性是一种通过在属性名前添加 `&` 字符来传递变量引用的语法（如 &value）。它允许你在模板中将某个属性与外部变量建立引用关系，使得该属性值可以被外部直接读写，从而实现灵活的数据联动和状态传递。

JavaScript 本身并不支持[引用传递](https://baike.baidu.com/item/%E5%BC%95%E7%94%A8%E4%BC%A0%E9%80%92?fromModule=lemma_search-box)，但 qingkuai 实现了一种类似于 C、C++、Go 等语言中的引用（指针）传递机制。借助引用属性，变量可以被显式“传址”传入某个元素或组件中，使你能够在多个上下文之间共享和操作同一个状态源。这种机制简洁而强大，提升了模板中状态管理的表达力和控制力。观察下面这段伪代码理解引用传递与值传递的区别：

```js
let number = 10

function passByValue(n) {
    // 在值传递中，n 是 number 的一个副本，
    // 它们在内存中是两个独立的位置，
    // 修改 n 不会影响原始的 number 值。
    n = n + 1
}

function passByReference(n) {
    // * 表示解引用操作符，它用于：
    // 读取指针 n 所指向的位置的值，
    // 加 1 后再将结果写回该位置。
    *n = *n + 1
}

passByValue(number)
console.log(number) // 10

// & 表示取地址操作符，它用于：
// 取得 number 变量的内存地址，
// 并作为参数传给 passByReference。
passByReference(&number)
console.log(number) // 11
```

---

## 获取 DOM 元素

当你需要获取模板中某个普通标签对应的 DOM 元素时，可以通过为该元素添加 `&dom` 属性来实现：

|js|ts|

```qk
<lang-ts>
    import { onAfterMount } from "qingkuai"

    let div!: HTMLDivElement
    onAfterMount(() => console.log(div))
</lang-ts>

<div &dom={div}></div>
```

```qk
<lang-ts>
    import { onAfterMount } from "qingkuai"

    let div: HTMLDivElement
    onAfterMount(() => console.log(div!))
</lang-ts>

<div &dom={div}></div>
```

<div class="custom-block tip">
    <code>onAfterMount</code> 是 qingkuai 的组件完成挂载与渲染之后的回调方法，它是 <a href="">组件生命周期</a>的一部分。
</div>

<div class="custom-block tip">
    如果你的嵌入脚本语言类型为 <a href="https://www.typescriptlang.org/">Typescript</a>，<code>&dom</code> 属性值是类型严格的，例如：对于 <code>div</code> 标签来说它的类型是 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLDivElement">HTMLDivElement</a>；对于 <code>p</code> 元素来说它的类型需要是 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLParagraphElement">HTMLParagraphElement</a>；当然你也可以将接受者类型定义为元素的基类 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement">HTMLElement</a>。
</div>

与动态属性一样，当引用属性和变量名称一致时可省略插值块，所以下面这两种写法是等效的：

```qk
<div &dom></div>
<div &dom={dom}></div>
```

<div class="custom-block warning">
    若属性名称是嵌入脚本语言中的关键字或保留字，则不支持这种语法，如 <code>class</code> 或 <code>for</code> 属性等。
</div>

---

## 表单输入处理

在使用表单时，我们常常需要将表单输入框的内容同步给嵌入脚本中相应的变量。通常情况下，我们可能需要在 `input` 元素中使用动态 `value` 属性，并监听用户输入以同步修改变量值：

|js|ts|

```qk
<lang-js>
    let inputValue = "Initial value"
</lang-js>

The inputValue is: {inputValue} <br />
<input
    type="text"
    !value={inputValue}
    @input={inputValue = $arg.target.value}
/>
```

```qk
<lang-ts>
    let inputValue = "Initial value"
</lang-ts>

The inputValue is: {inputValue} <br />
<input
    type="text"
    !value={inputValue}
    @input={inputValue = ($arg.target as HTMLInputElement).value}
/>
```

相较于上方示例中略显繁琐的做法，现在我们可以通过 `&value` 引用属性更简洁地实现这一需求：

```qk
<lang-js>
    let inputValue = "Initial value"
</lang-js>

The inputValue is: {inputValue} <br />
<input
    type="text"
    &value={inputValue}
/>
```

<div class="custom-block tip">
    这里我们只是简单介绍了引用属性在处理表单输入时的用法，关于引用属性在表单中的更多使用细节我们将在 <a href="">表单处理</a> 一节中介绍。
</div>

---

## 引用属性的合法值

与其他支持引用传递的语言一样，引用属性的值必须是 `可取址` 且不是常量的。你可以大致地将其理解为能出现等号左侧的表达式：

```qk
<!-- 合法值 -->
<p &dom={identifier}></p>
<p &dom={arr[index]}></p>
<p &dom={obj.property}></p>

<!-- 非法值 -->
<p &dom={test()}></p>
<p &dom={arr?.[index]}></p>
<p &dom={obj?.property}></p>
<p &dom={condition ? v1: v2}></p>
```
