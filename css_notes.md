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



## 3、

