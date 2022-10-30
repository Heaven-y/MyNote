### 常见的属性和方法

* Storage.length:返回一个整数，表示存储在Storage对象中的数据项数量
* Storage.getItem(key)
* Storage.setItem(key, value)
* Storage.removeItem(key)
* Storage.clear()
* Storage.key(index)



#### localStorage

本地存储，提供的是一种永久性的存储方法，关闭网页重新打开，存储的内容依然保留



#### sessionStorage

会话存储，提供的是本次会话的存储，在关闭掉会话时，存储的内容会被删除



#### 区别

* 关闭网页后重新打开，localStorage会保留，而sessionStorage会被删除
* 在页面内实现跳转，localStorage会保留，sessionStorage也会保留
* 在页面外实现跳转（打开新的网页），localStorage会保留，sessionStorage不会被保留



#### 封装Cache工具类

**Storage本身不能直接存储对象类型的**

```js
class Cache {
  constructor(isLocal = true) {
    this.storage = isLocal ? localStorage : sessionStorage
  }
  setCache(key, value) {
    if (!value) {
      throw new Error("value error: value必须有值")
    }
    // 对象转换成JSON格式存储
    this.storage.setItem(key, JSON.stringify(value))
  }
  getCache(key) {
    const result = this.storage.getItem(key)
    if (result) {
      // undefined不能用JSON.parse
      return JSON.parse(result)
    }
  }
  removeCache(key) {
    this.storage.removeItem(key)
  }
  clear() {
    this.storage.clear()
  }
}
const localCache = new Cache()
const sessionCache = new Cache(false)
```



### 正则表达式

Regular Expression

* 匹配的规则pattern
* 匹配的修饰符flag : g i m(多行匹配)



#### 使用方法

```js
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"

// 1.正则对象上的实例方法
// 1.1 test：一个在字符串中测试是否匹配的 RegExp 方法，它返回 true 或 false
if (re1.test(message)) {
  console.log("message符合规则")
}

// 1.2 exec：一个在字符串中执行查找匹配的 RegExp 方法，它返回一个数组(未匹配到则返回 null)
console.log(re1.exec(message))

// 2.使用字符串的方法，传入一个正则表达式
// 2.1 match：一个在字符串中执行查找匹配的 String 方法，它返回一个数组(在未匹配到时会返回 null)
console.log(message.match(re1))

// 2.2 matchAll：一个在字符串中执行查找所有匹配的 String 方法，它返回一个迭代器（iterator）
//		 matchAll传入的正则修饰符必须加g
const iterator = message.matchAll(re1)
console.log(iterator.next())
console.log(iterator.next())
for (const item of iterator) {
  console.log(item)
}
// 2.3 replace/replaceAll方法
// 2.4 split方法
// 2.5 search方法
```





#### 字符类

* \d (digit)：数字：从0到9的字符
  * 和[0-9]相同
* \s (space)：空格符号：包括空格，制表符 \t，换行符 \n 和其他少数稀有字符，例如 \v，\f 和 \r
* \w (word)：“单字”字符：拉丁字母或数字或下划线 _
  * 和 [a-zA-Z0-9_] 相同

* 反向类
  * \D 非数字：除 \d 以外的任何字符，例如字母
    * 和\[^0-9\]相同
  * \S 非空格符号：除 \s 以外的任何字符，例如字母
  * \W 非单字字符：除 \w 以外的任何字符，例如非拉丁字母或空格



#### 锚点

* ^ 匹配文本开头
* $ 匹配文本末尾
* \b : 词边界，它会检查字符串中的位置是否是词边界



#### 集合和范围

* 在方括号 […] 中的几个字符或者字符类意味着搜索给定的字符中的任意一个
* [a-z] 会匹配从a到z范围内的字母，[0-9]表示从0到9的数字

**除了普通的范围匹配，还有类似 \[^…\] 的“排除”范围匹配**



#### 量词

* {3, 5}表示3个到5个
* +：代表一个或多个，相当于 {1,}
* ?：代表零个或一个，相当于 {0,1}
* *：代表着零个或多个，相当于 {0,}



