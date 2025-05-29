# Compilation Directives

Directives are core components of qingkuai, represented as special attributes prefixed with `#` that instruct the qingkuai compiler how to generate corresponding JavaScript code. qingkuai provides a rich set of built-in directives covering multiple aspects including flow control, rendering control, and asynchronous processing:

-   Rendering control directives: target, html, show - control content insertion position and display
-   Flow control directives: for, if, el-if, else - control structural rendering logic
-   Async processing directives: await, then, catch - respond to asynchronous data changes

Additionally, there's a `slot` directive for receiving component slot parameters, which we'll introduce after explaining the [component](../components/basic.html) concept.

---

## Conditional Rendering

In qingkuai, we implement conditional rendering logic by combining if, elif and else directives, similar to Javascript's if, else if and else keywords. Consider this scenario: showing login prompts when users aren't logged in, and displaying user information after login - a very common frontend requirement. We can easily implement this logic using qingkuai's conditional rendering:

```qk
<qk:spread #if={userInfo}>
    <p>View after logging in.</p>
    <button
        class="login-btn"
        @click={handleLogin}
    >
        Login
    </button>
</qk:spread>
<p #else>Hello {userInfo.name}!</p>
```

<div class="custom-block tip">
    In the example above, the <code>qk:spread</code> tag serves as a virtual mounting point for directives. It won't be rendered to the page - you can understand that directives on this element will be applied to all child nodes. This design avoids introducing unnecessary meaningless elements, and also makes it possible to add directives to text nodes. We'll explain more usage details in <a href="../components/builtin-elements.html">Built-in Elements</a>.
</div>

We can also insert `elif` directives between `if` and `else` as branch nodes:

```qk
<p #if={language === "qk"}>QingKuai</p>
<p #elif={language === "js"}>Javascrip</p>
<p #elif={language === "ts"}>Typescrip</p>
<p #else>Language is not Qingkuai, Javascript, or Typescript.</p>
```

Alternatively, we can use the `show` directive to control element visibility. Unlike the three directives mentioned above, show doesn't unmount elements from the page - it simply controls whether to add the `display: none;` style rule. Therefore, for elements requiring frequent visibility toggling, the show directive is preferable due to its lower overhead:

```qk
<div #show={visiable}>
    <!-- some contents> -->
</div>
```

---

## List Rendering

List rendering is very convenient in qingkuai. Here's a basic usage example, commonly used during development testing to quickly create list rendering:

```qk
<p #for={3}>Paragraph in list rendering.<p>
```

| This will render three consecutive p tags:

```html
<p>Paragraph in list rendering.</p>
<p>Paragraph in list rendering.</p>
<p>Paragraph in list rendering.</p>
```

The `for` directive's value can be not just a number, but also arrays, objects, strings, Sets ([Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)), Maps ([Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)), or expressions evaluating to these types. Additionally, you can use `for...of`-like syntax to name each iteration's item and index.

```qk
<p #for={item, index of [1, 2 , 3]}>{index}: {item}</p>
```

| The rendered result will be:

```html
<p>0: 1</p>
<p>1: 2</p>
<p>2: 3</p>
```

List rendering with Map:

```qk
<lang-js>
    const languages = new Map([
        ["qk", "Qingkuai"],
        ["js", "Javascript"],
        ["ts", "Typescript"]
    ])
</lang-js>

<p #for={item, index of languages}>{index}: {item}</p>
```

| The rendered result will be:

```html
<p>qk: QingKuai</p>
<p>js: Javascript</p>
<p>ts: Typescript</p>
```

When naming for directive iteration items and indexes, you can also use [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring) syntax at the item or index identifier name:

```qk
<lang-js>
    const languageInfos = {
        qk: {
            age: 1,
            name: "QingKuai"
        },
        js: {
            age: 30,
            name: "Javascript"
        },
        ts: {
            age: 13,
            name: "Typescript"
        }
    }
</lang-js>

<p #for={{ name, age }, extension of languageInfos}>
    {name}: file extension is {extension}, released in {2025 - age}.
</p>
```

| The rendered result will be:

```html
<p>QingKuai: file extension is qk, released in 2024.</p>
<p>Javascript: file extension is js, released in 1995.</p>
<p>Typescript: file extension is ts, released in 2012.</p>
```

If you've used [vue](https://cn.vuejs.org), you might wonder why the `for` directive uses `of` rather than `in` as the iteration keyword. This is because the in keyword can appear in Javascript expressions while of cannot. For example, if we used in, cases like this would be hard to handle:

