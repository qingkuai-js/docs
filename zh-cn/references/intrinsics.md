# 内建标识符

内建标识符是指在组件文件中无需声明、可被编译器直接识别并处理的保留标识符。它们主要用于访问组件属性、引用属性与插槽状态，以及调用编译器提供的内建方法。

---

## props

`props` 用于在组件内部读取外部传入的普通属性与事件属性。组件可以通过它访问父组件传递的数据和事件处理函数，从而实现组件间的通信与数据流动。

参考： [组件属性](../components/attributes.html)

---

## refs

`refs` 用于在组件内部访问引用属性并执行可写更新。组件可以通过它获取父组件传递的引用数据，并直接修改这些数据，以实现双向绑定或其他交互效果。

参考： [组件属性](../components/attributes.html#引用属性)、[组件引用属性](../components/attributes.md#引用属性)

---

## slots

`slots` 用于在组件内部判断插槽内容是否被传递。组件可以通过它根据父组件是否提供插槽内容来调整渲染逻辑。

参考： [插槽](../components/slots.html)、[根据传递状态渲染](../components/slots.html#根据传递状态渲染)

---

## reactive

`reactive` 是一个内建方法，用于显式标注某个标识符具有深度响应性能力。编译器对标识符的处理取决于其声明方式：使用 `let` 或 `var` 声明时，标识符自身及其所有嵌套属性都会被推导为响应式；使用 `const` 声明时，由于标识符本身不可重新赋值，仅其属性会被递归推导为响应式。

参考： [响应性声明](../basic/reactivity.html#响应性声明)、[响应性模式](../basic/reactivity.html#响应性模式)、[响应性推导规则](./reactivity-infer-rules.html)

---

## shallow

`shallow` 是一个内建方法，用于显式标注某个标识符具有浅层响应性能力。编译器对标识符的处理取决于其声明方式：使用 `let` 或 `var` 声明时，仅标识符自身会被推导为响应式，其属性不参与响应式推导；使用 `const` 声明时，仅其第一层属性会被推导为响应式，更深层属性不参与推导。

参考： [响应性声明](../basic/reactivity.html#响应性声明)、[响应性模式](../basic/reactivity.html#响应性模式)、[响应性推导规则](./reactivity-infer-rules.html)

---

## raw

`raw` 是一个内建方法，用于显式标注某个标识符为静态值。被 `raw` 标记的标识符不会被编译器赋予响应式能力，因此对其的修改不会触发页面更新。

参考： [响应性声明](../basic/reactivity.html#响应性声明)、[响应性推导规则](./reactivity-infer-rules.html)

---

## alias

`alias` 是一个内建方法，用于创建某个标识符的别名。通过 `alias`，开发者可以将复杂的访问或写入操作简化为更直观的表达，同时保持响应性。

参考： [响应性别名](../basic/reactivity.html#响应性别名)、[属性解构](../components/attributes.html#属性解构)

---

## derived

`derived` 是一个内建方法，用于创建衍生响应式状态。通过 `derived`，开发者可以基于现有响应式状态定义新的响应式状态，这些衍生状态会自动追踪依赖并在依赖变化时更新。

参考： [衍生响应式状态](../basic/reactivity.html#衍生响应式状态)

---

## derivedExp

`derivedExp` 是一个内建方法，用于创建衍生响应式状态的简写声明。通过 `derivedExp`，开发者可以直接传入表达式来定义衍生状态，编译器会自动将其转换为标准的 `derived` 声明。

参考： [衍生响应式状态](../basic/reactivity.html#衍生响应式状态)、[衍生响应式状态简写声明](../basic/reactivity.html#衍生响应式状态简写声明)

---

## watchExp

`watchExp` 是一个内建方法，用于创建监视器的简写注册。通过 `watchExp`，开发者可以直接传入表达式来定义监视器依赖，编译器会自动将其转换为标准的 `watch` 注册。

参考： [监视器](../basic/watchers-and-side-effects.html#监视器)、[便捷注册](../basic/watchers-and-side-effects.html#便捷注册)

---

## preWatchExp

`preWatchExp` 是一个内建方法，用于创建前置监视器的简写注册。通过 `preWatchExp`，开发者可以直接传入表达式来定义前置监视器依赖，编译器会自动将其转换为标准的 `preWatch` 注册。

参考： [监视器](../basic/watchers-and-side-effects.html#监视器)、[便捷注册](../basic/watchers-and-side-effects.html#便捷注册)

---

## postWatchExp

`postWatchExp` 是一个内建方法，用于创建后置监视器的简写注册。通过 `postWatchExp`，开发者可以直接传入表达式来定义后置监视器依赖，编译器会自动将其转换为标准的 `postWatch` 注册。

参考： [监视器](../basic/watchers-and-side-effects.html#监视器)、[便捷注册](../basic/watchers-and-side-effects.html#便捷注册)

---

## syncWatchExp

`syncWatchExp` 是一个内建方法，用于创建同步监视器的简写注册。通过 `syncWatchExp`，开发者可以直接传入表达式来定义同步监视器依赖，编译器会自动将其转换为标准的 `syncWatch` 注册。

参考： [监视器](../basic/watchers-and-side-effects.html#监视器)、[便捷注册](../basic/watchers-and-side-effects.html#便捷注册)

---

## defaultProps

`defaultProps` 是一个内建方法，用于定义组件属性的默认值。通过 `defaultProps`，开发者可以为组件的普通属性和事件属性指定默认值，当父组件未传递对应属性时，这些默认值将被使用。

参考： [组件属性](../components/attributes.html#组件属性默认值)

---

## defaultRefs

`defaultRefs` 是一个内建方法，用于定义组件引用属性的默认值。通过 `defaultRefs`，开发者可以为组件的引用属性指定默认值，当父组件未传递对应引用时，这些默认值将被使用。

参考： [组件属性](../components/attributes.html#引用属性默认值)