#### 贪婪和懒惰

* 贪婪模式：默认情况下的匹配规则是查找到匹配的内容后，会继续向后查找，一直找到最后一个匹配的内容
* 懒惰模式：只要获取到对应的内容后，就不再继续向后匹配
  * 可以在量词后面再加一个问号 ‘?’ 来启用它

```js
const message = "我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》"

// 默认.+采用贪婪模式
const nameRe1 = /《.+》/ig
console.log(message.match(nameRe1))
// ["《黄金时代》和《沉默的大多数》、《一只特立独行的猪》"]

// 使用惰性模式
const nameRe2 = /《.+?》/ig
//['《黄金时代》', '《沉默的大多数》', '《一只特立独行的猪》']
```



#### 捕获组

模式的一部分可以用括号括起来 (...)

* 它允许将匹配的一部分作为结果数组中的单独项
* **方法 str.match(regexp)，如果 regexp 没有 g 标志，将查找第一个匹配并将它作为一个数组返回**
  *  在索引 0 处：完全匹配
  * 在索引 1 处：第一个括号的内容
* 命名组 在开始括号之后立即放置?\<name\>
  *  (?\<name\>)
* 非捕获组 在开始括号之后立即放置?:
  * (?:)

```js
const message = "我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》"
// 使用惰性模式
const nameRe = /(《)(?<lq>.+?)(?:》)/ig
const iterator = message.matchAll(nameRe)
const value = iterator.next().value
console.log(value)
// ['《黄金时代》', '《', '黄金时代', index: 10, input: '我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》', groups: {…}]
console.log(value.groups.lq)
// '黄金时代'
```



#### 封装歌词解析工具

```js
const lyricString = "[00:00.000] 作词 : 许嵩\n[00:01.000] 作曲 : 许嵩\n[00:02.000] 编曲 : 许嵩\n[00:22.240]天空好想下雨\n[00:24.380]我好想住你隔壁\n[00:26.810]傻站在你家楼下\n[00:29.500]抬起头数乌云\n[00:31.160]如果场景里出现一架钢琴\n[00:33.640]我会唱歌给你听\n[00:35.900]哪怕好多盆水往下淋\n[00:41.060]夏天快要过去\n"

function parseLyric(lyricString) {
  const lyricLineStrings = lyricString.spilt("\n")
	// [00:00.000] 作词 : 许嵩  ->  { time:0, content: "作词 : 许嵩" }
  // 针对每一行歌词进行解析
  const timeReg = /\[(\d{2}):(\d{2})\.(\d{2,3})\]/i
  const lyricInfos = []
  for (const lineString of lyricLineStrings) {
    const result = lineString.match(timeReg)
    if(!result) continue
    const minuteTime = result[1] * 60 * 1000
    const secondTime = result[2] * 1000
    const mSecondTime = result[3].length === 3 ? result[3] * 1 : result[3] * 10
    const time = minuteTime + secondTime + mSecondTime
    // 取得内容
    const content = lineString.replace(timeReg, "").trim()
    // 存入数组
    lyricInfos.push({ time, content})
  }
  return lyricInfos
}
const infos = parseLyric(lyricString)
console.log(infos)
```



#### 封装时间格式工具

```js
function formatDate(timestamp, fmtString) {
  const date = new Date(timestamp)
  const dateO = {
    "y+": date.getFullYear(),
    "M+": date.getMonth() + 1,
    "d+": date.getDate(),
    "h+": date.getHours(),
    "m+": date.getMinutes(),
    "s+": date.getSeconds()
  }
  for (const key in dateO) {
    const keyReg = new RegExp(key)
    if (keyReg.test(fmtString)) {
      // 5 ->  05
      const value = (dateO[key] + "").padStart(2, "0")
      fmtString = fmtString.replace(keyReg, value)
    }
  }
  return fmtString
}
console.log(formatDate(1665475530187, "yyyy-MM-dd hh:mm:ss"))
```





更多

**https://c.runoob.com/front-end/854/**

