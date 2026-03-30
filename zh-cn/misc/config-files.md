# 配置文件

在使用 Qingkuai 构建应用时，你通常无需进行复杂的配置即可获得开箱即用的开发体验。不过在实际项目中，为了适应不同的开发需求或行为定制，Qingkuai 提供了灵活的配置文件机制，帮助你更精细地控制其工作方式。统一的配置机制不仅提升了项目的一致性，也让多人协作更加顺畅，是构建可维护应用的重要基础。

---

## 运行配置

Qingkuai 的运行配置通过 `.qingkuairc` 文件修改，组件文件会被当前目录或其最近的上级目录中的运行配置文件影响。例如下面的目录结构中 Hello 组件文件会被自身目录中的配置文件影响，而 App 组件则会被项目根目录中的配置文件影响：

```txt
qingkuai-app
├── src
│   ├── Components
│   │   ├── Hello.qk
│   │   └── .qingkuairc
│   └── App.qk
└── .qingkuairc
```

### reactivityMode

该属性用于配置 Qingkuai 默认使用的响应性构造器，字符串，可选值：reactive、shallow，默认值为 reactive：

- reactive：使用深层响应性，嵌套的对象和数组也会被自动追踪；
- shallow：使用浅层响应性，仅追踪顶层值的变化，嵌套值不会被自动追踪。

### whitespace

该属性用于配置模板中空白字符的处理方式，字符串，可选值：preserve、trim、collapse、trim-collapse，默认值为 trim-collapse：

- preserve：保留模板中所有空白字符，不做任何处理；
- trim：裁剪元素边界处多余的空白字符；
- collapse：将连续的空白字符合并为单个空格；
- trim-collapse：同时应用 trim 和 collapse 规则（默认行为）。

### preserveHtmlComments

该属性用于配置是否保留 HTML 注释节点，字符串，可选值：never、always、development、production，默认值为 development。

### resolveImportExtension

该属性用于配置在组件文件的导入语句中是否可省略 `.qk` 扩展名，布尔值，默认值为 true：

```js
// 被解析为 ./Component.qk
import Component from "./Component"
```

### shorthandDerivedDeclaration

该属性用于配置是否启用衍生响应式状态的简写声明，布尔值，默认值为 true。启用后，组件文件嵌入脚本顶层作用域中以 `$` 字符开头的标识符会被自动编译为[衍生响应式状态](../basic/reactivity.html#衍生响应式状态)，修改为 false 可阻止这一行为：

```js
// 简写声明衍生响应式状态
const $double = number * 2
```

### interpretiveComments

该属性用于配置是否在编译结果中插入解释性注释，布尔值，默认值为 true

---

## 格式化配置

Qingkuai 语言服务的格式化功能基于 [prettier-plugin-qingkuai](https://www.npmjs.com/package/prettier-plugin-qingkuai) 实现，这是一个 Prettier 插件，组件文件的格式化遵循常规的 [Prettier 配置](https://prettier.io/docs/options)。其中有一些仅对组件文件生效的附加配置选项，它们需要写在 Prettier 配置文件的 `qingkuai` 对象下，例如：

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

该属性用于配置组件标签的风格偏好，字符串，可选值为 camel、kebab，默认值为 camel，修改该属性会影响 Qingkuai 语言服务器给出的补全建议中组件标签的格式。

### componentAttributeFormatPreference

该属性用于配置组件属性的风格偏好，字符串，可选值为 camel、kebab，默认值为 camel，修改该属性会影响 Qingkuai 语言服务器给出的补全建议中组件属性的格式。
