# 插值块

qingkuai 的模板语法与 HTML 语法非常相似，甚至可以说它就是 HTML，但又存在一些细微的区别：

-   模板语法中属性值未使用引号或花括号包裹时会导致编译器致命错误；
-   以 `!` 、 `@` 、 `#` 、 `&` 这四个字符开头的属性名称是特殊的，这些属性由编译器处理；
-   以大写字母开头或含有`-`字符的标签名会被认为是组件标签（嵌入语言标签除外）；
-   在模板的文本或属性值处可以使用花括号添加`插值块`以访问嵌入脚本语言中的值；

---

## 文本插值

要在模板中访问嵌入脚本语言的值，只需在文本处写一对花括号，并在其中编写 JS/TS 表达式即可：

```qk
<lang-js>
    let variable = "QingKuai"
</lang-js>

<p>value of variable is: {variable}</p>
```

| 此时页面会显示：<u>value of variable is: QingKuai</u>，并且在 variable 变量的值发生变更时页面也会发生相应的变化。

---

## 动态属性

然而，有些时候我们不止需要在文本处插值，还需要在属性值处插值，此时就可以在普通属性名称前添加一个英文感叹号 `!` ，并将属性值用花括号包裹以使用动态属性语法：

```qk
<div !id={dynamicId}></div>
```

<div class="custom-block tip">所有的插值块都是带有响应性能力的，即嵌入脚本语言中的响应性变量变更时页面也会发生相应变化。</div>

<br />

当 `class` 作为动态属性时，还可接受一个对象或一个数组作为值，当值为对象时，对象属性值为真值（truthy）时会应用对象属性名到 class 列表，当值为数组时，数组中的每一项都会被应用到 class 列表，也就是说下面两种语法都是允许的：

```qk
<!-- 对象形式的动态class属性 -->
<div
    !class={
        {
            active,
            "dark-mode": isDarkMode
        }
    }
></div>

<!-- 数组形式的动态class属性 -->
<div !class={[a, b, c, d]}><div>
```

需要注意，模板语法中同一个标签不能同时拥有相同名称的多个属性，即使一个是普通属性，一个是动态属性也不行。但 `class` 是个例外，它允许同时存在普通和动态 class 属性（均只能存在一个），编译器会将两种 class 列表合并：

```qk
<!-- 编译器报错，属性名重复 -->
<div id="top-box" !id={dynamicId}></div>

<!-- 正常编译，但不能同时存在两个普通或动态class属性 -->
<div class="container" !class={getDynamicClassList()}></div>
```

如果你的插值块中只需要输入一个与属性名名称相同的标识符，那么你可以省略这个插值块，也就是说下面两种写法是等效的：

```qk
<div !id></div>
<div !id={id}></div>
```

<div class="custom-block warning">若属性名称是嵌入脚本语言中的关键字或保留字，则不支持这种语法，如 <code>class</code> 或 <code>for</code> 属性等。</div>

---

## 合法的插值表达式

无论是文本插值、动态属性插值，还是我们稍后将介绍的[指令](./compilation-directives.html)、[引用属性](./reference-attributes.html)或[事件](./event-handling.html)插值中，都只能编写表达式，不允许使用语句。粗略判断某段代码是否为表达式的一个简单方法是：思考它是否可以出现在赋值语句的等号右侧，如果不能，那它很可能就是一条语句，而不是表达式，例如下面的代码中每一行都是一条合法的插值表达式：

```qk
{a * b - 5}

{`Hello ${str}`}

{class MyClass{}}

{new Date()}

{() => {}}

{function anonymous(){}}

{condition ? value1 : value2}

{str.split("").reverse().join("")}
```

而下面代码中每一行都是一条语句，它们不能出现在插值块中，否则将导致编译器致命错误：

```qk
{try{}}

{return 10}

{const number = 10}

{if(condition){}}

{switch(value){}}

{for(const user of users){}}

{import {raw} from "qingkuai"}
```
