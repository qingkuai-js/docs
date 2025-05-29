# 配置文件

在使用 qingkuai 构建应用时，你通常无需进行复杂的配置即可获得开箱即用的开发体验。不过在实际项目中，为了适应不同的开发需求或行为定制，qingkuai 提供了灵活的配置文件机制，帮助你更精细地它的的工作方式。统一的配置机制不仅提升了项目的一致性，也让多人协作更加顺畅，是构建可维护应用的重要基础。

---

## 运行配置

qingkuai 的运行配置通过 `.qingkuairc` 文件修改 ，组件文件会被当前目录或其最近的上级目录中的运行配置文件影响。例如下面的目录结构中 Hello 组件文件会被自身目录中的配置文件影响，而 App 组件则会被项目根目录中的配置文件影响：

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

该属性用于配置是否在编译结果中插入提示性注释，布尔值，默认值为 true，例如下面这段编译结果中的注释都是提示性注释：

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

该属性用于配置是否在组件实例上暴露卸载组件的方法，布尔值，其默认值在开发环境下为 true，生产环境下为 false；

### exposeDependencies

该属性用于配置是否在组件实例上暴露依赖的响应性数据，布尔值，其默认值在开发环境下为 true，生产环境下为 false；

### reserveHtmlComments

该属性用于配置是否保留 HTML 注释节点，字符串，可选值：never、all、development、production，默认值为 development。

### resolveImportExtension

该属性用于配置在组件文件的导入语句中是否可省略 `.qk` 扩展名，布尔值，默认值为 true：

```js
// 被解析为 ./Component.qk
import Component from "./Component"
```

### convenientDerivedDeclaration

该属性用于配置是否启用衍生响应性状态便捷声明，布尔值，默认为 true，组件文件嵌入脚本的顶层作用于中声明的以 `$` 字符开头的标识符会被编译为[衍生响应性状态](../basic/reactivity.html#衍生响应性状态)，修改为 false 可阻止这一行为：

```js
// 便捷声明衍生响应性状态
const $double = number * 2
```

---

## 格式化配置

qingkuai 语言服务的格式化功能是基于 [prettier-plugin-qingkuai](https://www.npmjs.com/package/prettier-plugin-qingkuai) 实现的，它是一个 prettier 插件，所以组件文件的格式化遵循常规的 [prettier 配置](https://prettier.io/docs/options)。除了常规配置外，还有一些仅对组件文件生效的附加配置选项，这些附加配置项都应作为 `qingkuai` 的属性（qingkuai 是 prettier 常规配置中的一个属性），例如对于 `.prettierrc` 文件：

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

该属性用于配置是否在插值块开始和结束处插入空格，布尔值，默认值为 false，改为 true 时整理风格如下：

```qk
<div #for={ item, index of 3 }>{ index }: { item }</div>
```

### componentTagFormatPreference

该属性用于配置组件标签的风格偏好，字符串，可选值为 camel、kebab，默认值为 camel，修改该属性会影响 qingkuai 语言服务器给出的补全建议中组件标签的格式。

### componentAttributeFormatPreference

该属性用于配置组件属性的风格偏好，字符串，可选值为 camel、kebab，默认值为 camel，修改该属性会影响 qingkuai 语言服务器给出的补全建议中组件属性的格式。
