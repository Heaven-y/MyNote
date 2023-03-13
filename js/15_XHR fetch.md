①服务器端渲染(`SSR`,server side render)

* 后端渲染
* 客户端发出请求 -> 服务端接收请求并返回相应HTML文档 -> 页面刷新，客户加载
  * 通过jsp(java server page)：html+css+java 渲染。              java代码：从数据库中读取数据，并将它动态放在页面中。完成后直接传给客户端，**不用加载js和css。利于SEO优化**

②客户端渲染

* 前后端分离
* 浏览器通过url到`静态资源服务器`拿到html+css+js。由浏览器执行js代码后**(Ajax   xhr/fetch)**，到`提供API接口的服务器`请求到数据，再执行其他js代码将请求到的数据渲染到网页中。
  * 后端只提供API来返回数据。前端用JS将数据渲染到页面。

③SPA(Single-Page Application)单页富应用：

* 整个网页只有一个html页面
* 在前后端分离的基础上加了一层**前端路由**
  * 一次性请求了所有资源，根据url决定渲染哪一部分资源
* **核心：改变URL，不刷新页面**



## HTTP

HyperText Transfer Protocol

### 各个版本



### 请求方式

* GET：GET 方法请求一个指定资源的表示形式，使用 GET 的请求**应该只被用于获取数据**

* HEAD：HEAD 方法请求一个与 GET 请求的响应相同的响应，但没有响应体
  * 比如在准备下载一个文件前，先获取文件的大小，再决定是否进行下载

* POST：POST 方法用于将实体提交到指定的资源

* DELETE：DELETE 方法删除指定的资源
* PATCH：PATCH 方法用于对资源应部分**修改**
* PUT：PUT 方法用请求有效载荷（payload）**替换**目标资源的所有当前表示

* CONNECT：CONNECT 方法建立一个到目标资源标识的服务器的隧道，通常用在代理服务器，网页开发很少用到

* TRACE：TRACE 方法沿着到目标资源的路径执行一个消息环回测试



请求报文

响应报文

Content-type：这次请求携带的数据的类型(**默认FormData格式**)

* application/x-www-form-urlencoded：表示数据被编码成以 '&' 分隔的键 - 值对，同时以 '=' 分隔键和值
* application/json
* text/plain
* application/xml
* multipart/form-data



### 响应状态码

https://developer.mozilla.org/zh-CN/docs/web/http/status

| 常见HTTP状态码 | 状态描述              | 信息说明                                             |
| -------------- | --------------------- | ---------------------------------------------------- |
| 200            | OK                    | 请求成功                                             |
| 201            | Created               | POST请求，创建新的资源                               |
| 301            | Moved Permanently     | 请求资源的URL已经修改，响应中会给出新的URL           |
| 400            | Bad Request           | 客户端的错误，服务器无法或者不进行处理               |
| 401            | Unauthorized          | 未授权的错误，必须携带请求的身份信息                 |
| 403            | Forbidden             | 客户端没有权限访问，被拒接                           |
| 404            | Not Found             | 服务器找不到请求的资源                               |
| 500            | Internal Server Error | 服务器遇到了不知道如何处理的情况                     |
| 503            | Service Unavailable   | 服务器不可用，可能处理维护或者重载状态，暂时无法访问 |



### AJAX

Asynchronous JavaScript And XML



#### XHR

##### XHR状态码

xhr.readyState -> XHR状态码     XMLHttpRequest.DONE -> XHR状态

xhr.status -> HTTP响应状态码	xhr.statusText -> HTTP状态描述

| 值   | 状态             | 描述                                            |
| ---- | ---------------- | ----------------------------------------------- |
| 0    | UNSENT           | 代理被创建，但尚未调用 open() 方法              |
| 1    | OPENED           | open() 方法已经被调用                           |
| 2    | HEADERS_RECEIVED | send() 方法已经被调用，并且头部和状态已经可获得 |
| 3    | LOADING          | 下载中；responseText 属性已经包含部分数据       |
| 4    | DONE             | 下载操作已完成                                  |



##### 其他事件监听

 除了onreadystatechange还有其他的事件可以监听

* loadstart：请求开始
* progress： 一个响应数据包到达，此时整个 response body 都在 response 中。
* abort：调用 xhr.abort() 取消了请求。
* error：发生连接错误，例如，域错误。不会发生诸如 404 这类的 HTTP 错误
* load：请求成功完成
* timeout：由于请求超时而取消了该请求（仅发生在设置了 timeout 的情况下）
* loadend：在 load，error，timeout 或 abort 之后触发
* timeout：由于请求超时而取消了该请求（仅发生在设置了 timeout 的情况下）
* loadend：在 load，error，timeout 或 abort 之后触发



##### 响应数据和状态

* xhr.response: 返回响应的正文内容
* xhr.responseType:返回的类型取决于responseType的属性设置





##### 传递参数

* GET请求的query参数
  * xhr.open("get", "http://123.207.32.32:1888/02_param/get?name=why&age=18&address=广州市")
* POST请求的JSON格式
  * xhr.open("post", "http://123.207.32.32:1888/02_param/postjson")
  * xhr.setRequestHeader("Content-type", "application/json")
  * xhr.send(JSON)
* POST请求的FormData格式
  * xhr.open("post", "http://123.207.32.32:1888/02_param/postform")
  * 将HTML中的Form元素对象转成FormData对象
    * const formData = **new FormData(formEl)**
  * xhr.send(formData )
* POST请求的 x-www-form-urlencoded格式
  * xhr.open("post", "http://123.207.32.32:1888/02_param/posturl")
  * xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
  * xhr.send("name=me&age=18&address=武汉市")
    * 放到请求体body





