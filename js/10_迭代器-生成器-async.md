### Iterator

使用户在容器对象（container，例如链表或数组）上遍访的对象，使用该接口无需关心对象的内部实现细节

即帮助我们对某个数据结构进行遍历的对象

在JavaScript中，迭代器也是一个具体的对象，这个对象需要符合迭代器协议（iterator protocol）

* 迭代器协议定义了产生一系列值（无论是有限还是无限个）的标准方式

* 这个标准就是一个特定的next方法：一个无参数或者一个参数的函数，返回一个应当拥有以下两个属性的对象
  * done：boolean
    * 如果迭代器可以产生序列中的下一个值，则为 false
    * 如果迭代器已将序列迭代完毕，则为 true。
  * value
    * 迭代器返回的任何 JavaScript 值

```js
const names = [1.5, 8, 2, 6, 7]
function creatArrayIterator(arr) {
  let index = 0
  return {
    next() {
      if (index < arr.length) {
        return { done: false, value: arr[index++] }
      } else {
        return { done: true }
      }
    }
  }
}

const namesIterator = creatArrayIterator(names)
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())

```



#### 迭代器的中断

* 除了next方法外，还可以添加return方法



#### 可迭代对象

它和迭代器是不同的概念

* 当一个对象实现了iterable protocol协议时，它就是一个可迭代对象
*  这个对象的要求是必须实现 @@iterator 方法，在代码中我们使用[Symbol.iterator]访问该属性
  * 当一个对象变成一个可迭代对象的时候，就可以进行某些迭代操作
  * 比如for...of操作时，就会调用它的 @@iterator 方法

```js
const infos = {
  name: 'info',
  age: 18,
  height: 1.88,
  // 1.必须实现一个特定的函数: [Symbol.iterator]
  [Symbol.iterator]() {
    const entries = Object.entries(this)
    let index = 0
    // 2.这个函数需要返回一个迭代器(这个迭代器用于迭代当前的对象)
    return {
      next() {
        if (index < entries.length) {
          return { done: false, value: entries[index++] }
        } else {
          return { done: true }
        }
      }
    }
  }
}

// const infosIterator = infos[Symbol.iterator]()
// console.log(infosIterator.next())
// console.log(infosIterator.next())
// console.log(infosIterator.next())
// console.log(infosIterator.next())
for (const entries of infos) {
  const [key, value] = entries
  console.log(key, value)
}
```





### Generator

ES6中新增的一种函数控制、使用的方案

* 需要在function的后面加一个符号：* 
* 通过yield关键字来控制函数的执行流程
* 函数的返回值是一个Generator（生成器）
  * MDN：生成器事实上是一种特殊的迭代器



```js
function* foo(initial) {
  console.log("函数开始执行")
  const value1 = yield initial + '11'
  const value2 = yield value1 + '22'
  const value3 = yield value2 + '33'
}

const generator = foo('lq')
const result1  = generator.next()
console.log("result1: ", result1) //result1:  { value: 'lq11', done: false }
const result2  = generator.next(result1.value)
console.log("result2: ", result2) // result2:  { value: 'lq1122', done: false }
const result3  = generator.next(result2.value)
console.log("result3: ", result3) // result3:  { value: 'lq112233', done: false }
```

**调用return传值后这个生成器函数就会结束，之后调用next不会继续生成值了**

```js
const result2  = generator.return(result1.value)
console.log("result2: ", result2) // result2:  { value: 'lq11', done: true }
const result3  = generator.next(result2.value)
console.log("result3: ", result3) // result3:  { value: undefined, done: true }
```





#### 生成器替代迭代器

```js
const names = [1.5, 8, 2, 6, 7]
function* creatArrayIterator(arr) {
  for(const item of arr)
    yield item
}

const namesIterator = creatArrayIterator(names)
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
```

还可以使用yield\*来生产一个可迭代对象

* 相当于是一种yield的语法糖，只不过会依次迭代这个可迭代对象，每次迭代其中的一个值

```js
function* creatArrayIterator(arr) {
  yield* arr
}
```



#### 自动执行Generator函数

```js
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(url)
    }, 2000)
  })
}
// 生成器的处理方案
function* getData() {
  const res1 = yield requestData("why")
  console.log("res1:", res1)

  const res2 = yield requestData(res1 + "kobe")
  console.log("res2:", res2)

  const res3 = yield requestData(res2 + "james")
  console.log("res3:", res3)

  const res4 = yield requestData(res3 + "curry")
  console.log("res4:", res4)
}

function execGenFn(genFn) {
  // 1.获取对应函数的generator
  const generator = genFn()
  // 2.定义一个递归函数
  function exec(res) {
    // result -> { done: true/false, value: 值/undefined }
    const result = generator.next(res)
    if (result.done) return
    result.value.then(res => {
      exec(res)
    })
  }
  // 3.执行递归函数
  exec()
}

execGenFn(getData)
```







### async  await

相当于生成器和Promise结合的语法糖



异步函数有返回值时

* 返回值相当于被包裹到Promise.resolve中
* 异步函数的返回值是Promise，状态由会由Promise决定
* 异步函数的返回值是一个对象并且实现了thenable，那么会由对象的then方法来决定



在async中抛出了异常，那么程序它并不会像普通函数一样报错，而是会作为Promise的reject来传递



await会等到代码后面Promise的状态变成fulfilled状态，之后继续执行异步函数

* 后面是一个普通的值，那么会直接返回这个值
* 后面是一个thenable的对象，那么会根据对象的then方法调用来决定后续的值
* **返回的Promise是reject的状态，那么会将这个reject结果直接作为函数的Promise的reject值**

