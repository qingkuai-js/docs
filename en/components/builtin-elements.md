# Built-in Elements

In qingkuai, built-in elements extend template syntax to provide more expressive power than standard HTML. They typically serve framework-level special purposes with specific semantics and behaviors, playing crucial roles in handling rendering logic or control structures. Built-in elements are prefixed with `qk:` to avoid conflicts with future HTML elements or occupying component namespaces.

---

## Spread

We've used the `qk:spread` built-in element multiple times in previous examples. Its primary role is serving as a virtual mounting point for directives - all its child elements are affected by directives mounted on it. Most importantly, it doesn't render as an actual HTML element, thus avoiding interference with the final page structure.

For example, when we want to create multiple p + button elements in a loop without introducing meaningless parent elements:

```qk
<qk:spread #for={3}>
    <p>...</p>
    <button>Click Me</button>
</qk:spread>
```

Or when conditionally determining whether to show multiple li elements:

```qk
<ul class="list">
    <li>normal list 1</li>
    <li>normal list 1</li>
    <qk:spread #show={visible}>
        <li>extra list 3</li>
        <li>extra list 4</li>
    </qk:spread>
</ul>
```

When slot content contains multiple parallel elements:

```qk
<Component>
    <qk:spread slot="default">
        <p>some</p>
        <p>contents</p>
    </qk:spread>
</Component>
```

Adding directives to text nodes:

```qk
<qk:spread
    #await={pms}
    #then={target}
>
    {target} is loaded.
</qk:spread>
```

<div class="custom-block tip">
    A common pattern emerges: <code>qk:spread</code> typically applies directives uniformly to multiple independent parallel elements without a common parent, which aligns with the "spread" meaning in its name - dispersed and expanded.
</div>
