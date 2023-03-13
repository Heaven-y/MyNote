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



## 优先级

* 默认规则的优先级最低
* new绑定优先级 > bind >apply/call > 隐式绑定
* new不可以和apply/call一起使用



## 题目

```js
var name = "window";

var person = {
  name: "person",
  sayName: function () {
    console.log(this.name);
  }
};

function sayName() {
  var sss = person.sayName;
  sss();
  person.sayName();
  (person.sayName)();
  (b = person.sayName)();
}

sayName();
// 绑定: 默认绑定, window -> window
// 绑定: 隐式绑定, person -> person
// 绑定: 隐式绑定, person -> person
// 术语: 间接函数引用, window -> window
```



```js
var name = 'window'

var person1 = {
  name: 'person1',
  foo1: function () {
    console.log(this.name)
  },
  foo2: () => console.log(this.name),
  foo3: function () {
    return function () {
      console.log(this.name)
    }
  },
  foo4: function () {
    // console.log(this) // 第一个表达式this -> person1
    // console.log(this) // 第二个表达式this -> person2
    // console.log(this) // 第三个表达式this -> person1
    
    return () => {
      console.log(this.name)
    }
  }
}

var person2 = { name: 'person2' }



person1.foo1(); // 隐式绑定: person1
person1.foo1.call(person2); // 显式绑定: person2

person1.foo2(); // 上层作用域: window
person1.foo2.call(person2); // 上层作用域: window

person1.foo3()(); // 默认绑定: window
person1.foo3.call(person2)(); // 默认绑定: window
person1.foo3().call(person2); // 显式绑定: person2

person1.foo4()(); // person1
person1.foo4.call(person2)(); // person2
person1.foo4().call(person2); // person1
```



```js
var name = 'window'

function Person(name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  },
  this.foo2 = () => console.log(this.name),
  this.foo3 = function () {
    return function () {
      console.log(this.name)
    }
  },
  this.foo4 = function () {
    return () => {
      console.log(this.name)
    }
  }
}

// person1/person都是对象(实例instance)
var person1 = new Person('person1')
var person2 = new Person('person2')


// 面试题目:
person1.foo1() // 隐式绑定: person1
person1.foo1.call(person2) // 显式绑定: person2

person1.foo2() // 上层作用域查找: person1
person1.foo2.call(person2) // 上层作用域查找: person1

person1.foo3()() // 默认绑定: window
person1.foo3.call(person2)() // 默认绑定: window
person1.foo3().call(person2) // 显式绑定: person2

person1.foo4()() // 上层作用域查找: person1(隐式绑定)
person1.foo4.call(person2)() //  上层作用域查找: person2(显式绑定)
person1.foo4().call(person2) // 上层作用域查找: person1(隐式绑定)
```



```js
var name = 'window'

function Person(name) {
  this.name = name
  this.obj = {
    name: 'obj',
    foo1: function () {
      return function () {
        console.log(this.name)
      }
    },
    foo2: function () {
      return () => {
        console.log(this.name)
      }
    }
  }
}

var person1 = new Person('person1')
var person2 = new Person('person2')

person1.obj.foo1()() // 默认绑定: window
person1.obj.foo1.call(person2)() // 默认绑定: window
person1.obj.foo1().call(person2) // 显式绑定: person2

person1.obj.foo2()() // 上层作用域查找: obj(隐式绑定)
person1.obj.foo2.call(person2)() // 上层作用域查找: person2(显式绑定)
person1.obj.foo2().call(person2) // 上层作用域查找: obj(隐式绑定)
```

