# API 参考

Qingkuai 的 API 按入口包划分，便于按需引入并保持清晰的职责边界。本文基于源码入口文件整理两类公开 API：运行时包 `qingkuai` 与编译器包 `qingkuai/compiler` 。

<div class="custom-block tip">
    内部包 <code>qingkuai/internal</code> 主要面向框架内部实现，通常不建议业务代码直接依赖，因此本节不展开说明。
</div>

---

## 运行时包

运行时包导出组件生命周期、响应式副作用、性能优化及状态转换等 API。

### 类型导出

- `ComponentInstance`
- `EffectCallback`
- `EffectFunc`
- `EffectHandle`
- `HtmlBlockOptions`
- `QingkuaiComponent`
- `WatcherCallback`
- `WatchFunc`

### 生命周期

- `onAfterDestroy`
- `onAfterMount`
- `onAfterUpdate`
- `onBeforeDestroy`
- `onBeforeUpdate`

参考：[生命周期](../components/lifecycle.md)

### 副作用与监视器

- `effect`
- `postEffect`
- `postWatch`
- `preEffect`
- `preWatch`
- `syncEffect`
- `syncWatch`
- `watch`

参考：[监视器与副作用](../basic/watchers-and-side-effects.md)

### 响应式优化控制

- `batchAndNoTracking`
- `batchUpdating`
- `noTracking`
- `noUpdating`
- `pauseTracking`
- `pauseUpdating`
- `resumeTracking`
- `resumeUpdating`
- `startBatchUpdating`
- `stopBatchUpdating`

### 状态与调度

- `createShallowStore`
- `createStore`
- `getCurrentInstance`
- `mountApp`
- `nextTick`
- `toRaw`
- `toReactive`
- `toShallowReactive`

### 其他导出

- `DESTRUCT_HTML`
- `version`

---

## 编译器包

编译器包用于解析与编译组件源码，主要被构建工具、语言服务和插件生态调用。

### 类型导出

- `ASTLocation`
- `ASTPosition`
- `ASTPositionWithFlag`
- `CompileIntermediateOptions`
- `CompileIntermediateResult`
- `CompileOptions`
- `CompileResult`
- `IdentifierStatus`
- `ScriptDescriptor`
- `StyleDescriptor`
- `TemplateAttribute`
- `TemplateNode`
- `TemplateNodeContext`
- `TextContentPart`

### 常量对象

编译器包导出的 `constants` 对象包含以下属性：

- `LSC`
- `PRESERVED_IDPREFIX`
- `SPREAD_TAG`

### 工具对象

编译器包导出的 `util` 对象包含以下属性：

- `camel2Kebab`
- `findEndBracket`
- `findOutOfComment`
- `findOutOfLiteral`
- `findOutOfLiteralComment`
- `formatSourceCode`
- `isEmbeddedLanguageTag`
- `isEmbeddedStyleTag`
- `isRequiredValueDirective`
- `isVoidTag`
- `kebab2Camel`
- `toPropertyKey`
- `ts`

### 标志

- `PositionFlag`
- `TestingMode`

### 方法

- `compile`
- `compileIntermediate`
- `isCompileError`
- `isCompileWarning`
- `parseDirectiveValue`
- `parseEventFlag`
- `parseTemplate`
