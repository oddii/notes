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

