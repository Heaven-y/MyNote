* this的绑定和定义的位置(编写的位置)没有关系
* this的绑定和调用方式以及调用的位置有关
* this是在运行时被绑定的



### 默认绑定

* 独立函数调用 this指向window
  * 严格模式下("use strict") this指向undefined



### 隐式绑定

* 通过某个对象发起的函数调用 this指向这个对象
* 前提条件：必须在调用的对象内部有一个对函数的引用(比如一个属性)



### 显式绑定

不希望在对象内部包含对这个函数的引用，同时又希望在这个对象上进行强制调用

* apply和call

  * (传入this指向, arg)
    * 后面的参数，apply为数组，call为参数列表

  * 如果传入的this不是对象类型，会创建一个包装类对象
    * 严格模式不会创建包装类对象

* bind

  * 会创建一个新的绑定函数,后面的参数为参数列表

  * ```js
    function foo() {
      console.log("foo", this)
    }
    let obj = { name: "bbbb" }
    let bar = foo.bind(obj)
    bar() // this -> obj
    ```

**如果在显式绑定中，传入一个null或者undefined，那么这个显式绑定会被忽略，使用默认规则**

* 严格模式使用基本数据类型



### new绑定

new的过程

* 在内存中创建一个新的对象(空对象)
* 这个对象内部的[[prototype]]属性会被赋值为该构造函数的prototype属性
* 构造函数内部的this，会指向创建出来的新对象
* 执行函数的内部代码(函数体代码)
* 如果构造函数没有返回非空对象，则返回创建出来的新对象



### 规则之外

* 间接函数引用
  * (obj2.foo = obj1.foo)()     默认绑定，指向window

* 箭头函数
  * 箭头函数不会绑定this、arguments属性
  * 箭头函数不能作为构造函数使用(和new一起使用会报错)



### 手写apply-call

```js
function foo(name, age) {
  console.log(this, name, age);
}
Function.prototype.excuteFn = function(thisArg, otherArgs) {
  thisArg = (thisArg ==='null' || thisArg === 'undefined') ? window : Object(thisArg)
  Object.defineProperty(thisArg, 'fn', {
    configurable: true,
    value: this
  })
  thisArg.fn(...otherArgs)
  delete thisArg.fn
}
Function.prototype.lqapply = function(thisArg, otherArgs) {
  this.excuteFn(thisArg, otherArgs)
}
Function.prototype.lqcall = function(thisArg, ...otherArgs) {
  this.excuteFn(thisArg, otherArgs)
}
foo.lqapply('aaa', ['kobe', 18])
foo.lqapply(undefined, ['james', 28])
foo.lqcall(123, 'ssss', 12)
```

### 手写bind

```js
function foo(name, age) {
  console.log(this, name, age);
}
Function.prototype.lqbind = function(thisArg, ...otherArgs) {
  thisArg = (thisArg ==='null' || thisArg === 'undefined') ? window : Object(thisArg)
  Object.defineProperty(thisArg, "fn", {
    configurable: false,
    value: this
  })
  return (...newArgs) => {
    thisArg.fn(...[...otherArgs, ...newArgs])
  }
} 
foo.lqbind(213, "kkkk")(20)
```





## 优先级

* 默认规则的优先级最低
* new绑定优先级 > bind >apply/call > 隐式绑定
* new不可以和apply/call一起使用
