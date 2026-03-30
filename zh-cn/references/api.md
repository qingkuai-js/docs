# API 参考

Qingkuai 的 API 按入口包划分，便于按需引入并保持清晰的职责边界。本文基于源码入口文件整理两类公开 API：运行时包`qingkuai`与编译器包`qingkuai/compiler`。

<div class="custom-block tip">
    内部包 <code>qingkuai/internal</code> 主要面向框架内部实现，通常不建议业务代码直接依赖，因此本节不展开说明。
</div>

---

## 运行时

运行时包导出组件生命周期、响应式副作用、性能优化及状态转换等 API。

### 类型导出

- `HtmlBlockOptions`

### 生命周期

- `onBeforeMount`
- `onAfterMount`
- `onBeforeUpdate`
- `onAfterUpdate`
- `onBeforeDestroy`
- `onAfterDestroy`

参考：[生命周期](../components/lifecycle.html)

### 副作用与监视器

- `watch`
- `effect`
- `preEffect`
- `postEffect`
- `syncEffect`
- `preWatch`
- `postWatch`
- `syncWatch`

参考：[监视器与副作用](../basic/watchers-and-side-effects.html)

### 响应式优化控制

- `noTracking`
- `noUpdating`
- `pauseTracking`
- `pauseUpdating`
- `resumeTracking`
- `resumeUpdating`
- `batchUpdating`
- `stopBatchUpdating`
- `startBatchUpdating`
- `batchAndNoTracking`

### 状态与调度

- `mountApp`
- `nextTick`
- `toRaw`
- `createStore`
- `toReactive`
- `toShallowReactive`

### 其他导出

- `DESTRUCT_HTML`

---

## 编译器（qingkuai/compiler）

编译器包用于解析与编译组件源码，主要被构建工具、语言服务和插件生态调用。

### 类型导出

- `ASTLocation`
- `ASTPosition`
- `TemplateNode`
- `CompileOptions`
- `CompileResult`
- `StyleDescriptor`
- `TextContentPart`
- `ScriptDescriptor`
- `IdentifierStatus`
- `TemplateAttribute`
- `ASTPositionWithFlag`
- `TemplateNodeContext`
- `CompileIntermediateOptions`
- `CompileIntermediateResult`

### 常量

- `SPREAD_TAG`
- `PRESERVED_IDPREFIX`
- `LANGUAGE_SERVICE_UTIL`
- `GET_TYPE_DELAY_MARKING`

### 工具函数

- `camel2Kebab`
- `kebab2Camel`
- `toPropertyKey`
- `findEndBracket`
- `findOutOfComment`
- `findOutOfLiteral`
- `findOutOfLiteralComment`
- `isSelfClosingTag`
- `isEmbeddedLanguageTag`
- `isRequiredValueDirective`

### 方法与标志

- `PositionFlag`
- `isCompileError`
- `isCompileWarning`
- `parseComponentTag`
- `parseDirectiveValue`
- `parseEventFlag`
- `parseTemplate`
- `compile`
- `compileIntermediate`
