# 奇怪的知识之 JS

## 1、为什么 `await` 不能使用在 `forEach()` 中？

> **示例**

```js
function test() {
    const arr = [3, 2, 1]
    arr.forEach(async item => {
        const res = await fetch(item)
        console.log(res)
    })
    console.log('end')
}

function fetch(x) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(x)
        }, 500 * x)
    })
}

test()
```

期望结果：

```
3
2
1
end
```

输出结果：

```
end
1
2
3
```

> **为什么？**

因为 `forEach()` 只支持同步代码，参考 `Polyfill` 版本的 `forEach()`，简化后就是类似这样的伪代码

```js
while(index < arr.length){
  callback(item, index)	//	就是我们传给 forEach() 的回调函数
}
```

从上述代码中可以发现， `forEach()` 只是简单地执行了下回调函数而已，并不会去处理异步的情况，并且你在 `callback()` 中即使使用 `break` 也并不能结束遍历

> **怎么解决？**

一般来说解决方法有3种，`for...of`、`for` 循环、`while` 循环

使用 `Promise.all` 的方式行不行？答案是：**不行**

```js
async function test() {
    const arr = [3, 2, 1]
    await Promise.all(arr.map(async item => {
        const res = await fetch(item)
        console.log(res)
    }))
    console.log('end')
}
```

输出结果：

```
1
2
3
end
```

可以看到并没有按照我们期望的输出

这样可以生效的原因是因为 `async` 函数肯定会返回一个 `Promise` 对象，调用 `map()` 以后返回值就是一个存放 `Promise` 的数组了，这样我们把数组传入 `Promise.all` 中就可以解决问题了，但是这种方式其实并不能达成我们想要的效果

> **第1种解决方法：for...of**

```js
async function test() {
    const arr = [3, 2, 1]
    for (let value of arr) {
        const res = await fetch(value)
        console.log(res)
    }
    console.log('end')
}
```

这种方式比 `Promise.all` 要简洁的多，并且也可以实现开头我想要的输出顺序，但是为什么 `for...of` 内部就能让 `await` 生效？

这是因为 `for...of` 内部处理的机制和 `forEach()` 不同，`forEach()` 是直接调用回调函数，`for...of` 是通过迭代器的方式去遍历

```js
async function test() {
    const arr = [3, 2, 1]
    const iterator = arr[Symbol.iterator]()
    let next = iterator.next()
    while (!next.done) {
        const value = next.value
        const res = await fetch(value)
        console.log(res)
        next = iterator.next()
    }
    console.log('end')
}
```

> **第2种解决方法：for 循环**

```js
async function test() {
    const arr = [3, 2, 1]
    for (let i = 0, len = arr.length; i < len; i++) {
        const item = arr[i]
        const res = await fetch(item)
        console.log(res)
    }
    console.log('end')
}
```

> **第3种解决方法：while 循环**

```js
async function test() {
    const arr = [3, 2, 1]
    let i = 0,
        len = arr.length
    while (i < len) {
        const item = arr[i]
        const res = await fetch(item)
        console.log(res)
        i++
    }
    console.log('end')
}
```

## 2、`Promise.prototype.then()` 第二个参数和 `Promise.prototype.catch()` 捕获错误有什么区别？
首先需要区分2个概念

1. `Promise.reject()` 是用来抛出异常的，`Promise.prototype.catch()` 是用来处理异常的

2. `reject` 是 `Promise` 的方法，`then` 和 `catch` 是 `Promise` 实例的方法

3. `catch` 只是一个语法糖而已，还是通过 `then` 来处理的，大概如下所示：

   ```js
   Promise.prototype.catch = function(fn){
     return this.then(null, fn)
   }
   ```

> **区别**

