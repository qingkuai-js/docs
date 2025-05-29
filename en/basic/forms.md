# Form Handling

In the previous [Reference Attributes](./reference-attributes.html) documentation, we learned the basic usage of obtaining variable references through the `&` syntax. In practical development, form element values often need to stay synchronized with state variables, making reference attributes particularly important in form handling scenarios. This section focuses on usage details of reference attributes with form elements, explaining how to efficiently obtain and control form data using this mechanism to achieve reactive data binding and updates.

---

## Text Input

In the [Form Input Handling](./reference-attributes.html#form-input-handling) section of the reference attributes documentation, we introduced how to use the `&value` reference attribute on `input` tags to synchronize input content with embedded script variables. Similarly, `textarea` tags support the same usage:

```qk
<lang-js>
    let inputValue = "Initial value"
</lang-js>

<p>The inputValue is: {inputValue}</p>
<textarea &value={inputValue}></textarea>
```

---

## Radio

For radio buttons and checkboxes, we can add `&checked` to synchronize the selected state with variable values:

```qk
<lang-js>
    let radioChecked = false
    let checkboxChecked = false
</lang-js>

<input
    type="radio"
    &checked={radioChecked}
/>
<input
    type="checkbox"
    &checked={checkboxChecked}
/>
<p>radio: {radioChecked ? "" : "not"} checked</p>
<p>checkbox: {checkboxChecked ? "" : "not"} checked</p>
```

---

## Checkbox

Sometimes you may need to synchronize multiple checkboxes' states. This can be achieved in several ways, with the core idea being to pass different targets for each checkbox's `&checked` attribute:

|js|ts|

```qk
<lang-js>
    const checkedArr = []
    const choices = ["Qingkuai", "Javascript", "Typescript"]
</lang-js>

<spread #for={item, index of choices}>
    <input
        type="checkbox"
        !id={"choice" + item}
        &checked={checkedArr[index]}
    />
    <label
        !for={"choice" + item}
        style="margin-right: 20px;"
    >
        {item}
    </label>
</spread>

<spread #for={item, index of choices}>
    <p>{item}: {checkedArr[index] ? "" : "not"} checked</p>
</spread>
```

```qk
<lang-ts>
    const checkedArr: boolean[] = []
    const choices = ["Qingkuai", "Javascript", "Typescript"]
</lang-ts>

<spread #for={item, index of choices}>
    <input
        type="checkbox"
        !id={"choice" + item}
        &checked={checkedArr[index]}
    />
    <label
        !for={"choice" + item}
        style="margin-right: 20px;"
    >
        {item}
    </label>
</spread>

<spread #for={item, index of choices}>
    <p>{item}: {checkedArr[index] ? "" : "not"} checked</p>
</spread>
```

如果要同步多个选择框的选择状态，可以使用 `&group` 属性，并传入

---

## Select

The select tag can accept the `&value` reference attribute:

```qk
<lang-js>
    let selected = "Typescript"
    const choices = ["QingKuai", "Javascript", "Typescript"]
</lang-js>

<select &value={selected}>
    <option
        !value={item}
        #for={item of choices}
    >
        {item}
    </option>
</select>
<p>Your selected: {selected}</p>
```

For multi-select enabled select elements, you can pass an [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) or [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) to its `&value` attribute. In this case the target can be constant since qingkuai will only call its methods without modifying it directly:

```qk
<lang-js>
    const selectedItems = ["QingKuai", "Typescript"]
    const choices = ["QingKuai", "Javascript", "Typescript"]
</lang-js>

<select
    multiple
    &value={selectedItems}
>
    <option
        !value={item}
        #for={item of choices}
    >
        {item}
    </option>
</select>
<p>Selected items: {selectedItems.join(", ")}</p>
```

We recommend using Set collections as they yield better performance in generated code:

```qk
<lang-js>
    const selectedItems = new Set(["QingKuai", "Typescript"])
    const choices = ["QingKuai", "Javascript", "Typescript"]
</lang-js>

<select
    multiple
    &value={selectedItems}
>
    <option
        !value={item}
        #for={item of choices}
    >
        {item}
    </option>
</select>
<p>Selected items: {Array.from(selectedItems).join(", ")}</p>
```
