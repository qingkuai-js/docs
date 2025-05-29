# 简介

QingKuai 为自中文“轻快”的拼音，使用此名称是因为它具有 `轻` 量级、`快` 响应以及更加 `轻快` 的开发体验为核心的特点。它是一个用于构建 web 界面及交互功能的框架，它提供了一套变量响应化以及界面组件化的编程模型，并使用编译器将 qk 文件源码转换为极简、高效且严格优化的 js 代码。

在编写组件时，可以将脚本和样式写在嵌入语言标签中，脚本嵌入语言标签有 lang-js 和 lang-ts，样式嵌入语言标签有 lang-css，lang-scss，lang-less，lang-stylus 以及 lang-postcss，除此之外的任意位置都是书写 HTML 模板代码的地方。下面是一个最基本的使用示例：

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

需要注意的是 `script` 和 `style` 标签中的代码不会受编译器影响，它们的原始内容会被保留并插入页面（即与普通 HTML 中表现一致）：

```qk
<style>
    /* 向HTML中插入同样内容的style标签 */
</style>

<script>
    /* 向HTML中插入同样内容的script标签 */
</script>
```

---

## 设计理念

qingkuai 的设计理念是尽可能少造新语法，优先采用现有主流框架的语法习惯，只有在现有设计存在明显不合理之处时，才会做出必要的调整。这一理念旨在最大程度地降低学习成本，使开发者能够快速上手并专注于业务本身。

不使用 `script` 和 `style` 作为嵌入语言标签并不简单的是为了突出 qingkuai 的个性化，而是出于实际的兼容性考虑：当这些标签包含多个属性并被换行时，会导致基于 [Textmate](https://macromates.com) 的语法高亮功能失效：

<!-- prettier-ignore -->
```html
<style
    scoped
    lang="scss"
>
    /* 此处不能正确高亮scss */
</style>
```

<div class="custom-block tip">
    如果你使用过 <a href="https://cn.vuejs.org">vue</a> 或 <a href="https://svelte.dev">svelte</a>，会发现 qingkuai 的模板语法在许多方面与其相似，这是在设计时特意保留的，目的正是为了降低学习成本。但在语法相似的背后，qingkuai 的编译产物以及运行时实现却与其他框架是截然不同的。
</div>

---

## 为何选择 ​QingKuai？

与目前流行的前端框架相对比，qingkuai 具有一些核心优势：

-   编译体积：qingkuai 的运行时体积非常小（约 8~20 KB，gzip：5~11kb），并且支持高度的 [Tree-Shaking](https://developer.mozilla.org/zh-CN/docs/Glossary/Tree_shaking) 优化。同时，qingkuai 编译器对输出文件的体积也进行了深度优化，最终编译结果仅为其他框架的 20% 到 50%；

-   响应性：默认情况下，若某个顶部作用域的变量在模板中被访问，它就会被编译器标记为具有响应性，只有在你需要修改这一默认行为时才需手动处理，可结合下面这段代码进行理解：

    ```qk
    <lang-js>
        const person = {
            age: 30,
            name: "javascript"
        }

        // person改变时，h1元素会被更新
        setTimeout(()=>{
            person.age = 1
            persion.name= "QingKuai"
        }, 1000)
    </lang-js>

    <h1>My name is {person.name}, I'm {person.age} years old.</h1>
    ```

-   更新粒度：qingkuai 不使用 `虚拟DOM` ，响应性变量的变更会直接映射为原生 `DOM API` 调用，这种机制能够将更新粒度精细化至节点级，从而实现更高效的响应性更新，观察下面的代码：

    ```qk
    <lang-js>
        let count = 0
    </lang-js>

    <p>You clicked {count} times.</p>
    <button @click={count++}>Add Count</button>
    ```

    | 按钮被点击后，qingkuai 会异步调度页面更新，期间仅会调用一条类似如下的伪代码，而不会执行任何多余操作：

    ```js
    pElement.textContent = `You clicked ${count} times.`
    ```

    <div class="custom-block tip"><code>@click</code> 的作用是为button元素添加点击事件监听器，按钮点击后执行<code>count++</code>这条javascript表达式，关于事件监听器等更多模板语法我们会在后边详细介绍。</div>
