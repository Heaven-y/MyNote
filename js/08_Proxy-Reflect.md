## Proxy

监听对象的操作

* Object.defineProperty(obj, key, {})

  * 初衷并不是为了监听，而是定义普通的属性
  * 如果想监听更丰富的操作，例如新增、删除属性等操作，它是无能为力的

* 代理对象      new Proxy(target, handler)

  * set(target, key, value, receiver)  receiver就是外层proxy对象

  * get(target, key, receiver) 

  * has(target, key)

  * deleteProperty(target, key)



https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots

| 内部方法            | Handler方法    | 触发条件                                                     |
| ------------------- | -------------- | ------------------------------------------------------------ |
| [[Get]]             | get            | 读取属性                                                     |
| [[Set]]             | set            | 写入属性                                                     |
| [[HasProperty]]     | has            | in 运算符                                                    |
| [[Delete]]          | deleteProperty | delete 操作                                                  |
| [[Call]]            | apply          | proxy对象作为函数被调用                                      |
| [[Construct]]       | construct      | new操作                                                      |
| [[GetPrototypeOf]]  | getPrototypeOf | Object.getPrototypeOf                                        |
| [[SetPrototypeOf]]  |                |                                                              |
| [[OwnPropertyKeys]] | ownKeys        | Object.getOwnPropertyNames<br/>Object.getOwnPropertySymbols<br />for...in<br />Object.keys/values/entries |

`Object.getOwnPropertyNames(obj)` 返回非 Symbol 键

`Object.getOwnPropertySymbols(obj)` 返回 symbol 键

`Object.keys/values()` 返回带有 `enumerable` 标记的非 Symbol 键值对

`for..in` 循环遍历所有带有 `enumerable` 标记的非 Symbol 键，以及原型对象的键





可取消的proxy

const {proxy, revoke} = Proxy.revocable(target, handler)

```js
const object = {
  data: "Valuable data"
};

const {proxy, revoke} = Proxy.revocable(object, {});

// proxy 正常工作
console.log(proxy.data); // Valuable data

// 之后某处调用
revoke();

// proxy 不再工作（已吊销）
console.log(proxy.data); // Error
```





## Reflect

有点像Object操作对象的方法

* Reflect.getPrototypeOf(target)     Reflect.defineProperty(target, propertyKey, attributes)

为了对 对象本身的操作更加规范，让这些操作都集中到Reflect对象上



对于每个可被 Proxy 捕获的内部方法，Reflect 都有一个对应的方法 Reflect，其名称和参数与 Proxy 钩子相同。

```js
const obj = {
  _name: "lq",
  set name(newValue) {
    console.log("this:", this) //默认是obj
    this._name = newValue
  },
  get name() {
    return this._name
  }
}
const objProxy = new Proxy(obj, {
  set(target, key, newValue, receiver) {
    // 1.好处一: 代理对象的目的: 不再直接操作原对象
    // 2.好处二: Reflect.set方法有返回Boolean值, 可以判断本次操作是否成功
    /*
       3.好处三:
         > receiver就是外层Proxy对象
         > Reflect.set/get最后一个参数, 可以决定对象访问器setter/getter的this指向
    */
    console.log("proxy中设置方法被调用")
    const isSuccess = Reflect.set(target, key, newValue, receiver)
    if (!isSuccess) {
      throw new Error(`set ${key} failure`)
    }
  },
  get(target, key, receiver) {
    console.log("proxy中获取方法被调用")
    return Reflect.get(target, key, receiver)
  }
})
// 操作代理对象
objProxy.name = "kobe"
console.log(objProxy.name)
```



和construct结合

```js
function Person(name, age) {
  this.name = name
  this.age = age
}

function Student(name, age) {
  // Person.call(this, name, age)
  const _this = Reflect.construct(Person, [name, age], Student)
  return _this
}

// const stu = new Student("why", 18)
const stu = new Student("why", 18)
console.log(stu)
console.log(stu.__proto__ === Student.prototype)
```



