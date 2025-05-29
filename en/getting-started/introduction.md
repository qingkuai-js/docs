# Introduction

QingKuai is the pinyin for the Chinese word “轻快”. This name was chosen because it reflects the core characteristics of being `lightweight`, `fast` in response, and offering a more `easily` development experience. It is a framework for building web interfaces and interactive features, it provides a programming model with reactive variables and component-based UI, and uses a compiler to transform `.qk` source files into minimal, efficient, and strictly optimized JavaScript code.

When writing components, scripts and styles can be placed inside embedded language tags. Script language tags include lang-js and lang-ts, and style language tags include lang-css, lang-scss, lang-less, lang-stylus, and lang-postcss. All other areas are for writing HTML template code. Below is a basic usage example:

```qk
<lang-js>
    const name = "World"
</lang-js>

<h1>Hello {name}!</h1>

<lang-css>
    h1 {
        color: red;
    }
</lang-css>
```

It should be noted that the code within the `script` and `style` tags will not be affected by the compiler - their original content will be preserved and inserted into the page (i.e., behaving the same as in regular HTML):

```qk
<style>
    /* 向HTML中插入同样内容的style标签 */
</style>

<script>
    /* 向HTML中插入同样内容的script标签 */
</script>
```

---

## Design Philosophy

qingkuai's design philosophy is to minimize the creation of new syntax and prioritize adopting the syntactic conventions of existing mainstream frameworks. Adjustments are only made when current designs exhibit clear unreasonable aspects. This approach aims to significantly reduce the learning curve, enabling developers to quickly get started and focus on the business logic itself.

The decision to avoid using `script` and `style` tags as embedded language tags isn't merely for qingkuai's distinctive branding, but stems from practical compatibility considerations: when these tags contain multiple attributes and line breaks, they cause syntax highlighting failures in [Textmate](https://macromates.com)-based systems:

<!-- prettier-ignore -->
```html
<style
    scoped
    lang="scss"
>
    /* scss cannot be highlighted correctly here */
</style>
```

<div class="custom-block tip">
    If you have used <a href="https://vuejs.org">Vue</a> or <a href="https://svelte.dev">Svelte</a>, you'll find that qingkuai's template syntax is similar to them in many aspects — this was intentionally preserved during design to reduce the learning curve. However, behind this syntactic similarity, qingkuai's compilation output and runtime implementation are fundamentally different from other frameworks.
</div>

---

## Why Choose QingKuai?

Compared with currently popular front-end frameworks, qingkuai has several core advantages:

-   Bundle size: qingkuai's runtime is extremely lightweight (approximately 8~20 KB, gzip: 5~11KB) and supports advanced [Tree-Shaking](https://developer.mozilla.org/zh-CN/docs/Glossary/Tree_shaking) optimization. Meanwhile, the qingkuai compiler performs deep optimization of output file size, with final compilation results being just 20% to 50% of other frameworks';

-   Reactivity: By default, if a top-level scoped variable is accessed in the template, it will be marked as reactive by the compiler. You only need to manually intervene when you want to modify this default behavior, as demonstrated in the following code example:

    ```qk
    <lang-js>
        const person = {
            age: 30,
            name: "javascript"
        }

        // When person changes, the h1 element will be updated
        setTimeout(()=>{
            person.age = 1
            persion.name= "QingKuai"
        }, 1000)
    </lang-js>

    <h1>My name is {person.name}, I'm {person.age} years old.</h1>
    ```

-   Update granularity: qingkuai doesn't use a `Virtual DOM`. Changes to reactive variables are directly mapped to native `DOM API` calls. This mechanism enables node-level update granularity, achieving more efficient reactivity updates. Observe the following code:

    ```qk
    <lang-js>
        let count = 0
    </lang-js>

    <p>You clicked {count} times.</p>
    <button @click={count++}>Add Count</button>
    ```

    | After the button is clicked, qingkuai asynchronously schedules page updates. During this process, it only executes a single piece of pseudocode similar to the following, without performing any redundant operations:

    ```js
    pElement.textContent = `You clicked ${count} times.`
    ```

    <div class="custom-block tip"><code>@click</code> serves to add a click event listener to the button element. When the button is clicked, it executes the JavaScript expression <code>count++</code>. We'll provide more detailed explanations about event listeners and other template syntax later.</div>