- 如果在 `then` 的第一个参数里抛出了异常，后面的 `catch` 能捕获到，而 `then` 的第二个参数捕获不到

  ```js
  const promise = new Promise((resolve, reject) => {
      resolve()
  })
  
  promise
      .then(function () {
          throw new Error()
      }, function (err) {
          console.log(`then got ${err}`)
      })
      .catch(function (err) {
          console.log(`catch got ${err}`);
      })
  
  //	catch got Error
  ```

- `then` 的第二个参数和 `catch` 捕获错误信息的时候会采取就近原则，如果 `Promise` 内部报错，`reject` 抛出错误后，`then` 的第二个参数和 `catch` 方法都存在的情况下，只有 `then` 的第二个参数能捕获到，如果 `then` 的第二个参数不存在，则 `catch` 方法能捕获到

   ```js
  const promise = new Promise((resolve, reject) => {
      reject('error')
  })
  
  promise
      .then(function () {
          
      }, function (err) {
          console.log(`then got ${err}`)
      })
      .catch(function (err) {
          console.log(`catch got ${err}`);
      })
  
  //	then got error
  ```

  ```js
  const promise = new Promise((resolve, reject) => {
      reject('error')
  })
  
  promise
      .then(function () {
          
      })
      .catch(function (err) {
          console.log(`catch got ${err}`);
      })
  
  //	catch got error
  ```

## 3、JS 中的隐式类型转换

显示转换：将值从一种类型转换为另一个种类型

隐式转换：将值**强制**从一种类型转换为另一种类型

## 4、JS 将一个页面重定向去另一个页面

`window.location.replace()` 最适合模拟 `HTTP` 重定向，`window.location.replace()` 优于使用 `window.location.href`，因为 `replace()` 不会将原始页面保留在会话历史记录中，这意味着用户将不会陷入永无休止回退按钮

如果要模拟点击链接，可以使用 `location.href`，如果要模拟 `HTTP` 重定向，请使用 `location.replace`

## 5、如何从 JS 对象中删除属性

```js
delete obj.property
delete obj['property']
```

## 6、跨域与 OPTIONS 请求

> **同源策略**

在说**跨域**之前，首先要了解一下**同源策略**

百度百科这样定义**同源策略**：

**同源策略**（Same origin policy）是一种约定，它是浏览器最核心也是最进本的安全功能，如果缺少了**同源策略**，则浏览器的正常功能可能都会受到影响。可以说 `Web` 是构建在**同源策略**基础之上的，浏览器知识针对**同源策略**的一种实现

简单理解**同源策略**就是一种安全策略，为了安全而生的一种限制措施，它是由 `Netscapte` 公司提出的一个著名安全策略，现在所有支持 `JS` 的浏览器都会使用这个策略，也是必须遵守的一个策略

**同源策略**中的同源指的是**域名**、**协议**、**端口**三者必须相同，如果有任何一个不同就会引起**跨域**

**同源策略**会影响：

- `Cookie`、`LocalStorage` 和 `IndexDB` 无法读取
- `DOM` 无法获取
- `Ajax` 请求不能正常发送，有可能还会引起 `OPTIONS` 请求

> **HTTP 请求方法**

- `GET`：向特定的资源发出请求，一般对服务器来说是一个只读的请求，不会对资源进行写操作，应该只被用于获取数据（为 `HTTP 1.0` 定义）
- `POST`：向指定资源提交数据进行处理请求，例如提交表单或者上传文件，数据被包含在请求体中，该请求可能会对服务器资源进行读写操作（为 `HTTP 1.0` 定义）
- `PUT`：向指定资源位置上传其最新内容，一般用于资源的整体更新，而下面的 `PATCH` 用于资源的部分更新
- `DELETE`：请求服务器删除所标识的资源
- `HEAD`：向服务器索要与 `GET` 请求相一致的响应，只不过响应体将不会被返回，可以在不必传输整个响应内容的情况下，就可以获取包含在响应消息头中的元信息（为 `HTTP 1.0` 定义）
- `TRACE`：回显服务器收到的请求，主要用于测试或诊断
- `OPTIONS`：返回服务器针对特定资源所支持的 `HTTP` 请求方法。也可以利用向 `Web` 服务器发送 `*` 的请求来测试服务器的功能性。该请求不会修改服务器资源，相对比较安全
- `CONNECT`：是 `HTTP/1.1` 协议预留的，能够将连接改为管道方式的代理服务器。通常用于 `SSL` 加密服务器的链接与非加密的 `HTTP` 代理服务器的通信
- `PATCH`：是对 `PUT` 方法的补充，用来对已知资源进行局部更新。当资源不存在时，`PATCH` 会创建一个新的资源，而 `PUT` 只会对已存在的资源进行更新

