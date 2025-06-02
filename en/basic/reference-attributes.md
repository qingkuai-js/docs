# Reference Attributes

In qingkuai, reference attributes are a syntax for passing variable references by prefixing attribute names with `&` (e.g., &value). They allow you to establish reference relationships between attributes and external variables in templates, enabling direct read/write access to attribute values from outside, thus achieving flexible data linkage and state transfer.

JavaScript itself doesn't support [pass-by-reference](https://stackoverflow.com/questions/373419/whats-the-difference-between-passing-by-reference-vs-passing-by-value), but qingkuai implements a mechanism similar to references (pointers) in languages like C, C++, and Go. Using reference attributes, variables can be explicitly "passed by reference" into elements or components, allowing you to share and manipulate the same state source across multiple contexts. This mechanism is concise yet powerful, enhancing expressiveness and control in template state management. Observe this pseudocode to understand the difference between pass-by-reference and pass-by-value:

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
    <code>onAfterMount</code> is qingkuai's callback method after component mounting and rendering completes, part of the <a href="">component lifecycle</a>.
</div>

<div class="custom-block tip">
    If using <a href="https://www.typescriptlang.org/">Typescript</a>, <code>&dom</code> attribute values are strictly typed - e.g. <a href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLDivElement">HTMLDivElement</a> for <code>div</code> tags, <a href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLParagraphElement">HTMLParagraphElement</a> for <code>p</code> elements. You can also use the base class <a href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement">HTMLElement</a>.
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

The inputValue is: {inputValue} <br />
<input
    type="text"
    !value={inputValue}
    @input={inputValue = $args.target.value}
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
    @input={inputValue = ($args.target as HTMLInputElement).value}
/>
```

Instead of the somewhat cumbersome approach above, we can achieve this more concisely using `&value` reference attributes:

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
    This briefly introduces reference attributes for form handling. More details will be covered in the <a href="">Form Handling</a> section.
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
