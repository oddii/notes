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

## 4、object-fit 与 object-position

> **object**

首先要了解下这里的 `object` 为何物？

官方文档解释是这样的：

The `object-fit` CSS property specifies how the contents of a replaced element should be fitted to the box established by its used **height** and **width**.

The `object-position` property determines the alignment of the replaced element inside its box.

虽然官方文档没有说 `object` 具体是什么，我们可以从 `W3C`、`MDN` 中查到，这里的 `object` 实际上指的是 `replaced element` （替换元素）

> **替换元素**

不是所有的元素都叫**替换元素**，在 `CSS` 中，**替换元素**指的是：

其内容不受 `CSS` 视觉格式化模型控制的元素，比如 `img`，嵌入的文档（ `iframe` 之类）或者 `applet`，比如说，`img` 元素的内容通常会被其 `src` 属性指定的图像**替换**掉

**这是什么意思？**

首先要清楚 `img` 的结构原理，`img` 是一个元素，`img` 就像是一个 `div` 一样，包裹着里面的图片

<img src="/Users/hellotalk/Downloads/img结构.jpg" alt="img结构" style="zoom:80%;" />

`img` 后面调用的 `src="xxx.jpg"` 就是图片的本体，它可以替换 `img` 的内容，所以不要想着 `img` 就是单纯的一张图，其实它本身是有结构的，所以它就属于一个**替换元素**，而这两者之间的关系是独立的，所以为 `img` 设置宽高，其实是为 `img` 的**外壳**设置宽高，而不是其中的**替换元素**，这就是为什么 `object-fit` 能生效的原因，`object-fit`控制了 `src` 对应的替换内容的尺寸，或者包含，或者覆盖

**替换元素**通常有其固有的尺寸：一个固有的**宽度**，一个固有的**高度**，一个固有的**比率**。比如一副位图有绝对指定的宽度和高度，从而也有固有的宽高比率。另一方面，其他文档也可能没有固有的尺寸，比如一个空白的 `html` 的文档

`CSS` 渲染模型不考虑**替换元素**内容的渲染，这些替换元素的展现独立于 `CSS`，`object`、`video`、`textarea`、`input` 也是**替换元素**，`audio`、`canvas` 在某些特定情形下为**替换元素**

就是说，`object-fit` 和 `object-position` 只针对替换元素有作用，也就是 `form` 表单家族控件系列、`img` 图片、`video` 等元素

> **object-fit**

`object-fit` 和 `object-position` 之间的关系有点类似于 `background-size` 和 `background-position`，无论是关系还是属性值，都有相似之处，因此我们在理解 `object-fit` 和 `object-position` 的时候，可以或多或少映射 `background-size` 和 `background-position`

**object-fit 属性**

- `fill`：中文释义“填充”，默认值，替换内容拉伸填满整个 `content box`, 不保证保持原有的比例
- `contain`：中文释义“包含”，保持原有尺寸比例，保证替换内容尺寸一定可以在容器里面放得下。因此，此参数可能会在容器内留下空白
- `cover`：中文释义“覆盖”，保持原有尺寸比例，保证替换内容尺寸一定大于容器尺寸，宽度和高度至少有一个和容器一致。因此，此参数可能会让替换内容（如图片）部分区域不可见
- `none`：中文释义“无”，保持原有尺寸比例，同时保持替换内容原始尺寸大小
- `scale-down`：中文释义“降低”，就好像依次设置了**none**或**contain**，最终呈现的是尺寸比较小的那个

**使用注意**

由上面的英文官方介绍我们知道，`object-fit` 需要为你使用的 `img` 设置 `width` 和 `height` 才能生效，因为，`img` 在没有设置宽度和高度时，它的宽高会跟随图片本身的大小，此时你设置 `object-fit` 是**无效**的

目前，IE浏览器并不支持`object-fit`

> **object-position**

`object-position` 要比 `object-fit` 单纯的多，就是控制替换内容位置的。默认值是 `50% 50%`，也就是居中效果，所以，无论上一节 `object-fit` 值为那般，图片都是水平垂直居中的

其可以使用 `calc` 实现相对右下角定位：

```css
object-position: calc(100% - 20px) calc(100% - 10px);
```

## 5、外容器是自适应的盒子，里层有两个盒子，一个固定高度，希望另一个要填满剩下的高度

> **CSS3 弹性盒子解法**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="gb2312">
    <title>Title</title>
    <style>
        .box {
            width:200px;
            height:300px;
            background:red;
            display:flex;
            flex-direction:column;
      	}
        
        .a {height:100px;background:green;}
        .b {background:blue;flex:1}
    </style>
</head>
<body>
     <div class="box">
         <div class="a"></div>
         <div class="b"></div>
     </div>
</body>
</html>
```

使用 `flex: 1` 将另一个盒子的填满剩下的位置

> **纯 CSS 解法**

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="gb2312">
    <title>Title</title>
    <style>
        body { height: 100%; padding: 0; margin: 0; }
        .box { height: 400px; position: relative; }
        .a { height: 100px; background: #ff0000; }
        .b { background: #00ff00; width: 100%; position: absolute; top: 100px ; left: 0 ; bottom: 0;}
    </style>
</head>
<body>
     <div class="box">
         <div class="a"></div>
         <div class="b"></div>
     </div>
</body>
</html>
```

将外容器设置为 `position: relative`，高度固定的盒子使用 `position: relative`，另一个盒子设置为

```css
b {
  position: absolute;
  top: 0;
  rigth: 0;	//	or left: 0
  bottom: 0;
}
```

## 6、画出扇形与半圆形

> **半圆形**

```css
.half-circle {
    width: 200px;
    height: 100px;
    background-color: skyblue;
  
    border-top-left-radius: 100px;
    border-top-right-radius: 100px;
    border: 10px solid gray;
    border-bottom: 0;
    
    box-sizing: border-box;
}
```

```html
<div class="half-circle"></div>
```

> **扇形**

```css
.half-circle {
    width: 100px;
    height: 100px;
    background-color: skyblue;
  
    border-top-left-radius: 100px;
    border: 10px solid gray;
    border-right: 0;
    border-bottom: 0;
    
    box-sizing: border-box;
}
```

```html
<div class="half-circle"></div>
```

