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



