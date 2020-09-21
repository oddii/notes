# 奇怪的知识之 CSS

## 1、`:root` 

> **概述**

`：root` 这个 `CSS` 伪类匹配文档树的根元素，对于 `HTML` 来说，`:root` 表示 `<html>` 元素，除了**优先级更高**之外，与 `html` 选择器相同

> **示例**

在声明全局 `CSS` 变量时，`:root` 会很有用，如：

```css
:root{
  --main-color: hotpink;
  --pane-padding: 5px 42px;
}
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/:root
```



## 2、`var()`

> **概述**

`var()` 函数可以代替元素中任何属性中的值的任何部分，`var()` 函数不能作为属性名、选择器或者其他除了属性值之外的值

> **语法**

```css
var( <custom-property-name>, <declaration-value>? )
```

函数第一个参数是要替换的自定义属性名称，函数的可选第二个参数用作回退值，如果第一个参数引用的自定义属性无效，则该函数将使用第二个值

> **示例**

```css
:root {
  --main-bg-color: pink;
}

body {
  background-color: var(--main-bg-color);
}
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/var
```



## 3、常用特殊选择器

### 3.1、属性选择器

> **概述**

通过已经存在的属性名或属性值匹配元素

> **语法**

- `[attr]`：表示带有以 `attr` 命名的属性的元素
- `[attr=value]`：表示带有以 `attr` 命名的属性，且属性值为 `value` 的元素
- `[attr~=value]`：表示带有以 `attr` 命名的属性，且该属性是一个以空格作为分割的值列表，其中至少有一个值为 `value` 的元素
- `[attr|=value]`：表示带有以 `attr` 命名的属性，属性值为 `value` 或是以 `value-` 为前缀开头的元素，典型的应用场景是用来匹配语言简写代码（如 `zh-CN`，`zh-TW` 可以用 `zh` 作为 `value`）
- `[attr^=value]`：表示带有以 `attr` 命名的属性，且属性值是以 `value` 开头的元素
- `[attr$=value]`：表示带有以 `attr` 命名的属性，且属性值是以 `value` 结尾的元素
- `[attr*=value]`：表示带有以 `attr` 命名的属性，且属性值至少包含一个值为 `value` 的元素
- `[attr operate value i]`：在属性选择器的右方括号前添加一个用空格隔开的字幕 `i` 或 `I`，可以在匹配属性值时忽略大小写
- `[attr operate value s]`：在属性选择器的右方括号前添加一个用空格隔开的字幕 `s` 或 `S`，可以在匹配属性值时区分大小写

> **示例**

```css
/* 存在title属性的<a> 元素 */
a[title] {
  color: purple;
}

/* 存在href属性并且属性值匹配"https://example.org"的<a> 元素 */
a[href="https://example.org"] {
  color: green;
}

/* 存在class属性并且属性值包含以空格分隔的"logo"的<a>元素 */
a[class~="logo"] {
  padding: 2px;
}

/* 存在href属性并且属性值结尾是".org"的<a> 元素 */
a[href$=".org"] {
  font-style: italic;
}

/* 存在href属性并且属性值包含"example"的<a> 元素 */
a[href*="example"] {
  font-size: 2em;
}

/* 包含 "insensitive" 的链接,不区分大小写 */
a[href*="insensitive" i] {
  color: cyan;
}

/* 包含 "cAsE" 的链接，区分大小写 */ 
a[href*="cAsE" s] { 
  color: pink; 
}
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors
```

### 3.2、相邻兄弟选择器（+）

> **概述**

相邻兄弟选择器（`+`）介于两个选择器之间，当第二个元素**紧跟在**第一个元素之后，并且两个元素都是属于同一个父元素的子元素，则第二个元素将被选中

> **语法**

````css
元素1 + 元素2 { style properties }
````

> **示例**

```css
li:first-of-type + li {
  color: red;
}
```

```html
<ul>
  <li>one</li>
  <li>two</li>	<!-- 这里会被选中 -->
  <li>three</li>
</ul>
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator
```

### 3.3、通用兄弟选择器（~）

> **概述**

通用兄弟选择器（`~`），位置无需紧邻，只需同层级，`A~B` 选择 `A` 元素**之后**所有同层级 `B` 元素

> **语法**

```css
元素1 ~ 元素2 { style properties }
```

> **示例**

```css
p ~ span {
  color: red;
}
```

```html
<span>This is not red.</span>
<p>Here is a paragraph.</p>
<code>Here is some code.</code>
<span>And here is a span.</span> <!-- 这里会被选中 -->
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator
```

### 3.4、子选择器（>）

> **概述**

当使用子选择器（`>`）时，`A>B` 只会匹配作为 `A` 元素 的**直接后代（子元素）**的 `B` 元素

> **语法**

```css
元素1 > 元素2 { style properties }
```

> **示例**

```css
div > span {
  background-color: DodgerBlue;
}
```

```html
<div>
  <span>Span 1. In the div. <!-- 这里会被选中 -->
    <span>Span 2. In the span that's in the div.</span>
  </span>
</div>
<span>Span 3. Not in a div at all</span>
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/Child_combinator
```

### 3.5、后代选择器（ ）

当使用（）选择器时（这里代表一个空格，更确切的说是一个或多个的空白字符），`A B` 可以只匹配那些由 `A` 元素作为祖先元素的所有 `B` 元素（后代元素）

> **语法**

```css
元素1 元素2 { style properties }
```

> **示例**

 ```css
div span { background-color: DodgerBlue; }
 ```

```html
<div>
  <span>Span 1. <!-- 这里会被选中 -->
    <span>Span 2.</span> <!-- 这里会被选中 -->
  </span>
</div>
<span>Span 3.</span>
```

> **参考**

```
https://developer.mozilla.org/zh-CN/docs/Web/CSS/Descendant_combinator
```

