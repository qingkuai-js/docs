# Error Code Reference

The compiler and runtime of Qingkuai may throw prompt messages with error codes to help developers quickly locate problems. This section lists all built-in error codes and their meanings for easy reference and troubleshooting. Error codes are categorized by function type and follow the following numbering rules:

-   1xxx: Compilation errors, indicating that the code cannot be compiled successfully, usually requiring developers to fix syntax or logic issues;
-   9xxx: Compilation warnings, indicating potential problems or discouraged usages, but will not block compilation;
-   2xxx: Runtime errors, indicating fatal issues occurred during execution, which may cause program interruption;
-   8xxx: Runtime warnings, indicating non-blocking abnormal behaviors during execution, which are recommended to be addressed.

By using error codes to quickly find detailed information, debugging efficiency can be improved and framework behavior better understood.

---

## Compilation Errors

| Status Code | Description                                                                                                                                                                                  |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1001        | Empty interpolation block                                                                                                                                                                    |
| 1002        | Unexpected character                                                                                                                                                                         |
| 1003        | Unclosed interpolation block                                                                                                                                                                 |
| 1004        | Invalid identifier format                                                                                                                                                                    |
| 1005        | Unclosed attribute value (e.g., `class="box`)                                                                                                                                                |
| 1006        | Unclosed HTML tag (e.g., `<div` or `</div`)                                                                                                                                                  |
| 1007        | HTML end tag without a matching start tag (e.g., there is no matching `<div>` for `</div>`)                                                                                                  |
| 1008        | Interpolation attribute without a name (e.g., only using `!`, `@`, `#`, or `&` as the attribute name)                                                                                        |
| 1009        | Embedded language tag exceeds limit (a component file can have at most one embedded script language tag)                                                                                     |
| 1010        | Tag cannot be used as a self-closing tag (e.g., `<div />`)                                                                                                                                   |
| 1011        | Tag using `#key` directive does not have `#for` directive                                                                                                                                    |
| 1012        | `#slot` directive is not used on a top-level child tag of a component                                                                                                                        |
| 1013        | Multiple top-level child tags in a component have the same `slot` attribute value                                                                                                            |
| 1014        | A regular element tag received an unacceptable reference attribute                                                                                                                           |
| 1015        | Directive requires explicit assignment but no value was provided (e.g., `<Test #await />`)                                                                                                   |
| 1016        | Value part of an interpolation attribute is not wrapped in braces (e.g., `!attr="xxx"`)                                                                                                      |
| 1017        | Value part of a regular attribute is not enclosed in quotes (e.g., `class=box`)                                                                                                              |
| 1018        | Incompatible directives are used together on the same tag (e.g., both `#if` and `#else`)                                                                                                     |
| 1019        | Dynamic `name` attribute is not allowed on `slot` tag                                                                                                                                        |
| 1020        | Duplicate identifier is registered in the top-level scope of an embedded script block (e.g., `rea`, `props`, etc.)                                                                           |
| 1021        | Duplicate attribute (e.g., both `!id` and `id` attributes on the same tag)                                                                                                                   |
| 1022        | Missing starting directive (e.g., previous sibling tag of a tag using `#elif` does not use `#if`)                                                                                            |
| 1023        | Built-in reactivity declaration methods are used outside the top-level scope of an embedded script block                                                                                     |
| 1024        | An `input` tag with dynamic `type` attribute or a `select` tag with dynamic `multiple` attribute can only accept `&dom` as a reference attribute                                             |
| 1025        | Built-in reactivity declaration methods are used outside variable declaration statements                                                                                                     |
| 1026        | Unknown directive (see: [Compilation Directives](../basic/compilation-directives.html))                                                                                                      |
| 1027        | Identifier format is forbidden (e.g., `__c__`, `__s1__`, etc.)                                                                                                                               |
| 1028        | No argument is passed to built-in methods when destructuring reactive declarations (`undefined` cannot be destructed)                                                                        |
| 1029        | Invalid reference attribute value (constant or non-targetable value, see: [Valid Values for Reference Attributes](../basic/reference-attributes.html#valid-reference-attribute-values)) |
| 1030        | Reference attributes are not accepted on regular tags (see: [Reference Attributes](../basic/reference-attributes.html))                                                                      |
| 1031        | Invalid `slot` attribute (e.g., `!slot` or `&slot`)                                                                                                                                          |
| 1032        | Multiple `slot` tags have the same `name` attribute                                                                                                                                          |
| 1033        | Identifiers in template context (usually created by directives) cannot be used as targets for reference attributes (because they are constants)                                              |
| 1034        | No matching end tag found (e.g., no corresponding `</div>` for a `<div>`)                                                                                                                    |
| 1035        | Embedded language tag is used as a child element                                                                                                                                             |
| 1036        | Invalid `#for` directive value (see: [List Rendering](../basic/compilation-directives.html#list-rendering))                                                                                  |
| 1037        | Mixing derived reactive state shortcut declarations with non-`der` built-in helper methods (see: [Derived Reactive State](../basic/reactivity.html#derived-reactive-state))                  |
| 1038        | Required parameters are missing when using built-in watcher registration helper methods (minimum two parameters required)                                                                    |
| 1039        | `slot` tag does not accept any events                                                                                                                                                        |
| 1040        | [export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) related syntax is not allowed inside embedded script blocks                                    |
| 1041        | Format of directive value that produces context identifiers is invalid (e.g., values like `#then`, `#catch` must be valid identifiers or object/array literal expressions)                   |
| 1042        | Tag using `#html` directive has child elements (they can only accept a single text node child)                                                                                               |
| 1043        | `#html` directive is used on an unsupported tag (e.g., self-closing tag, component, or `slot` tag)                                                                                           |
| 1044        | `&dom` reference attribute is used on an unsupported tag (e.g., component or `slot` tag)                                                                                                     |
| 1045        | Top-level [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) syntax is not supported inside embedded script blocks                                   |

---

## Compilation Warnings

| Status Code | Description                                                                                          |
| ----------- | ---------------------------------------------------------------------------------------------------- |
| 9001        | Extra arguments are passed when using built-in methods; they are ignored                             |
| 9002        | `$args` identifier in the top-level scope of an embedded script block is overwritten in inline events |
| 9003        | Mixing derived reactive state shortcut declarations with `der` built-in method                       |
| 9004        | Invalid event flags; they are ignored                                                                |
| 9005        | Events in components do not accept any flags; they are ignored                                       |
| 9006        | Adding `compose` flag to non-`input` events; it will be ignored                                      |
| 9007        | Using keyboard-related flags on non-keyboard-related events; they are ignored                        |
| 9008        | Duplicate event flags                                                                                |
| 9009        | Redundant directive value (e.g., `#else={ok}`); this value will be omitted                           |
| 9010        | `slot` tag's `name` attribute is empty; defaults to `default`                                        |
| 9011        | `slot` attribute of a component's top-level child is empty; defaults to `default`                    |

---

## Runtime Errors

| Status Code | Description                                                                                                              |
| ----------- | ------------------------------------------------------------------------------------------------------------------------ |
| 2001        | Assigning value to a constant reactive state                                                                             |
| 2002        | Duplicate values in `#key` directive                                                                                     |
| 2003        | Component class cannot be manually instantiated                                                                          |
| 2004        | Invalid reactivity depth (less than 0)                                                                                   |
| 2005        | `#for` directive value is not iterable (see [List Rendering](../basic/compilation-directives.html#list-rendering))       |
| 2006        | `#await` directive value is not a `Promise` (see: [Async Handling](../basic/compilation-directives.html#async-processing)) |
| 2007        | Mount target selector given by `mountApp` or `#target` directive cannot be found                                         |

---

## Runtime Warnings

| Status Code | Description                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| 8001        | Assigning value to property of built-in `props` object (invalid operation; assigning to getter fails)     |
| 8002        | Assigning value to derived reactive state (invalid operation; assigning to getter fails)                  |
| 8003        | Target of watcher registration or method of side effect registration contains no reactive state           |
| 8004        | Derived reactive state does not depend on any other reactive states (can be declared as regular variable) |
| 8005        | `#target` directive value is neither a string (selector) nor a valid HTML element                         |
