# Event Handling

In frontend development, user interactions with pages are often event-driven. qingkuai provides concise and intuitive event binding syntax, allowing you to easily add click, input, keyboard and other event handling logic to elements, helping achieve responsive user interaction experiences.

In qingkuai, attributes starting with `@` are used to bind event handlers, for example `@click` listens for click events. This syntax is similar to HTML's `onclick` attribute but more powerful, supporting interpolation blocks to write arbitrary expressions as event handling logic - not only more concise but also more expressive.

---

## Binding Events

This code binds a click event to a button element. When clicked, the `handleAddCount` method will be called:

```qk
<lang-js>
    let count = 0

    function handleAddCount(){
        // When modifying reactive variables,
        // the content of p element will be automatically updated
        count++
    }
</lang-js>

<p>current count: {count}</p>
<button @click={handleAddCount}>Add Count</button>
```

Functions like `handleAddCount` are called event handlers. They can declare parameters to receive event objects, consistent with native `addEventListener` behavior:

|js|ts|

```js
function handleAddCount(e) {
    count++

    // true, both point to the clicked button element
    console.log(e.target === this)
}
```

```ts
function handleAddCount(this: HTMLButtonElement, e: MouseEvent) {
    count++

    // true, both point to the clicked button element
    console.log(e.target === this)
}
```

Like dynamic attributes, when event names match variable names the interpolation block can be omitted, making these two syntaxes equivalent:

```qk
<button @click></button>
<button @click={click}></button>
```

<div class="custom-block warning">
    This syntax isn't supported if the event name is a keyword or reserved word in the embedded scripting language, such as <code>class</code> or <code>for</code> attributes.
</div>

---

## Inline Event Handlers

In the above example, the event handler only contains one line of code, so declaring a separate method seems unnecessary. We have two ways to simplify:

1. Use arrow functions directly in interpolation blocks:

    ```qk
    <button @click={() => count++}>Add Count</button>
    ```

2. Use inline event handler syntax - writing JS/TS expressions directly in interpolation blocks:

    ```qk
    <button @click={count++}>Add Count</button>
    ```

The second approach's inline handlers are compiled to code like:

```qk
<button @click={$args => count++}>Add Count</button>
```

| So we can also access native event objects via `$args` in inline handlers:

```qk
<button @click={$args => console.log($args.target)}>Add Count</button>
```

<div class="custom-block tip">
    From a native event perspective, naming <code>$args</code> as <code>$event</code> might seem more logical. But for semantic consistency, $args better covers arbitrary parameters passed in <a href="../components/basic.html">component</a> inline handlers we'll introduce later. Thus qingkuai uniformly uses $args as the default parameter name to reflect its versatility - representing both native events and arbitrary component parameters.
</div>

If you call other methods in inline handlers, qingkuai automatically binds their `this` to the current element:

|js|ts|

```qk
<lang-js>
    let count = 0

    function handleAddCount(e) {
        count++

        // true, both point to the clicked button element
        console.log(e.target === this)
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

        // true, both point to the clicked button element
        console.log(e.target === this)
    }
</lang-ts>

<p>current count: {count}</p>
<button @click={handleAddCount($args)}>Add Count</button>
```

<div class="custom-block tip">
    If using <a href="https://www.typescriptlang.org">Typescript</a>, <code>$args</code> is strictly typed - e.g. <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/KeyboardEvent">KeyboardEvent</a> for <code>@keydown</code>, <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent">MouseEvent</a> for <code>@click</code>, etc.
</div>

---

You may wonder: when is a handler inline vs regular? From qingkuai's compiler perspective:

-   Inline handlers are interpolation expressions needing function wrapping
-   When the expression is a single identifier, property access, function declaration or arrow function, it's considered complete and treated as a regular handler:

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

## Event Handler Modifiers

qingkuai allows appending modifiers after event names to simplify common interaction patterns. Currently supported modifiers:

1. Functional modifiers:

    - self: Only triggers if `event.target` is the element itself (doesn't block propagation);
    - stop: Calls [stopPropagation](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopPropagation) after handler;
    - prevent: Calls [preventDefault](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault) after handler;
    - compose: Triggers during IME composition (e.g. Chinese input candidate selection);
    - once: Removes handler after first trigger (like [addEventListener options.once](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#options));
    - capture: Triggers during capture phase (like [options.capture](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#options));
    - passive: Indicates handler will never call `preventDefault` (mobile performance optimization);

2. Key modifiers (only trigger if specified keys are pressed, for keyboard events):

    - Basic keys: enter, tab, del, esc, up, down, left, right, space;
    - System keys: meta, alt, ctrl, shift;

3. Other modifiers:
    - compose: Triggers during IME composition (only for @input);

Example modifier usage:

```qk
<!--
    The event handler only executes when clicking the
    "Click" portion, and triggers just once
-->
<button @click|self|once={console.log("ok")}>
    Click <span>Me</span>
</button>


<!--
    The event handler only triggers when both alt and
    shift keys are held down while clicking the button
-->
<button @click|alt|shift={console.log("ok")}> Click Me </button>
```
