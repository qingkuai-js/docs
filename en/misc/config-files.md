# Configuration Files

When building applications with qingkuai, you typically don't need complex configurations to get an out-of-the-box development experience. However, in real-world projects, to accommodate different development needs or customize behaviors, qingkuai provides a flexible configuration file mechanism that helps you control its behavior more precisely. A unified configuration system not only improves project consistency but also facilitates smoother collaboration among teams, forming an important foundation for building maintainable applications.

---

## Runtime Configuration

Qingkuai's runtime configuration is modified through the `.qingkuairc` file. Component files are affected by the configuration file in the current directory or the nearest parent directory. For example, in the following directory structure, the Hello component file will be influenced by the configuration file within its own directory, while the App component will be affected by the configuration file at the project root:

```txt
qingkuai-app
├── src
│   ├── Components
│   │   ├── Hello.qk
│   │   └── .qingkuairc
│   └── App.qk
└── .qingkuairc
```

### insertTipComments

This property configures whether to insert tip comments into the compilation output. It is a boolean value and defaults to `true`. For instance, the comments in the following compiled output are all tip comments:

```js
scts([
    [
        "h1",
        _ => {
            return "" + "Hello " + __w__target.$ + "!"
        }
    ],
    [
        "label",
        "",
        NIL,
        NIL,
        [/* cache id */ 0, "" + "Say hello to:"],
        [
            /* input */ __s0__,
            "",
            ["spellcheck", "false"],
            [
                ...withReference(
                    /* input */ __s0__,
                    "value",
                    _ => {
                        return __w__target.$
                    },
                    v => (__w__target.$ = v)
                )
            ]
        ]
    ]
])
```

### exposeDestructions

This property configures whether to expose component destruction methods on the component instance. It is a boolean value. The default value is `true` in development mode and `false` in production mode.

### exposeDependencies

This property configures whether to expose reactive dependency data on the component instance. It is a boolean value. The default value is `true` in development mode and `false` in production mode.

### reserveHtmlComments

This property configures whether to preserve HTML comment nodes. It accepts a string with possible values: `never`, `all`, `development`, `production`. The default value is `development`.

### resolveImportExtension

This property configures whether the `.qk` extension can be omitted in import statements within component files. It is a boolean value, and the default is `true`:

```js
// Resolved as ./Component.qk
import Component from "./Component"
```

### convenientDerivedDeclaration

该属性用于配置是否启用衍生响应性状态便捷声明，布尔值，默认为 true，组件文件嵌入脚本的顶层作用于中声明的以 `$` 字符开头的标识符会被编译为[衍生响应性状态](../basic/reactivity.html#derived-reactive-state)，修改为 false 可阻止这一行为：

```js
// Conveniently declare derived reactive state
const $double = number * 2
```

---

## Formatting Configuration

The formatting functionality of the qingkuai language service is built upon [prettier-plugin-qingkuai](https://www.npmjs.com/package/prettier-plugin-qingkuai), which is a Prettier plugin. Therefore, formatting for component files follows the standard [Prettier configuration](https://prettier.io/docs/en/options). In addition to the standard configuration options, there are some additional ones that only apply to component files. These extra options should be placed under the `qingkuai` property (which is a property inside the standard Prettier configuration). For example, in a `.prettierrc` file:

```json
{
    "tabWidth": 4,
    "printWidth": 80,
    "qingkuai": {
        "spaceAroundInterpolation": true
    }
}
```

### spaceAroundInterpolation

This property configures whether to insert spaces around interpolation blocks. It is a boolean value and defaults to `false`. When set to `true`, the formatting style changes as follows:

```qk
<div #for={ item, index of 3 }>{ index }: { item }</div>
```

### componentTagFormatPreference

This property configures the preferred format style for component tags. It accepts a string with possible values: `camel`, `kebab`. The default value is `camel`. Changing this affects the format of component tag suggestions provided by the qingkuai language server.

### componentAttributeFormatPreference

This property configures the preferred format style for component attributes. It accepts a string with possible values: `camel`, `kebab`. The default value is `camel`. Changing this affects the format of component attribute suggestions provided by the qingkuai language server.
