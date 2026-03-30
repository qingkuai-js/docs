# 样式表

样式表不仅用于美化页面，也常常是组件功能的重要补充。良好的样式体系不仅能提升用户体验，还能增强组件的表达力和复用性。在组件化开发中，传统全局样式容易引发冲突并增加维护成本，而作用域机制可以有效缓解这些问题。通过将样式限定在组件内部，开发者可以更放心地定义类名和规则，无需担心影响其他组件或页面元素。这种方式既保留了 CSS 的灵活性，也带来了更强的可控性与可维护性。

---

## 样式作用域

组件模板中的内容在渲染时都会被添加作用域属性，例如：

```html
<div>...</div>
```

会被渲染为类似这样的 HTML 片段：

```html
<div qk-dbb1016b>...</div>
```

为了避免组件样式污染全局或其他组件，嵌入样式同样会被添加作用域属性，例如：

```qk
<lang-css>
    div {
        color: red;
    }
</lang-css>
```

会被转换为：

```css
div[qk-dbb1016b] {
    color: red;
}
```

<div class="custom-block tip">
    在组件嵌入样式标签中，通过 <a href="https://developer.mozilla.org/zh-CN/docs/Web/CSS/@import">@import</a> 导入的样式表也会受到这一机制的影响。
</div>

---

## 作用域属性位置

通常情况下，作用域属性会被添加到最后一个选择器后：

```css
div p[qk-dbb1016b] {
}
.container .box[qk-dbb1016b] {
}
```

但我们可以通过 `qk-scope` 属性选择器手动调整作用域属性添加的位置，例如：

```css
div[qk-scope] p {
}
[qk-scope] .container .box {
}
```

会被转换为：

```css
div[qk-dbb1016b] p {
}
[qk-dbb1016b] .container .box {
}
```