> **OPTIONS 请求**

回到这次的重点 `OPTIONS` 请求，该请求与 `HEAD` 请求有点类似，一般也是用于客户端查看服务器的性能

`OPTIONS` 方法会请求服务器返回该资源所支持的所有 `HTTP` 请求方法，该方法会用来替代资源名称，向服务器发送 `OPTIONS` 请求，可以测试服务器功能是否正常，`JS` 的 `XMLHttpRequest` 对象进行 `CORS` 跨域资源共享时，就是使用 `OPTIONS` 方法发送嗅探请求，以判断是否有对指定资源的**访问权限**

触发 `OPTIONS` 请求的条件：

1. 必须是**跨域请求**
2. 自定义了请求头
3. 请求头中的 `Content-Type` 是 `application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain` **之外**的格式（非简单请求）

满足了 **1** 和 **2**，或者满足 **1** 和 **3** 就会发生 `OPTIONS` 请求

> **OPTIONS 请求主要用途**

- 获取服务器支持的 `HTTP` 请求方法
- 用来检查服务器的性能，如 `Ajax` 进行跨域请求时的预检，需要向另外一个域名的资源发送一个 `HTTP` 的 `OPTIONS` 请求头，用以判断实际发送的请求是否安全

`HTTP` 的 `OPTIONS` 请求，很多地方也被称之为**预请求**、**预检请求**，换句话说就是试探性的请求，不算正式请求

为了避免对服务器产生一些副作用，有些时候网页中的请求就会产生 `OPTIONS` 请求，也算是一种对服务器的保护，只有当服务器允许后，浏览器才会发出正式的请求，否则不发送正式请求

> **跨域**

解决跨域问题，大概有下面几种方案：

- `JSONP`
- `CORS`
- `postMessage`
- `websocket`
- `node` 中间件
- `nginx` 反向代理
- `window.name` + `iframe`
- `location.hash` + `iframe`

## 7、DOM 获取 class 相关操作

> **classList**

`Element.classList` 是一个只读属性，返回一个元素的类属性的实时 `DOMTokenList` 集合，其本身是只读的，但是可以通过 `DOMTokenList` 的 `API` 进行其他操作修改 `DOM` 元素的类属性

那这个 `DOMTokenList` 又是什么呢？

> **DOMTokenList**

`DOMTokenList` 接口表示一组空格分隔的标记，其总是区分大小写

我们可以看一下它的数据结构：

```js
{
  0:'container',
  2:'container2',
  length: 2,
  value: 'container container2'
}
```

其类似一个伪数组，索引从 `0` 开始，`DOMTokenList` 不可以通过 `length` 和索引改变它的值，但可以通过 `value` 改变它的值

其会自动去除空格和重复项目，例如：

```html
<span class="    d   d e f"></span>
```

```js
let span = document.querySelector("span")
let classes = span.classList
span.classList.add("x")
span.textContent = `span classList is "${classes}"`
```

输出结果：

```
span classList is "d e f x"
```

**属性：**

- `length`：只读，一个整数，表示存储在该对象里值的个数
- `value`：该属性以

**方法：**

`DOMTokenList` 的方法较多，详细可以查看：https://developer.mozilla.org/zh-CN/docs/Web/API/DOMTokenList

这里只列出兼容 `IE10` 或以上的方法：

