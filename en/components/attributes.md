# Attributes

In qingkuai, we can also add attributes to component tags just like regular HTML tags to pass in parameters. This behavior is called component prop passing and is used to provide external data or configuration to a component. With component props, we can make a component behave differently or look different in different scenarios, enhancing its reusability and flexibility.

---

## Normal Attributes

Normal attributes are passed to components as strings and accessed within the component via the built-in `props` object:

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
    If your embedded script language is TypeScript or if you want component prop autocomplete, please first read <a href="../misc/typescript.html">TypeScript Support</a> before continuing with this section.
</div>

If you add an attribute name to the component tag without assigning it a value, the component receives a boolean `true` internally:

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
    In the TypeScript type system, such valueless props are also considered as boolean.
</div>

---

## Dynamic Aattributes

Dynamic attributes passed to components are also accessed internally through the `props` built-in object. However, dynamic attributes can carry various data types beyond just strings. Moreover, when the data source changes, DOM elements using that attribute inside the component will be updated accordingly:

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

## Events

Component events, like other non-ref props, are accessed inside the component through the built-in `props` object:

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

## Reference Attributes

Reference attributes are a very important feature in components. They allow the component to modify values passed in from outside. Since properties in the `props` object are actually read-only getters, their values cannot be modified directly. At this point, you need to use the reference-passing mechanism provided by ref attributes to achieve this. In qingkuai, the component file has a built-in `refs` object for accessing ref attributes passed from outside. By modifying properties on `refs`, you can not only update external data but also trigger its reactive updates. Here's a simple example:

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
    If a value in <code>props</code> is itself a reference type (such as an object or array), you can modify its internal properties. For example, when <code>props.obj</code> is an object, <code>props.obj.xxx</code> can be modified.
</div>

---

## Destructure Built-in Objects

Values obtained by directly destructuring the `props` or `refs` built-in objects are not themselves reactive. This is because accessing built-in object properties triggers the `getter` to collect reactive dependencies:

```js
const { str } = props
```

To destructure built-in objects properly, you need to use the [reactive destructure declaration](../basic/reactivity.html#destructuring-reactive-declarations) syntax we introduced earlier:

```js
const { value } = der(refs)
const { str, arr } = der(props)
```

<div class="custom-block tip">
    When destructuring <code>refs</code>, if the destructured property is a primitive (such as a string, number, or boolean), modifying it will not affect the original ref value. This is because what you get is a value copy, not a reference to the original — which is consistent with JavaScript's default behavior.
</div>

---

## Attribute Name Format

Component attribute names in qingkuai, just like component names, support both kebab-case and camelCase. The following usages are equivalent:

```qk
<Component myAttr />
<Component my-attr />
```

By default, when formatting component files, all kebab-case component attribute or event names will be converted to camelCase. However, you can add a `.prettierrc` file to the component file's directory or its parent directories and include the following content to change the preference to kebab-case:

```json
{
    "qingkuai": {
        "componentAttributeFormatPreference": "kebab"
    }
}
```

<div class="custom-block tip">When using this configuration, the qingkuai language server will also prioritize suggesting kebab-case attribute names during autocomplete.</div>
