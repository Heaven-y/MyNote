字面量赋值

* 对象的引用赋值时会进行**freshness擦除**

  * 多余的类型擦除，剩下类型能匹配时即可赋值

  * ```typescript
    interface IPerson {
      name: string
      age: number
      height: number
    }
    function printInfo(person: IPerson) {
      console.log(person)
    }
    cosnt info = {
      name: "me",
      age: 18,
      height: 1.88,
      address: "武汉"
    }
    // 不能直接传对象
    printInfo(info)
    ```



枚举类型

* 默认有值，从数字0开始递增



### 泛型

进行类型的参数化







封装Axios



































