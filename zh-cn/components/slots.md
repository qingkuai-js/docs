# 插槽

组件插槽用于向组件传递一段结构化的 UI 内容（即模板片段），它和属性（attributes）最大的区别在于传递的内容类型不同：属性用于传递数据，而插槽则用于传递界面结构。通过插槽，父组件可以将自定义的 DOM 内容插入到子组件的指定位置，从而实现更高的灵活性和复用性。这使得插槽成为构建布局容器、弹窗、列表渲染等通用组件时不可或缺的机制。

<img src="https://qingkuai-js.oss-cn-beijing.aliyuncs.com/docs/slots.png" />

---

## 基本用法

在组件内部使用 `slot` 标签声明插槽位置，它会被使用组件时传入的子元素替换：

```qk
<!-- Outter.qk -->
<Inner>...</Inner>

<!-- Inner.qk -->
<div class="inner-box">
    <slot></slot>
</div>
```

这会被渲染为：

```html
<div class="inner-box">...</div>
```

插槽模板不止局限于文本内容，还可以元素、组件等任意合法的模板内容：

```qk
<Inner>
    <FontIcon name="tip" />
    <p #for={3}>...</p>
</Inner>
```

---

## 作用域

插槽模板中可以访问其所处组件的数据：

```qk
<!-- Inner组件代码同上 -->
<lang-js>
    const langs = ["js", "ts", "qk"]
</lang-js>

<Inner>
    <p #for={item, index of langs}>
        {index + 1}/{langs.length}: {item}
    </p>
</Inner>
```

这会被渲染为：

```html
<div class="inner-box">
    <p>1/3: js</p>
    <p>2/3: ts</p>
    <p>3/3: qk</p>
</div>
```

---

## 默认内容

slot 标签内的子元素会被认为插槽的默认内容，如果外部使用组件时未传入插槽内容，那么默认内容会被渲染：

```qk
<!-- Outter.qk -->
<Inner />

<!-- Inner.qk -->
<div class="inner-box">
    <slot>Default content</slot>
</div>
```

这会被渲染为：

```html
<div class="inner-box">Default content</div>
```

---

## 指定插槽名称

很多时候组件可能并不只有一个插槽，当一个组件拥有多个插槽时，我们需要通过 `name` 属性为它们指定名称以进行区分：

```qk
<!-- Article.qk -->
<article>
    <slot></slot>
</article>
<footer>
    <slot name="footer"></slot>
</footer>
```

<div class="custom-block tip">
    未添加 <code>name</code> 属性的插槽名称默认为 <code>default</code>。
</div>

在使用组件时，通过 `slot` 属性指定插槽名称：

```qk
<Atricle>
    <!-- slot属性值为default时可省略 -->
    <div slot="default">Article contents...</div>
    <p slot="footer">Copyright informations...</p>
</Article>
```

当插槽内容仅为一段文本或想要避免添加额外的无意义标签时可以使用 `qk:spread` 作为虚拟父元素：

```qk
<Article>
    <qk:spread>Article contents...</qk:spread>
    <qk:spread slot="footer">
        <p>Release informations...</p>
        <p>Copyright informations...</p>
    </qk:spread>
</Article>
```

---

## 传递上下文

我们在本节的[作用域](#作用域)部分介绍了插槽内容默认只能访问其所在组件的作用域数据，但在实际开发中，我们常常需要访问子组件中插槽定义侧的数据。为此，可以通过在 `slot` 标签上添加属性的方式（这些属性会被组合成一个对象上下文），将所需的上下文暴露出来：

```qk
<!-- Article.qk -->
<article>
    <slot
        !time={article.time}
        !title={article.title}
    ></slot>
</article>
```

接收侧可以通过 `#slot` 指令为这个对象指定一个标识符名称并使用它：

```qk
<Article>
    <qk:spread #slot={articleInfo}>
        <h1>{articleInfo.title}</h1>
        <p>Published in {articleInfo.time}</p>
    </qk:spread>
</Article>
```

`#slot` 指令支持解构语法：

```qk
<Article>
    <qk:spread
        #slot={
            {
                title,
                time: publishedTime
            }
        }
    >
        <h1>{title}</h1>
        <p>Published in {publishedTime}</p>
    </qk:spread>
</Article>
```