- `item(index)`：返回一个在列表中的索引的项
- `contains(token)`：用于判断是否存在于列表中的标记，当`token`包含在列表中时返回`true`，否则返回`false`
- `add(token)`：添加一个或多个标记（`token`）到 `DOMTokenList` 列表中，`IE` 不能添加多个！
- `remove(token)`：从 `DOMTokenList` 列表中移除一个或多个标记 （`token`），`IE` 不能移除多个！

所以 `DOM.classList` 能够调用相关 `API` 的方法就是其用返回的 `DOMTokenList` 的 `API` 再去修改类属性

从上面我们也看出来了，这些操作并不兼容 `IE9`！！头疼，

> **className**

 `className` 是一个字符串变量，表示当前元素的`class`属性的值，可以是由空格分隔的多个`class`属性值

由于它的兼容性优于 `classList`，所以通常可以使用它对 `classList` 进行兼容性处理（需进行 `babel` 处理）：

```js
export const hasClass = (() => {
  const html = document.documentElement;
  if ("classList" in html && typeof html.classList.contains === "function") {
    return (elem, className) => {
      if (typeof className !== "string") return false;
      if (elem.nodeType === 1) {
        return elem.classList.contains(className);
      }
      return false;
    };
  } else {
    return (elem, className) => {
      if (typeof className !== "string") return false;
      if (elem.nodeType === 1) {
        const classes = elem.className.split(/s+/);
        const len = classes.length;
        for (let i = 0; i < len; i++) {
          if (classes[i] === className) {
            return true;
          }
        }
        return false;
      }
      return false;
    };
  }
})();
```

## 8、in 与 hasOwnProperty

> **in**

**语法：**

```js
"prop" in object
```

**参数：**

- `prop`：一个字符串类型或者 `Symbol` 类型的属性名或者数组索引（非 `symbol` 类型将会强制转为字符串）
- `object`：检查它（或其原型链）是否包含具有指定名称的属性的对象

如果指定的属性在指定的对象或其原型链中，则 `in` 运算符返回 `true`，对被删除或值为 `undefined` 的属性使用 `in` 运算符会返回 `false`

**如果一个属性是从原型链上继承来的**，`in` 运算符也会返回 `true`

> **hasOwnProperty**

**语法：**

```js
obj.hasOwnProperty(prop)
```

**参数：**

- 要检测的属性的 `String` 字符串形式表示的名称，或者 `Symbol`

用来判断某个对象是否含有指定的属性的 `Boolean`

这个方法可以用来检测一个对象是否含有特定的自身属性，即使属性的值是 `null` 或 `undefined`，只要属性存在，`hasOwnProperty` 依旧会返回 `true`

和 `in` 运算符不同，**该方法会忽略掉那些从原型链上继承到的属性**

> **结论**

从上面加粗的文字就可以知道，`in` 运算符与 `hasOwnProperty` 最大的不同就是，`in` 运算符判断从原型链继承的属性仍然会返回 `true`，而 `hasOwnProperty` 则会返回 `false`

`hasOwnProperty` 也会将 `ES6` 的 `getters` 和 `methods` 返回 `false`

## 9、JS 获取 div 高度的方法、getComputedStyle

> **object.offsetHeight**

包括：元素内容 + `padding` + `border-width`

> **object.clientHeight**

包括：元素内容 + `padding`

**区别：**

从上述来看，这两个获取 `div` 高度的属性区别就是有无 `border-width`

> **object.getBoundingClientRect()**

- 该方法返回元素的大小及其相对于视口的位置（`CSS` 属性边框集合，即 `top`、`left`、`right`、`bottom`、`x`、`y`、`width`、`height`，且 `IE` 不兼容 `x`、`y`）

- 如果是标准盒子模型，元素的尺寸等于 `width/height` + `padding` + `border-width` 的总和。如果 `box-sizing: border-box`，元素的的尺寸等于  `width/height`，当然，这个 `height` 也是如 `offsetHeight` 一样，包括元素内容 + `padding` + `border-width`
- 而它如果被**空边框盒**（没有内容的边框）调用时会被忽略，返回的 `width`、`height` 值为 0
- 除了`width` 和 `height` 以外的属性是相对于视图窗口的**左上角**来计算的

