三种状态

* pending：执行executor中的代码时
* fulfilled：执行了resolve
* rejected：执行了reject
* 状态一旦被确定，就不会再更改
  * 并不是后面的代码不会执行，而是无法改变Promise的状态

**如果resolve/reject中传入是Promise对象，那么当前的Promise的状态会由传入的Promise决定**

**如果resolve/reject中传入了一个有实现then方法的对象，那么会执行then方法，并根据其结果决定Promise状态**



**finally**:无论前面是什么状态，最终都会执行



### 类方法

**all**：

状态由[]中的Promise共同决定

* 当所有Promise状态变成fulfilled时，其状态为fulfilled，并且将所有的Promise返回值组成一个数组
* 当有一个Promise状态为rejected时，其状态为rejected，并且将第一个reject的返回值作为参数

```js
Promise.all([
  new Promise((resolve,reject) => {
    setTimeout(() => {
      resolve({name:'lq',age:18})
    },2000)
  }),
  new Promise((resolve,reject) => {
    setTimeout(() => {
      resolve({name:'kobe',age:39})
    })
  })
]).then(results => {
  console.log(results)
})
```



**allSettled**:当所有Promise都有结果时，才会有状态，其状态为fulfilled，并且返回一个对象数组[{status, value/reason}]

**any**:第一个fulfilled，若所有的都是rejected，则会执行catch

**race**



### 手写Promise

