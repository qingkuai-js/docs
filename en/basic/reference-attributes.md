# Reference Attributes

In Qingkuai, reference attributes are a syntax for passing variable references by prefixing attribute names with `&` (for example, `&value`). They allow you to establish reference relationships between attributes and external variables in templates, enabling direct read and write access to attribute values from outside and therefore achieving flexible data linkage and state transfer.

JavaScript itself does not support [pass-by-reference](https://stackoverflow.com/questions/373419/whats-the-difference-between-passing-by-reference-vs-passing-by-value), but Qingkuai implements a mechanism similar to passing references, or pointers, in languages such as C, C++, and Go. By using reference attributes, variables can be explicitly "passed by reference" into an element or component, allowing the same state source to be shared and manipulated across multiple contexts. This mechanism is concise yet powerful and improves both expressiveness and control in template state management. The following pseudocode helps illustrate the difference between pass-by-reference and pass-by-value:

```js
let number = 10

function passByValue(n) {
    // In pass-by-value, n is a copy of number,
    // they occupy two separate memory locations,
    // modifying n won't affect the original number value.
    n = n + 1
}

function passByReference(n) {
    // * is the dereference operator, and it is used to:
    // Reads the value at the memory location pointed to by pointer n,
    // increments it by 1, then writes the result back to that location.
    *n = *n + 1
}

passByValue(number)
console.log(number) // 10

// & is the address-of operator, and it is used to:
// Gets the memory address of variable number,
// and passes it as an argument to passByReference.
passByReference(&number)
console.log(number) // 11
```

---

## Accessing DOM Elements

When you need to access the DOM element corresponding to a regular tag in templates, you can add the `&dom` attribute to that element:

|js|ts|

```qk
<lang-js>
    import { onAfterMount } from "qingkuai"

    let div = null
    onAfterMount(() => {
        console.log(div)
    })
</lang-js>

<div &dom={div}></div>
```

```qk
<lang-ts>
    import { onAfterMount } from "qingkuai"

    let div: HTMLDivElement | null = null
    onAfterMount(() => {
        console.log(div!)
    })
</lang-ts>

<div &dom={div}></div>
```

<div class="custom-block tip">
    <code>onAfterMount</code> is Qingkuai's callback method that runs after a component has finished mounting and rendering. It is part of the <a href="../components/lifecycle.html">component lifecycle</a>.
</div>

<div class="custom-block tip">
    If your embedded script language is <a href="https://www.typescriptlang.org/">TypeScript</a>, the value of the <code>&dom</code> attribute is strictly typed. For example, a <code>div</code> tag maps to <a href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLDivElement">HTMLDivElement</a>, and a <code>p</code> element maps to <a href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLParagraphElement">HTMLParagraphElement</a>. You can also use the base class <a href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement">HTMLElement</a>.
</div>

Like dynamic attributes, when reference attribute names match variable names, the interpolation block can be omitted, making these two syntaxes equivalent:

```qk
<div &dom></div>
<div &dom={dom}></div>
```

<div class="custom-block warning">
    This syntax isn't supported if the attribute name is a keyword or reserved word in the embedded scripting language, such as <code>class</code> or <code>for</code> attributes.
</div>

---

## Form Input Handling

For forms, we often need to synchronize input field contents with corresponding variables in embedded scripts. Typically, we might use dynamic `value` attributes in `input` elements and listen for user input to update variables:

|js|ts|

```qk
<lang-js>
    let inputValue = "Initial value"
</lang-js>

The inputValue is: {inputValue}

<div>
    <input
        type="text"
        !value={inputValue}
        @input={inputValue = $arg.target.value}
    />
</div>
```

```qk
<lang-ts>
    let inputValue = "Initial value"
</lang-ts>

The inputValue is: {inputValue}

<div>
    <input
        type="text"
        !value={inputValue}
        @input={inputValue = ($arg.target as HTMLInputElement).value}
    />
</div>
```

Instead of the somewhat cumbersome approach above, we can achieve this more concisely using `&value` reference attributes:

```qk
<lang-js>
    let inputValue = "Initial value"
</lang-js>

The inputValue is: {inputValue}

<div>
    <input
        type="text"
        &value={inputValue}
    />
</div>
```

<div class="custom-block tip">
    This is only a brief introduction to using reference attributes in form input scenarios. More details are covered in the <a href="./forms.html">Form Handling</a> section.
</div>

---

## Valid Reference Attribute Values

Like other languages supporting pass-by-reference, reference attribute values must be `addressable` and non-constant. You can roughly understand them as expressions that can appear on the left side of an assignment:

```qk
<!-- Valid values -->
<p &dom={identifier}></p>
<p &dom={arr[index]}></p>
<p &dom={obj.property}></p>

<!-- Invalid values -->
<p &dom={test()}></p>
<p &dom={arr?.[index]}></p>
<p &dom={obj?.property}></p>
<p &dom={condition ? v1: v2}></p>
```