但是我们如果想获得元素本身的高度呢？	

> **window.getComputedStyle()**

`window.getComputedStyle` 是一个可以获取的是最终应用在元素上的**所有** `CSS` 属性对象（即使没有 `CSS` 代码，也会把默认的祖宗八代都显示出来），返回的是一个 `CSS` 样式声明对象 `([object CSSStyleDeclaration])`，**只读**

**语法：**

```js
let style = window.getComputedStyle(element, [pseudoElt(伪类)]);
```

**与 `style` 的区别：**

- 只读与可写：`getComputedStyle` 是只读的，而 `style` 能屈能伸，可读可写
- 获取的对象范围：`getComputedStyle` 是获取的是最终应用在元素上的所有 `CSS` 属性对象（即使没有CSS代码，也会把默认的祖宗八代都显示出来），而 `style` 只能获取元素 `style` 属性中的 `CSS` 样式，对于一个光秃秃的 `div` 元素来说，`element.style` 返回的对象 `length` 为 0

所以如果我们只想获得高度，只需要结合 `getPropertyValue` 方法即可

>**object.getPropertyValue**

该方法返回一个 `DOMString`，其中包含请求的 `CSS` 属性的值，属性不支持驼峰写法，其还可以获得 `CSS` 原生变量值

**语法：**

```js
var value = style.getPropertyValue(property);
```

所以上文如果只想获得高度，可以这样使用：

```js
let height = window.getComputedStyle(div).getPropertyValue('height')
```

当然，如果涉及到了兼容性问题，还可以使用 `getAttribute` 方法，该方法也可以访问 `CSS` 样式对象的属性。用法与 `getPropertyValue` 类似

## 10、JS 判断一个对象是否为空对象

- 在高版本的浏览器中可以这样使用

  ```js
  function isEmpty(value){
    return value //	👈 null and undefined check
    	&& Object.keys(value).length === 0
    	&& value.constructor === Object
  }
  ```

  为什么要加 `value.constructor === Object` 呢？

  因为不加这个条件，`JS` 内置的对象也会被判断为空对象，如下：

  ```js
  function badEmptyCheck(value) {
    return Object.keys(value).length === 0;
  }
  badEmptyCheck(new String());    // true 😱
  badEmptyCheck(new Number());    // true 😱
  badEmptyCheck(new Boolean());   // true 😱
  badEmptyCheck(new Array());     // true 😱
  badEmptyCheck(new RegExp());    // true 😱
  badEmptyCheck(new Function());  // true 😱
  badEmptyCheck(new Date());      // true 😱
  ```

- 在低版本的浏览器中可以这样使用

  ```js
  function isEmpty(value){
  	return Object.prototype.toString.call(value) === '[object Object]'
    	&& JSON.stringify(value) === '{}'
  }
  ```

## 11、JS 简易适配各种移动端页面方案

```js
/**
	* 动态调整 html 的 font-size
	* @param {string} el 目标元素
	* @param {number} maxWidth 设计稿最大宽度
	* @param {number} aliquot 分为多少等份
*/
function scales(el, maxWidth, aliquot) {
  let width;
  let htmlEl = document.documentElement

  width = document.querySelector(el).offsetWidth
  if (!width) {
    width = htmlEl.offsetWidth
  }

  const scale = width / maxWidth

  htmlEl.style.fontSize = aliquot * scale + 'px'
}

scales('.container', 375, 100)	//	意思就是把 375px 的设计稿分成 100 等份，每一等份为 1 rem
```

通过第一次加载 `JS` 后根据参照物调整 `html` 的 `font-size` ，然后结合 `CSS` 中的 `rem` 单位，达到适配各种移动端页面的操作

