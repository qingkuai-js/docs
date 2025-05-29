# Interpolation Blocks

qingkuai's template syntax is very similar to HTML syntax - one might even say it is HTML, but with some subtle differences:

-   Attribute values that aren't wrapped in quotes or curly braces will cause a compiler fatal error;
-   Attribute names starting with `!`, `@`, `#`, or `&` are special and are processed by the compiler;
-   Tag names starting with uppercase letter or containing `-` character are considered component tags (except for embedded language tags);
-   Curly braces can be used in template text or attribute values to add `interpolation blocks` for accessing values from embedded scripting languages;

---

## Text Interpolation

To access values from embedded scripting languages in templates, simply write a pair of curly braces in the text and include JS/TS expressions inside:

```qk
<lang-js>
    let variable = "QingKuai"
</lang-js>

<p>value of variable is: {variable}</p>
```

| The page will display: <u>value of variable is: QingKuai</u>, and will update accordingly when the variable's value changes.

---

## Dynamic Attributes

However, sometimes we need interpolation not just in text but also in attribute values. In such cases, you can prefix a regular attribute name with an exclamation mark `!` and wrap the attribute value in curly braces to use dynamic attribute syntax:

```qk
<div !id={dynamicId}></div>
```

<div class="custom-block tip">
    All interpolation blocks are reactive - when reactive variables in the embedded scripting language change, the page updates accordingly.
</div>

<br />

When `class` is used as a dynamic attribute, it can accept either an object or an array as its value. When the value is an object, property names with truthy values will be applied to the class list. When the value is an array, each item will be applied to the class list. Both syntaxes are allowed:

```qk
<!-- Dynamic class attribute in object form -->
<div
    !class={
        {
            active,
            "dark-mode": isDarkMode
        }
    }
></div>

<!-- Dynamic class attribute in array form -->
<div !class={[a, b, c, d]}><div>
```

Note that in template syntax, a single tag cannot have multiple attributes with the same name, even if one is regular and one is dynamic. The exception is `class`, which allows both a regular and dynamic class attribute to coexist (only one of each), and the compiler will merge both class lists:

```qk
<!-- Compiler error: duplicate attribute name -->
<div id="top-box" !id={dynamicId}></div>

<!-- Compiles normally, but cannot have two regular or dynamic class attributes simultaneously -->
<div class="container" !class={getDynamicClassList()}></div>
```

If your interpolation block only needs to contain an identifier matching the attribute name, you can omit the interpolation block. These two syntaxes are equivalent:

```qk
<div !id></div>
<div !id={id}></div>
```

<div class="custom-block warning">This syntax isn't supported if the attribute name is a keyword or reserved word in the embedded scripting language, such as <code>class</code> or <code>for</code> attributes.</div>

---

## Valid Interpolation Expressions

Whether in text interpolation, dynamic attribute interpolation, or the [directives](./compilation-directives.html), [reference attributes](./reference-attributes.html), or [events](./event-handling.html) we'll introduce later, only expressions are allowed - statements are prohibited. A simple way to determine if code is an expression is to consider whether it could appear on the right side of an assignment statement. If not, it's likely a statement. For example, each line below is a valid interpolation expression:

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

Whereas each line below is a statement and cannot appear in interpolation blocks, as they would cause compiler fatal errors:

```qk
{try{}}

{return 10}

{const number = 10}

{if(condition){}}

{switch(value){}}

{for(const user of users){}}

{import {raw} from "qingkuai"}
```