##### 综合使用

```js
//1.创建XMLHttpRequest对象
const xhr = new XMLHttpRequest()

//2.监听XMLHttpRequest对象状态变化或监听onload事件
	//onreadystatechagne(宏任务)
xhr.onreadystatechange = function() {
  // XMLHttpRequest.DONE -> 状态码4
  if (xhr.readyState !== XMLHttpRequest.DONE) return 
  
  	// 响应数据在response
  console.log(xhr.response)
}
  //	onload监听数据加载完成
xhr.onload = function() {
  console.log(xhr.status, xhr.statusText)
  // 根据http的状态码判断是否请求成功
  if (xhr.status >= 200 && xhr.status < 300) {
    console.log(xhr.response)
  } else {
    console.log("请求不成功", xhr.status, xhr.statusText)
  }
}
	//	onerroe监听错误
xhr.onerror = function() {
  console.log("onerror", xhr.status, xhr.statusText)
}

//3.告知xhr获取到的数据的类型
xhr.responseType = "json"
//xhr.responseType = "xml"


//4.配置网络请求(open方法)
	// open第三个参数Boolean值决定是否发送同步请求
//xhr.open("get", "http://123.207.32.32:1888/01_basic/hello_json")
//xhr.open("get", "http://123.207.32.32:1888/01_basic/hello_xml")                   
//5.发送send网络请求
//xhr.send()


//4.1	GET请求的query参数
//xhr.open("get", "http://123.207.32.32:1888/02_param/get?name=why&age=18&address=广州市")
//xhr.send()

//4.2	POST请求的JSON格式
xhr.open("post", "http://123.207.32.32:1888/02_param/postjson")
xhr.setRequestHeader("Content-type", "application/json")
const JSONString = JSON.stringify({name: "me", age: 18, height: 1.88})
xhr.send(JSONString)

//4.3	POST请求的FormData格式
//xhr.open("post", "http://123.207.32.32:1888/02_param/postform")
	//将HTML中的Form元素对象转成FormData对象
//const formData = new FormData(formEl)
//xhr.send(formData )

//4.4 请求的 x-www-form-urlencoded格式
//xhr.open("post", "http://123.207.32.32:1888/02_param/posturl")
//xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
	//放到请求体body
//xhr.send("name=me&age=18&address=武汉市")
```





##### 请求封装

```js
function myAjax({
  url,
  method = "get",
  data = {},
  headers = {}, // token
  timeout = 10000
} = {}) {
  const xhr = new XMLHttpRequest()
  
  const promise = new Promise((resolve, reject) => {
    xhr.onload = function() {
      if (xhr.status >= 200 && xhr.status < 300) {
        resolve(xhr.response)
      } else {
        reject({ status: xhr.status, message: xhr.statusText })
      }
    }

    xhr.responseType = "json"
    xhr.timeout = timeout
    // 到达过期时间还没获取到相应结果时，取消本次请求
    xhr.ontimeout = function() {
      console.log("请求已经过期")
    }
    

    if (method.toUpperCase() === "GET") {
      // 拼接成 key=value
      const queryStrings = Object.keys(data).map(key => `${key}=${data[key]}`)
      // ?key=value&key=value
      url = url + "?" + queryStrings.join("&")
      xhr.open(method, url)
      xhr.send()
    } else {
      xhr.open(method, url)
      // 设置请求头
      xhr.setRequestHeader("Content-type", "application/json")
      xhr.send(JSON.stringify(data))
    }
  })
  
  promise.xhr = xhr
  return promise
}
```





#### Fetch

返回值是一个Promise

```js
//默认get请求
fetch("http://123.207.32.32:8000/home/multidata").then(res => {
  // 1.获取到response
  const response = res
  // 2.获取到具体结果
  //response.text()
  return response.json()
}).then(res => {
  // 3.真正的结果
  console.log("res", res)
}).catch(err => {
  console.log(err)
})
```

改进

```js
async funtion getData() {
  // 如果没设置Content-type，body传入formData
  const formData = new FormData()
  formData.append("name", "me")
  formData.append("age", 18)
  
	const response = await fetch("http://123.207.32.32:1888/02_param/postjeson", {
    method: "post",
    headers: {
     	"Content-type": "application/json" 
    },
    // body: formData
    body: JSON.stringify({
      name: "me",
      age: 18
    })
  })
  const res = await response.json()
  console.log("res", res)
}
getData()
```





### 文件上传

XHR能监听进度，fetch没有提供该API

发送POST请求

* GET请求 -> base64

#### XHR

```js
// <input class="file" type="file">
// <button class="upload">上传文件</button>

const uploadBtn = document.querySelector(".upload")
const fileEl = document.querySelector(".file")
uploadBtn.onclick = function() {
  // 通过files属性获取文件
  if (!fileEl.files.length) {
    alart("请选择要上传的文件")
    return 
  }
  const file = fileEl.files[0]
  // 存入表单
  const formData = new FormData()
  formData.appen("avatar", file)
  
  
  const xhr = new XMLHttpRequest
  // 监听进度
  xhr.upload.onprogress = function(event) {
    console.log(`${event.loaded}/${event.total}`)
  }
  xhr.onload = function() {
    console.log(xhr.response)
  }
  
  xhr.responseType = "json"
  
  xhr.open("post", "http://123.207.32.32:1888/02_param/upload")
  xhr.send(formData)
}
```





#### Fetch

```js
uploadBtn.onclick = async function() {
  // 获取文件与XHR相同
  
  const response = await fetch("http://123.207.32.32:1888/02_param/upload", {
    method: "post",
    body: formData
  })
  const res = await response.json()
}
```

