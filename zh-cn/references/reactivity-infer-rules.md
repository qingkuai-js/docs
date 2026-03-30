# 响应性推导规则

在 Qingkuai 中，编译器会根据一套推导规则自动决定各个标识符的响应性类型。了解这些规则有助于开发者更好地管理状态，并在必要时通过显式标记覆盖默认行为。

---

## 推导流程

编译器会按以下顺序对脚本块顶层作用域中的每个标识符进行响应性推导：

1. **检查显式标记**：若变量声明在初始值中调用了 `reactive`、`shallow` 或 `raw`，则优先按显式标记推导；
2. **解析隐式规则**：若未使用显式标记，则根据标识符在模板中的使用情况及其声明形式进行隐式推导。

---

## 显式标记

响应性标记内建方法必须在变量声明的初始值部分调用。当标识符使用上述内建方法进行显式标记时，编译器优先将其推导为对应的响应性类型：

```qk
<lang-ts>
    const config = raw([1, 2, 3])               // 原始值
    const list = shallow({ debug: false })      // 浅层响应式
    const user = reactive({ name: "Qingkuai" }) // 深度响应式
</lang-ts>
```

### 退化行为

即便使用了显式标记，当声明同时满足以下两个条件时，标识符会**退化为原始值**，显式标记将被编译器忽略：

- 使用 `const` 声明
- 初始值为字面量类型（如数字字面量、字符串字面量等）

```qk
<lang-ts>
    const a = shallow(1)    // 退化为原始值，shallow 被忽略
    const b = reactive("")  // 退化为原始值，reactive 被忽略
    const c = reactive({})  // 正常推导，根据后续使用情况确定其响应性类型
</lang-ts>
```

未发生退化时，标识符推导为显式标记所指定的响应性类型。

---

## 别名与衍生值

- 别名绑定只能通过 `alias` 内建方法显式创建。
- 衍生响应式值只能通过 `derived`、`derivedExp` 或其简写声明进行显式标记与创建。

这些规则独立于 `reactive`、`shallow`、`raw` 的显式标记流程：

```qk
<lang-ts>
    const firstName = reactive("Qing")
    const lastName = reactive("kuai")

    const userName = alias(props.userInfo.name)

    const fullName = derived(() => firstName + " " + lastName)
    const shortName = derivedExp(firstName + "-" + lastName)

</lang-ts>
```

---

## 隐式推导

当标识符未使用任何显式标记时，编译器按以下规则进行隐式推导。

### 未在模板中使用

当标识符未在模板中被访问时，编译器将其推导为原始值。此类标识符仅存在于脚本逻辑中，不会参与依赖收集与更新流程。

```qk
<lang-ts>
    let count = 0     // 从未在模板中使用 → 原始值
    let message = ""  // 从未在模板中使用 → 原始值
</lang-ts>

<p> 这里未访问 count 或 message </p>
```

### 在模板中使用

当标识符在模板中被访问时，对于以 `let` 或 `var` 声明、且初始值为字面量类型的标识符，编译器还会检查其在脚本中是否存在被修改的情况：

- **未被修改**：推导为原始值，避免不必要的依赖收集与更新开销
- **存在修改**：推导为当前响应性模式对应的响应性类型

```qk
<lang-ts>
    let a = 1    // 在模板中使用，但从未被修改 → 原始值
    let b = 0    // 在模板中使用，且存在修改 → 当前响应性模式对应的类型
    function increment() {
        b++
    }
</lang-ts>

<p>{ a }</p>
<button @click={increment}>{ b }</button>
```

### 其他声明形式

对于 `class` 声明、`function` 声明、TypeScript `enum` 声明等非变量声明，编译器在隐式推导阶段将其视作可变声明处理。

- 这类声明不能使用 `reactive`、`shallow`、`raw` 进行显式标记。
- 若在模板中使用，则按当前响应性模式参与推导；若未使用，则按原始值处理。

---

## 推导提示

如果安装了 Qingkuai [VS Code 扩展](../misc/language-features.html#ide-扩展)，将鼠标指针悬停在顶层作用域的标识符上时，语言服务器会在提示中显示编译器推导出的响应性类型：

<img src="/static/medias/inferred-raw.png" alt="vscode-hover" style="width:60%; margin-left:20%;" />
<img src="/static/medias/inferred-reactive.png" alt="vscode-hover" style="width:60%; margin-left:20%;" />
<img src="/static/medias/inferred-alias.png" alt="vscode-hover" style="width:60%; margin-left:20%;" />
