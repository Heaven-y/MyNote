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

  
  



## Reflect

有点像Object操作对象的方法

* Reflect.getPrototypeOf(target)     Reflect.defineProperty(target, propertyKey, attributes)

为了对 对象本身的操作更加规范，让这些操作都集中到Reflect对象上

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