```qk
<p #for={prop in obj ? 3 : 2}>...</p>
```

---

## key Directive

-   When using the `for` directive to create list rendering, if the directive's dependent reactive variable changes, the list updates with this logic:

    1. If the new list is longer, new elements are created and appended; if shorter, extra elements are removed from the end
    2. During qingkuai's scheduled updates, each list element's attributes and textContent are updated

    <img class="large-margin" src="https://qingkuai-js.oss-cn-beijing.aliyuncs.com/docs/no-key-update-en.gif" style="width: 90%; margin-left: 5%;">

-   This causes an issue: if nodes have their own state (typically form elements), the state may become disordered since list changes don't always just append/remove at the end. The `#key` directive solves this by assigning each element a unique key for identification. The update logic then becomes:

    1. Check if keys exist in the new list, and position their corresponding elements correctly
    2. During updates, each element's attributes and textContent are updated

    <img class="large-margin" src="https://qingkuai-js.oss-cn-beijing.aliyuncs.com/docs/has-key-update-en.gif" style="width: 90%; margin-left: 5%;">

Therefore, when list-rendered elements have state, it's recommended to add the key directive to elements using the for directive:

```qk
<form>
    <input
        #for={user of users}
        #key={user.id}
        !value={user.name}
        placeholder="user name"
    />
</form>
```

<div class="custom-block warning">
    qingkuai runtime converts key directive interpolations to strings via <code>"" + [Interpolation expression]</code>, and checks for duplicate key values in the list - duplicates will throw runtime errors! Each item's key directive value should be unique within a single list.
</div>

---

## Async Processing

In some scenarios, you may need to asynchronously wait for a state in embedded scripts before rendering. qingkuai's async processing directives make this easy. The await directive accepts a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) - after resolution, then and catch directives can render different content for success/failure cases:

```qk
<p #await={pms}>waiting...</p>
<p #then>pms is resolved.</p>
<p #catch>pms is rejected.</p>
```

To access Promise resolution/rejection values, simply set the then/catch directive value to a Javascript identifier:

```qk
<p #await={pms}>waiting...</p>
<p #then={res}>pms is resolved and received {res}.</p>
<p #catch={err}>pms is rejected and received {err}.</p>
```

then/catch directive values also support [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring) syntax:

```qk
<p #await={pms}>waiting...</p>
<p
    #then={
        {
            id: userId,
            name: userName
        }
    }
>
    pms is resolved and user id is {userId}, user name is {userName}.
</p>
<p #catch={{msg, code}}>pms is rejected and the error code is {code}, msg: {msg}.</p>
```

If no rendering is needed during waiting, just write await with then/catch directives on the same tag:

```qk
<p
    #await={pms}
    #then={res}
>
    pms is resolved with: {res}
</p>
```

<div class="custom-block tip">
    qingkuai's <a href="../components/async-components.html">async components</a> are also implemented using these three directives.
</div>

---

## html Directive

Sometimes we need to render text as HTML fragments, while regular interpolation only modifies textContent and escapes HTML. The html directive fulfills this need:

```qk
<div class="dynamic-html-content" #html>{htmlStr}</div>
```

While the above code works, the outer element isn't always necessary. To avoid meaningless elements, use `qk:spread` as a virtual mounting point:

```qk
<qk:spread #html>{htmlStr}</qk:spread>
```

We can also pass html directive a value specifying which tags to keep escaped, preventing [xss](https://en.wikipedia.org/wiki/Cross-site_scripting) attacks when handling untrusted HTML fragments. The html directive value type is:

```ts
type HTMLDirectiveValueType = Partial<{
    escapeTags: string[] // 需要保持转义的标签列表
    escapeStyle: boolean // 是否保持转义style标签
    escapeScript: boolean // 是否保持转义script标签
}>
```

We recommend this pattern for handling semi-trusted content:

```qk
<lang-js>
    const htmlDireciveConf = {
        escapeStyle: true,
        escapeScript: true,
        escapeTags: ["link", "iframe", "form"]
    }
</lang-js>

<p #html={htmlDireciveConf}>{htmlStr}</p>
```

<div class="custom-block warning">
    Tags using the html directive can only accept one text child node - otherwise a compiler fatal error occurs.
</div>

---

## target Directive

Some scenarios require manually controlling an element's parent, like full-screen modals. The target directive handles this easily, accepting either a CSS selector string or [HTMLElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement). Both examples below mount the div to the body:

```qk
<div
    class="page-modal"
    #target={"body"}
></div>
```

```qk
<div
    class="page-modal"
    #target={document.body}
></div>
```
