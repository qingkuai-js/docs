# Slots

Component slots are used to pass structured UI content (template fragments) to components. Their key difference from attributes lies in the content type they convey: attributes pass data while slots pass interface structures. Through slots, parent components can insert custom DOM content into specified locations of child components, enabling greater flexibility and reusability. This makes slots an essential mechanism for building layout containers, modals, list renderings and other generic components.

<img src="/static/medias/slots-en.png" />

---

## Basic Usage

Declare slot positions inside components using the `slot` tag, which gets replaced by child elements passed when using the component:

```qk
<!-- Outter.qk -->
<Inner>...</Inner>

<!-- Inner.qk -->
<div class="inner-box">
    <slot></slot>
</div>
```

This renders as:

```html
<div class="inner-box">...</div>
```

Slot templates aren't limited to text - they can contain elements, components, or any valid template content:

```qk
<Inner>
    <FontIcon name="tip" />
    <p #for={3}>...</p>
</Inner>
```

---

## Scope

Slot templates can access data from their component's scope:

```qk
<!-- The code of the Inner component is the same as above -->
<lang-js>
    const langs = ["js", "ts", "qk"]
</lang-js>

<Inner>
    <p #for={item, index of langs}>
        {index + 1}/{langs.length}: {item}
    </p>
</Inner>
```

This renders as:

```html
<div class="inner-box">
    <p>1/3: js</p>
    <p>2/3: ts</p>
    <p>3/3: qk</p>
</div>
```

---

## Default Content

Child elements inside slot tags serve as default content, rendered when no slot content is provided:

```qk
<!-- Outter.qk -->
<Inner />

<!-- Inner.qk -->
<div class="inner-box">
    <slot>Default content</slot>
</div>
```

This renders as:

```html
<div class="inner-box">Default content</div>
```

---

## Named Slots

Components often require multiple slots. When a component has several slots, we distinguish them using the `name` attribute:

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
    Slots without <code>name</code> attributes default to <code>default</code>.
</div>

When using components, specify slot names via the `slot` attribute:

```qk
<Atricle>
    <!-- Can be omitted when slot attribute value is "default" -->
    <div slot="default">Article contents...</div>
    <p slot="footer">Copyright informations...</p>
</Article>
```

For text-only slot content or to avoid extra meaningless tags, use `qk:spread` as a virtual parent element:

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

## Context Passing

While our [Scope](#scope) section explained slot content normally only accesses its component's scope data, practical development often requires accessing data from the child component's slot definition side. We expose needed context by adding attributes to the `slot` tag (these get combined into a context object):

```qk
<!-- Article.qk -->
<article>
    <slot
        !time={article.time}
        !title={article.title}
    ></slot>
</article>
```

he receiving side can use the `#slot` directive to name and utilize this object:

```qk
<Article>
    <qk:spread #slot={articleInfo}>
        <h1>{articleInfo.title}</h1>
        <p>Published in {articleInfo.time}</p>
    </qk:spread>
</Article>
```

The `#slot` directive supports destructuring syntax:

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
