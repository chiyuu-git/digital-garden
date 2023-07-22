---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-protocol/fetch/"}
---


# *Ajax

## 概述

Ajax：Asynchronous Javascript And XML

Ajax 的作用：获取服务器的数据

Ajax 的效果：在不刷新整个页面的情况下，通过一个 url 地址获取服务器的数据，然后进行页面的局部刷新

异步加载，加载数据的时候，还可以操作页面

## XHR 的使用

Ajax 简单的来说，就是一个异步的 javascript 请求，用来获取后台服务端的数据，而并不是整个界面进行跳转。

在 JS 中实现 Ajax 主要的类就是 XMLHttpRequest，它的使用一般有四个步骤：

1. 创建 XMLHttpRequest 对象
2. 准备发送网络请求
3. 开始发送网络请求
4. 指定回调函数

## XHR 对象的属性

### XMLHttpRequest.onreadystatechange

**XMLHttpRequest.onreadystatechange** 会在 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 的 [`readyState`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/readyState) 属性发生改变时触发 `readystatechange` 事件的时候被调用。

当一个 `XMLHttpRequest` 请求被 [abort()](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/abort) 方法取消时，其对应的 `readystatechange` 事件不会被触发。

### XHR.readyState 只读

**XMLHttpRequest.readyState** 属性返回一个 XMLHttpRequest 代理当前所处的状态。一个 XHR 代理总是处于下列状态中的一个：

| 值   | 状态               | 描述                                                |
| ---- | ------------------ | --------------------------------------------------- |
| `0`  | `UNSENT`           | 代理被创建，但尚未调用 open() 方法。                |
| `1`  | `OPENED`           | `open()` 方法已经被调用。                           |
| `2`  | `HEADERS_RECEIVED` | `send()` 方法已经被调用，并且头部和状态已经可获得。 |
| `3`  | `LOADING`          | 下载中； `responseText` 属性已经包含部分数据。      |
| `4`  | `DONE`             | 下载操作已完成。                                    |

**OPENED**：open() 方法已经被触发。在这个状态中，可以通过 [setRequestHeader()](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/setRequestHeader) 方法来设置请求的头部， 可以调用 [send()](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/send) 方法来发起请求。 @@@

### XHR.status 只读

只读属性 `XMLHttpRequest.status` 返回了 `XMLHttpRequest` 响应中的数字状态码。`status` 的值是一个 `无符号短整型`。在请求完成前，`status` 的值为 `0`。值得注意的是，如果 XMLHttpRequest 出错，浏览器返回的 status 也为 0。

如：200

### XHR.statusText 只读

只读属性 `XMLHttpRequest.statusText` 返回了 `XMLHttpRequest` 请求中由服务器返回的一个 [`DOMString`](https://developer.mozilla.org/en-US/docs/Web/API/DOMString) 类型的文本信息，这则信息中也包含了响应的数字状态码。不同于使用一个数字来指示的状态码 `XMLHTTPRequest.status`，这个属性包含了返回状态对应的文本信息，例如 "OK" 或是 "Not Found"。如果请求的状态 `readyState` 的值为 "UNSENT" 或者 "OPENED"，则这个属性的值将会是一个空字符串。

### 判断响应是否正常

在接收到响应后，第一步是检查 **status** **属性**，以确定响应已经成功返回。一般来说，可以将 **HTTP 状态代码**为 200 作为成功的标志。此外，状态代码为 304 表示请求的资源并没有被修改，可以直接使用浏览器中缓存的版本；当然，也意味着响应是有效的。

此时，responseText 属性的内容已经就绪，而且在内容类型正确的情况下，responseXML 也应该能够访问了。

为确保接收到适当的响应，应该像下面这样检查上述这两种状态代码：

```js
if(request.readyState == 4){
  // 再判断响应是否可用：XMLHttpRequest对象的status属性值为200
  if(request.status == 200 || request.status == 304){
    // 执行响应操作
  }
}
```

### XHR.responseType

**XMLHttpRequest.responseType** 属性是一个枚举类型的属性，返回响应数据的类型。它**允许我们手动的设置返回数据的类型**。如果我们将它设置为一个空字符串，它将使用默认的 "text" 类型。

https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/responseType

| 值              | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| `""`            | 将 `responseType` 设为空字符串与设置为 `"text"` 相同， 是默认类型 （实际上是 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString)）。 |
| `"arraybuffer"` | [`response`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/response) 是一个包含二进制数据的 JavaScript [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 。 |
| `"blob"`        | `response` 是一个包含二进制数据的 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 对象 。 |
| `"document"`    | `response` 是一个 [HTML](https://developer.mozilla.org/en-US/docs/Glossary/HTML) [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 或 [XML](https://developer.mozilla.org/en-US/docs/Glossary/XML) [`XMLDocument`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLDocument) ，这取决于接收到的数据的 MIME 类型。请参阅 [HTML in XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/HTML_in_XMLHttpRequest) 以了解使用 XHR 获取 HTML 内容的更多信息。 |
| `"json"`        | `response` 是一个 JavaScript 对象。这个对象是通过将接收到的数据类型视为 [JSON](https://developer.mozilla.org/en-US/docs/Glossary/JSON) 解析得到的。 |
| `"text"`        | `response` 是包含在 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 对象中的文本。 |

### XHR.response 只读

XMLHttpRequest.response 属性返回响应的正文。返回的类型可以是 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBuffer) 、 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 、 [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 、 JavaScript [`Object`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object) 或 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 。 这取决于 [`responseType`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/responseType) 属性。

> **DOMString** 是一个 UTF-16 字符串。由于 JavaScript 已经使用了这样的字符串，所以 DOMString 直接映射到 一个 [`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/String)。
>
> 将 `null` 传递给接受 DOMString 的方法或参数时通常会把其 stringifies 为“null”。

### XHR.responseText 只读

**XMLHttpRequest.responseText** 属性返回一个 DOMString，它包含对文本的请求的响应，如果请求不成功或尚未发送，则返回 null。`responseText` 属性在请求完成之前将会得到部分属性。 如果 `XMLHttpRequest.responseType` 的值**不是** `text` 或者空字符串，届时访问 `XMLHttpRequest.responseText` 将抛出 `InvalidStateError` 异常。

## XHR 对象的方法

### XHR.open()

**XMLHttpRequest.open()** 方法初始化一个请求。该方法要从 JavaScript 代码使用；从原生代码初始化一个请求，使用 `openRequest()` 替代。

**注意**：为已激活的请求调用此方法（`open()` 或 `openRequest()` 已被调用）相当于调用 `abort()`。

**参数**

- **method**：要使用的 HTTP 方法，比如「GET」、「POST」、「PUT」、「DELETE」、等。对于非 HTTP(S) URL 被忽略。
- **url**：一个 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 表示要向其发送请求的 URL。
- async **可选**：一个可选的布尔参数，默认为 `true`，表示要不要异步执行操作。如果值为 `false`，`send()` 方法直到收到答复前不会返回。如果 `true`，已完成事务的通知可供事件监听器使用。

### XHR.setRequestHeader()

**XMLHttpRequest.setRequestHeader()** 是设置 HTTP 请求头部的方法。此方法必须在 [`open()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/open) 方法和 [`send()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/send) 之间调用。

如果多次对同一个请求头赋值，只会生成一个合并了多个值的请求头。 @@@

**参数**

- header：属性的名称
- value：值

### XHR.send()

`XMLHttpRequest.send()` 方法用于发送 HTTP 请求。

如果是异步请求（默认为异步请求），则此方法会在请求发送后立即返回；如果是同步请求，则此方法直到响应到达后才会返回。

XMLHttpRequest.send() 方法接受一个可选的参数，其作为请求主体：

- 如果请求方法是 GET 或者 HEAD，则应将请求主体设置为 null。
- 如果是 POST 方法，传入以 & 分隔，= 连接的键值对字符串

```js
xhr.send("foo=bar&lorem=ipsum"); 
```

如果没有使用 setRequestHeader（）方法设置 [`Accept`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept) 头部信息，则会发送带有 * / * 的 [`Accept`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept) 头部。

### XHR.abort()

如果该请求**已被发出**，**XMLHttpRequest.abort()** 方法将终止该请求。当一个请求被终止，它的 readyState 属性将被置为 0（ `UNSENT` )。

### XHR.getResponseHeader()

```js
var client = new XMLHttpRequest();
client.open("GET", "unicorns-are-teh-awesome.txt", true);
client.send();
client.onreadystatechange = function() {
  if(this.readyState == this.HEADERS_RECEIVED) {
    console.log(client.getResponseHeader("Content-Type"));
  }
}
```

## 具体步骤

### 第一步：创建 XMLHttpRequest 对象

兼容低版本的浏览器

```js
var xhr = null
if(window.XMLHttpRequest){
	xhr = new XMLHttpRequest()
}else{
	//低版本浏览器，比如IE6
	xhr = new ActiveXObject(“Microsoft.XMLHTTP”)
}
```

### 第二步：open()，准备发送网络请求

```js
xhr.open(“get”,”./serveer/checkName.php? name=”+name,true)
```

**参数**

- method：GET or POST

  > 如果是 get 请求，需要在 url 的后面**拼上参数**。
  >
  > 如果是 post 请求，在调用 send() 时传入，参数会被附加在请求体中

- 第二个参数，是 Http 请求的 url 地址
- 第三个参数代表这个 Http 请求是同步的还是异步的，false 代表同步，true 代表异步。

  ```js
  xhr.open(“post”,”./server/checkName.php”,true)
  ```

- post 请求，参数在请求体当中，之前都是通过表单提交的，表单提交的请求是个黑箱，最终后台服务器可以正常获得表单中的数据，但是如果通过 ajax 请求，则需要在第三步的时候指定参数

### 第三步：send()，开始发送 Ajax 请求

```js
xhr.send(null)
```

使用 get 请求，传递 nul

下面是使用 post 请求的例子

```js
var params = “name=” + name //用&连接多个参数
//仍是使用key=value的形式传递参数
xhr.setRequestHeader(“Content-type”, “application/x-www-form-urlencoded”)
//模仿表单提交数据的时候的请求头，仅仅在post请求的时候需要
xhr.send(params)
//send方法会把param加入到http的请求体中，后台可以正常获取
```

### 第四步，在回调函数中获取返回值

```js
xhr.onreadystatechange = function(){
  if(xhr.readyState === 4){
    //xhr的状态4，正常
    if(xhr.status===200){
      //服务器的状态400，正常
      //xhr.responseXML
      var result = xhr.responseText
      console.log(result)
    }	
  }
}
```

如果返回的数据是 JSON 字符串，使用 JSON.parse 转换为对象，再读取需要的值

根据接口文档编写 ajax 请求

![1553412107359](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412107359.png)

- 实际使用中，通常会对 ajax 进行封装

### 同步 XHR

```js
xhr.open(“get”,”./serveer/checkName.php? name=”+name,false)
```

改变为同步请求，这样做的话会有两个问题

- 界面会卡顿，取决于网络速度
- xhr.onreadystatechange 的回调不会被执行，同步的请求在数据返回（js 代码执行 xhr.send() 的时候 readyState 就已经改变成 4 了），之后 readyState 的值没有再变化过，所以触发 onreadystatechange 回调函数

解决办法：将回调去掉即可

```js
var xhr = null
if(window.XMLHttpRequest){
  xhr = new XMLHttpRequest()
}else{
  //低版本浏览器，比如IE6
  xhr = new ActiveXObject(“Microsoft.XMLHTTP”)
}

xhr.open(“get”,”./serveer/checkName.php? name=”+name,false)

xhr.send(null)

if(xhr.readyState === 4){
  //xhr的状态4，正常
  if(xhr.status===200){
    //服务器的状态400，正常
    //xhr.responseXML
    var result = xhr.responseText
    console.log(result)
  }	
}

```

### 数据格式

![1553412270368](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412270368.png)

元数据：描述数据的数据

![1553412291982](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412291982.png)

优点：体积小、传输快、易解析

## 封装 Ajax

### 封装

哪些是变化的: 请求方式 url 地址 请求参数 返回的数据类型

哪些是不变的: 创建 XMLHttpRequest 对象，准备发送，执行发送，响应回调

如何将结果通知调用者

- 传入一个回调函数（参数是请求结果），而不是在封装函数内部 return 结果
- 回调函数的话，调用者可以直接操作结果
- 如何调用更方便

### 初步封装

```js
function myAjax(type,url,params,dataType,callback){
  var xhr = null;
  // 兼容低版本IE
  if(window.XMLHttpRequest){
    xhr = new XMLHttpRequest()
  } else {
    xhr = new ActiveXObject("Microsoft.XMLHTTP")
  }

  // 默认了传入的是拼串后的params，所以只需要加在url后边即可
  if(type==="get") url = "?" + params

  xhr.open(type,url,true)

  if(type==="get"){
    xhr.send(null)
  }else if(type==="post"){
    xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded")
    xhr.send(params)
  }
  xhr.onreadystatechange = function(){
    if(xhr.readyState===4){
      if(xhr.status===200){
        var result = null;
        if(dataType==="json"){
          result = xhr.responseText
          result = JSON.parse(result)
        } else if(dataType==="xml"){
          result = xhr.responseXML
        }else{
          result = xhr.responseText
        }
        //把result作为参数传递给回调函数
        callback(result)
      }
    }
  }
}

```

### 使用

```js
const params = "name="+name
myAjax("get","/checkName.php",params,"text",function(result){
  if(result="ok"){
    username_result.innerText = "用户名可以使用"
 }else {
    username_result.innerText = "用户名已经被注册"
 }
})
```

## 优化

### 分析

设置响应数据的类型，全都是小写

```js
dataType = dataType.toLowerCase()
xhr.responseType = dataType
```

转换 method 为大写/小写，便于判断

```js
method = method.toUpperCase()
```

让用户传入对象，把查询字符串的拼装放在内部

- 只有传入了非对象才进行拼串
- params 的拼串操作无论是 get 还是 post 都是需要的
- 切割拼合完成后 params 最后的&符号

```js
let query = ''
if(Object.keys(params).length !==0){
  for(const key in params){
    query += `${key}=${params[key]}&`
  }
  query = query.slice(0,query.length-1)
}
```

只有传入了且是非空才执行拼串

```js
if(type==="GET"){
  //只有传入了且是非空才执行拼串
  if(query!=="") url = "?" + params
}
```

如果忘了传入回调，一个 null not a function 会报错，封装的时候通常要考虑的

```js
if(callback){
  //如果传入了callback才执行调用
  callback(result)
}
```

由调用者来决定是同步请求还是异步请求

```js
if(aysnc){
   xhr.onreadystatechange = function(){
     if(xhr.readyState===4){
       if(xhr.status===200){
       }
     }
   }
}else{
   if(xhr.readyState===4){
     if(xhr.status===200){
     }
   }
}
```

通过 switch 处理不同类型的响应数据

```js
let result = xhr.responseText
// 根据dataType即不同的文件类型，对返回的内容做处理 
switch(this.dataType.toUpperCase()){
  case 'TEXT':
  case 'HTML':
    break;
  case 'JSON':
    result = JSON.parse(result)
    break;
  case 'XML':
    result = xhr.responseXML
}   
```

参数，严格的数量顺序要求，通过解构赋值可以进一步优化

参数默认值

es5 实现：

```js
function myAjax2(obj){
  var defaults = {
    type:"GET",
    url:"#",
    dataType:"json",
    data:{}
  }
 //使obj中存在的属性，覆盖到dafaults中的属性
 //1.如果属性只存在obj中，会给defaults增加属性
 //2.如果属性obj和defaults中都存在，会将defaults中的默认值覆盖
 //3.如果属性只存在defaults中，那么将会保持默认值，因为obj无法遍历到相同的属性名，
 for(var key in obj){
   defaults[key]=obj[key]
 }
}
```

### 实现

```js
function myAjax2(obj){
  var defaults = {
    type:"get",
    url:"#",
    dataType:"json",
    params:{},
    async:true,
    success:function(result){console.log(result)}
  }

  for(var key in obj){
    defaults[key]=obj[key]
  }

  var xhr = null;
  if(window.XMLHttpRequest){
    xhr = new XMLHttpRequest()
  } else {
    xhr = new ActiveXObject("Microsoft.XMLHTTP")
  }
	const dataType = defaults.dataType.toLowerCase()
  xhr.responseType = dataType
  let query = ''
  if(Object.keys(defaults.params).length !==0){
    for(const key in defaults.params){
      query += `${key}=${defaults.params[key]}&`
    }
    query = query.slice(0,query.length-1)
  }

  if(type==="get"){
    if(query!==""){
      url = "?" + query
    }
  }

  xhr.open(defaults.type,defaults.url,defaults.async)

  if(defaults.type==="get"){
    xhr.send(null)
  }else if(defaults.type==="post"){
    xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded")
    xhr.send(query)
  }

  if(defaults.aysnc){//异步请求
    xhr.onreadystatechange = function(){
      if(xhr.readyState===4){
        if(xhr.status===200){
          var result = null;
          if(defaults.dataType==="json"){
            result = xhr.responseText
            result = JSON.parse(result)
          } else if(defaults.dataType==="xml"){
            result = xhr.responseXML
          }else{
            result = xhr.responseText
          }
          defaults.success(result)
        }
      }
    }
  }else{//同步请求
    if(xhr.readyState===4){
      if(xhr.status===200){
        var result = null;
        if(dataType==="json"){
          result = xhr.responseText
          result = JSON.parse(result)
        } else if(dataType==="xml"){
          result = xhr.responseXML
        }else{
          result = xhr.responseText
        }
        defaults.success(result)
      }
    }
  }
}

```

使用

![1553412925148](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412925148.png)

## jQuery 中使用 Ajax

jQuery 中对 ajax 的封装与上文 ajax2 中的操作基本一致。

jQuery 对于 ajax 的封装，提供了多个方法可供使用，这些方法都是在一个方法的基础上进行修改的，就是 $.ajax()

- $.ajax()
- $.get()
- $.post()

![1553412965966](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412965966.png)

更详细的参数配置，可以查看 jQuery 参考文档

![1553412985214](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412985214.png)

![1553412993011](/img/user/programming/basic/cs-basic/network-protocol/fetch/1553412993011.png)

## 使用 Promise 封装 Ajax

**参考**：

https://juejin.im/post/5b699295e51d45199358f7b5

https://juejin.im/post/5ca1f7f3e51d454937026839

### 初步封装

原生的 XHR，必须传入回调函数，在回调函数中操作 result 。但是有了 promise ，我们可以直接在 then 函数里获得 result

```js
export default function ajaxPro(
 url = null,
 params = {},
 method = 'GET',
 dataType = 'text',
 async = true // 不考虑同步请求
){
  return new Promise((resolve,reject) => {
    let xhr =null

    if(window.XMLHttpRequest){
      xhr = new XMLHttpRequest()
    }else {
      xhr = new ActiveXObject('Microsoft.XMLHTTP')
    }

    dataType = dataType.toLowerCase()
    xhr.responseType = dataType

    let query = ''
    for(const key in params){
      query += `${key}=${params[key]}&`
    }
    if(query) query = query.slice(0,query.length-1)
    if(method === 'GET') {
      if(query) url += `?${query}`
    }

    xhr.open(method,url,async)

    method = method.toUpperCase()
    if(method === 'GET'){
      xhr.send(null)
    }else if(method === 'POST'){
      xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded")
      xhr.send(params)
    }

    xhr.onreadystatechange = function(){
      if(!/^[23]\d{2}$/.test(xhr.status)) return
      if(xhr.readyState===4){
        let result = xhr.responseText
        // result = JSON.parse(result) 通过switch对不同的类型做不同的处理
        // 最主要的区别
        resolve(result)
      }
    }
    xhr.onerror = function(err){
      reject(err)
    }
  })
}
```

使用

```js
ajaxPro('/api/test').then((result) => {
  setResult(result)
},(err) => {
  console.log(err)
})
```

## Axios

### 使用 Axios

```js
/*
ajax请求函数模块
返回值: promise对象(异步返回的数据是: response.data)
 */
import axios from 'axios'
export default function ajax (url, data={}, type='GET') {

  return new Promise(function (resolve, reject) {
    let promise
    if (type === 'GET') {
      // 准备url query参数数据
      let query = '' 
      //数据拼接字符串
      Object.keys(data).forEach(key => {
        query += key + '=' + data[key] + '&'
      })
      if (dataStr !== '') {
        dataStr = dataStr.substring(0, dataStr.lastIndexOf('&'))
        url = url + '?' + dataStr
      }
      // 发送get请求
      promise = axios.get(url)
    } else {
      // 发送post请求
      promise = axios.post(url, data)
    }
    promise.then(function (response) {
      // 成功了调用resolve()
      // axios会对返回值进行包装，data是返回的JSON
      resolve(response.data)
    }).catch(function (error) {
      //失败了调用reject()
      reject(error)
    })
  })
}
```

使用

```js
import ajax from './ajax'

// 登陆接口
export const reqLogin = ({username, password}) => ajax('/login',{username, password}, 'POST')
// 获取用户列表
export const reqUserList = (type) => ajax('/userlist', {type})
```

# Fetch

了解 Fetch API <https://aotu.io/notes/2017/04/10/fetch-API/index.html>

## 概述

在 Ajax 中涉及到的 JavaScript 方面的技术，即 XMLHttpRequest（以下简称 XHR）。很长一段时间我们都是通过 XHR 来与服务器建立异步通信。然而在使用的过程中，我们发现 XHR 是基于事件的异步模型，在设计上将输入、输出和事件监听混杂在一个对象里，且必须通过实例化方式来发请求。配置和调用方式混乱，不符合关注分点离原则。

Fetch API 是近年来被提及将要取代 `XHR` 的技术新标准，是一个 HTML5 的 API。

Fetch 并不是 `XHR` 的升级版本，而是从一个全新的角度来思考的一种设计。Fetch 是基于 Promise 语法结构，而且它的设计足够低阶，这表示它可以在实际需求中进行更多的弹性设计。对于 **XHR** 所提供的能力来说，Fetch 已经足够取代 `XHR` ，并且提供了更多拓展的可能性。

Fetch API 规范明确了用户代理获取资源的语义。原生支持 `Promise`[1](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)，调用方便，符合语义化。可配合使用 ES2016 中的 `async`/ `await` 语法，更加优雅。

### Get 示例

```js
// 获取 some.json 资源
fetch('some.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(result) {
    console.log('data', data);
  })
  .catch(function(error) {
    console.log('Fetch Error: ', error);
  });

// 采用ES2016的 async/await 语法
  const weekly = (async () => {
    try {
      const response = await fetch('http://127.0.0.1:8000/weekly');
      const result = await response.json();
      console.log('data', result);
      return result
    } catch (error) {
      console.log('Request Error: ', error)
    }
  })()
```

### Post 示例

当我们使用 Fetch 发起 Post 请求时，需要手动设置 method 参数和 body 参数，如下：

```js
fetch(url, {
    method: 'post',
    headers: {
      "Content-type": "application/x-www-form-urlencoded; charset=UTF-8"
    },
    body: 'foo=bar&lorem=ipsum'
  })
  .then(function(response) {
     return response.json();
   })
  .then(function (result) {
    console.log('Request succeeded with JSON response', result);
  })
  .catch(function (error) {
    console.log('Request failed', error);
  });
```

## 具体用法

### Fetch 方法

fetch 方法有两种调用方式。

```js
Promise fetch(String url, [, Object options])
Promise fetch(Request req, [, Object options])
```

第一个参数是一个 `url`，第二个参数是配置信息，可选

第一个参数是一个 `Request` 对象，第二个参数是配置信息，可选

> 也可以理解为只有一种参数，因为 request 对象也可以接受 [`USVString`](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString)，传入的 String，最终还是被转换为 Request 对象

**返回值**

+ 一个 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/API/Promise)，resolve 时回传 [`Response`](https://developer.mozilla.org/zh-CN/docs/Web/API/Response) 对象。

**异常**

+ `AbortError`The request was aborted (using [`AbortController.abort()`](https://developer.mozilla.org/zh-CN/docs/Web/API/AbortController/abort)).
+ `TypeError`Since [Firefox 43](https://developer.mozilla.org/en-US/docs/Mozilla/Firefox/Releases/43), `fetch()` will throw a `TypeError` if the URL has credentials, such as `http://user:password@example.com`.

### 配置信息

可选配置信息是一个 Object 对象，可以包含以下字段：

+ method: 请求的方法，例如：`GET`, `POST`。
+ headers: 请求头部信息，可以是一个简单的对象，也可以是 Headers 类实例化的一个对象。
+ body: 需要发送的信息内容，可以是 `Blob`, `BufferSource`, `FormData`, `URLSearchParams` 或者 `USVString`。注意，`GET`, `HEAD` 方法不能包含 body。
+ mode: 请求模式，分别有

	- cors: 允许跨域，要求响应中 `Acess-Control-Allow-Origin` 这样的头部表示允许跨域。
	- no-cors: 只允许使用 `HEAD`, `GET`, `POST` 方法
	- same-origin: 只允许同源请求，否则直接报错，**默认值**
	- navigate: 支持页面导航。

> 模式允许来自 CDN 的脚本、其他域的图片和其他一些跨域资源，但是首先有个前提条件，就是请求的 method 只能是 "HEAD","GET" 或者 "POST"。此外，任何 ServiceWorkers 拦截了这些请求，它不能随意添加或者改写任何 headers，除了 [这些](https://fetch.spec.whatwg.org/#simple-header)。第三，JavaScript 不能访问 Response 中的任何属性，这保证了 ServiceWorkers 不会导致任何跨域下的安全问题而隐私信息泄漏。
>
> 不能是其他域的 text
>
> json：Cross-Origin Read Blocking (CORB) blocked cross-origin response http://localhost:8000/fetch with MIME type text/plain. See https://www.chromestatus.com/feature/5629709824032768 for more details.

+ credentials: 表示是否发送 cookie，有三个选项

	- omit: 不发送 `cookie`。
	- same-origin: 仅在同源时发送 `cookie`，**默认值**
	- include: 发送 `cookie`。

+ cache: 表示处理缓存的策略。
+ redirect: 表示发生重定向时，有三个选项

	- follow: 跟随，**默认值**
	- error: 发生错误。
	- manual: 需要用户手动跟随。

+ integrity: 包含一个用于验证资资源完整性的字符串。

## Headers 类

`Headers` 可用来表示 HTTP 的头部信息，使用 `Headers` 的接口，你可以通过 Headers() 构造函数来创建一个你自己的 `headers` 对象。

```js
var headers = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});
headers.append("X-Custom-Header", "AnotherValue");
headers.has("Content-Type") // true
headers.getAll("X-Custom-Header"); // ["ProcessThisImmediately", "AnotherValue"]
```

`Headers` 提供 `set`，`append`，`get`，`getAll` , `has`, `delete`, `forEach` 等这些实例方法，可供开发者更加灵活地配置请求中的 headers。

因为安全性原因，一些 headers 仅受用户代理控制。包括 [forbidden header names](https://developer.mozilla.org/en-US/docs/Glossary/Forbidden_header_name) 和 [forbidden response header names](https://developer.mozilla.org/en-US/docs/Glossary/Forbidden_response_header_name)

### set()

**set()** 方法在可以在已经声明中的 `headers` 对象修改已有的一组键值对或者创建一个新的键值对。

```js
myHeaders.set(name, value);
```

**示例**

+ 创建一个新的 `Headers` 对象:

  ```js
  var myHeaders = new Headers(); // Currently empty
  ```

+ 如果这个键值对不存在，那么**set()**方法首先创建一个键值对，然后给它赋值。
+ 如果这个键值对存在，那么 `set()` 方法将会覆盖之前的**value**值:

  ```js
  myHeaders.set('Accept-Encoding', 'deflate');
  myHeaders.set('Accept-Encoding', 'gzip');
  myHeaders.get('Accept-Encoding'); // Returns 'gzip'
  ```

### append()

在一个 `Headers` 对象内部，[`Headers`](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers) 接口的**append()**方法可以追加一个新值到已存在的 headers 上，或者新增一个原本不存在的 header。

[`Headers.set`](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers/set) 和 `append()` 两者之间的不同之处在于当指定 header 是已经存在的并且允许接收多个值时，[`Headers.set`](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers/set) 会重写此值为新值，而 `append()` 会追加到值序列的尾部。

```js
myHeaders.append(name,value);
```

**示例**

- 创建一个空的 Headers 对象:

  ```js
  var myHeaders = new Headers(); // Currently empty
  ```

- 可以通过 append() 方法添加 header:

  ```js
  myHeaders.append('Content-Type', 'image/jpeg');
  myHeaders.get('Content-Type'); // Returns 'image/jpeg'
  ```

- 如果指定 header 不存在, `append()` 将会添加这个 header 并赋值 . 如果指定 header 已存在并允许有多个值, `append()` 将会把指定值添加到值队列的末尾。

  ```js
  myHeaders.append('Accept-Encoding', 'deflate');
  myHeaders.append('Accept-Encoding', 'gzip');
  myHeaders.getAll('Accept-Encoding'); // Returns [ "deflate", "gzip" ]
  ```

- 要使用新值覆盖旧值，请使用 [`Headers.set`](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers/set)。

### get()

**get()** 方法以 [`ByteString`](https://developer.mozilla.org/zh-CN/docs/Web/API/ByteString) 形式从 Headers 对象中返回指定 header 的全部值. 如果 Header 对象中不存在请求的 header,则返回 `null`.

```js
myHeaders.get(name);
```

**示例**

+ 可以通过 get() 方法来获取 header 中的值:

  ```js
  myHeaders.append('Content-Type', 'image/jpeg');
  myHeaders.get('Content-Type'); // Returns 'image/jpeg'
  ```

+ 如果存在多个 header 值,那么**只有第一个值会被返回**:

  ```js
  myHeaders.append('Accept-Encoding', 'deflate');
  myHeaders.append('Accept-Encoding', 'gzip');
  myHeaders.get('Accept-Encoding'); // Returns 'deflate,gzip'
  ```

### has()

has() 方法返回一个布尔值来表示一个 `Headers` 对象 是否包含特定的头信息

### delete()

**delete()** 方法可以从 Headers 对象中删除指定 header.

下列原因将会导致该方法抛出一个 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError):

- header 名在 HTTP header 中是不存在的.
- header 被锁定了

### 遍历

getAll()，forEach() 均已废弃

建议使用 keys(),values(),enties()，类 set，map

```
headers.entries()
```

## Request 类

`Request` 类用于描述**请求内容**。构造函数接受的参数与 fetch 方法一致，这里就不展开介绍了。我们可以这么理解，事实上 fetch 方法在调用时，会将传入的参数构造出一个 Request 对象并执行。

```js
var URL = '//api.some.com';
var getReq = new Request(URL, {method: 'GET', cache: 'reload'});
fetch(getReq)
	.then(function(response) {
	  return response.json();
	})
	.catch(function(error) {
	  console.log('Fetch Error: ', error);
	});
```

Request 接口中的配置项 headers 可以是实例化的 Headers

```js
var URL = '//api.some.com';
// 实例化 Headers
var headers = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});

var getReq = new Request(URL, {method: 'GET', headers: headers });
fetch(getReq)
	.then(function(response) {
	  return response.json();
	})
	.catch(function(error) {
	  console.log('Fetch Error: ', error);
	});
```

更便捷的是，Request 对象可以从已有的 Request 对象中继承，并拓展新的配置。

```js
var URL = '//api.some.com';
var getReq = new Request(URL, {method: 'GET', headers: headers });
// 基于已存在的 Request 实例，拓展创建新的 Request 实例
var postReq = new Request(getReq, {method: 'POST'});
```

### Request() 构造函数

`Request()` 构造器创建一个新的 [`Request`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request) 对象。

```js
var myRequest = new Request(input, init);
```

**参数**

+ input：定义你想要 fetch 的资源。可以是下面两者之一:

	- 一个直接包含你希望 fetch 的资源的 URL 的 [`USVString`](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString)
	- 一个 [`Request`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request) 对象

+ init ：一个可选对象，包含希望被包括到请求中的各种自定义选项。可用的选项如下：

	- `method`: 请求的方法，例如：`GET`, `POST。`
	- `headers`: 任何你想加到请求中的头，其被放在 [`Headers`](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers) 对象或内部值为 [`ByteString`](https://developer.mozilla.org/zh-CN/docs/Web/API/ByteString) 的对象字面量中。
	- `body`: 任何你想加到请求中的 body，可以是 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob), [`BufferSource`](https://developer.mozilla.org/zh-CN/docs/Web/API/BufferSource), [`FormData`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData), [`URLSearchParams`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams), 或 [`USVString`](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString) 对象。注意 `GET` 和 `HEAD请求没有body。`
	- `mode`: 请求的模式, 比如 `cors`, `no-cors`, `same-origin`, 或 `navigate`。默认值应该为 `cors。` 但在 Chrome 中，Chrome 47 之前的版本默认值为 `no-cors` ，自 Chrome 47 起，默认值为 `same-origin。`
	- `credentials`: 想要在请求中使用的 credentials：: `omit`, `same-origin`, 或 `include`。默认值应该为 `omit`。但在 Chrome 中，Chrome 47 之前的版本默认值为 `same-origin` ，自 Chrome 47 起，默认值为 `include。`
	- `cache`: 请求中想要使用的 [cache mode](https://developer.mozilla.org/en-US/docs/Web/API/Request/cache)
	- `redirect`: 对重定向处理的模式： `follow`, `error`, or `manual`。在 Chrome 中，Chrome 47 之前的版本默认值为 `manual` ，自 Chrome 47 起，默认值为 `follow。`
	- `referrer`: 一个指定了 `no-referrer`, `client`, 或一个 URL 的 [`USVString`](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString) 。默认值是 `client`.
	- `integrity`: 包括请求的 [subresource integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) 值 (e.g., `sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=`)

+ 与 fetch options object 是完全一致的，**所以称之为 init** ， 是设置初始值的地方，之后可以根据需求，在不同的 fetch 分别设置不同的参数

**描述**

```js
  var myImage = document.querySelector('img');
  
  var myHeaders = new Headers();
  myHeaders.append('Content-Type', 'image/jpeg');
  
  var myInit = { method: 'GET',
                 headers: myHeaders,
                 mode: 'cors',
                 cache: 'default' };
  
  var myRequest = new Request('flowers.jpg',myInit);
  
  fetch(myRequest,specificOptions).then(function(response) {
    ... 
  });
```

+ 注意你也可以把 init 对象作为参数传递到 fetch 调用中来达到同样的效果。如下：

  ```js
  fetch(myRequest,myInit).then(function(response) {
    ...
  });
  ```

**描述**

+ 可以把 [`Request`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request) 对象再作参数传递进 `Request()` 构造器来创建一个请求的副本（就像调用 [`clone()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/clone) 一样）。

  ```js
  var copy = new Request(myRequest);
  ```

### Request 实例属性

全都是只读属性，包括：

+ [`Request.method`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/method) 包含请求的方法 (`GET`, `POST`, 等.)
+ [`Request.url`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/url) 包含这个请求的 URL。
+ [`Request.headers`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/headers) 包含请求相关的 [`Headers`](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers) 对象。
+ [`Request.context`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/context) 包含请求的上下文 (例如：`audio`, `image`, `iframe`, 等)
+ [`Request.referrer`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/referrer) 包含请求的来源 (例如：`client`)。
+ [`Request.referrerPolicy`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/referrerPolicy) 包含请求来源的策略 (例如：`no-referrer`)。
+ [`Request.mode`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/mode) 包含请求的模式 (例如： `cors`, `no-cors`, `same-origin`,` navigate`).
+ [`Request.credentials`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/credentials) 包含请求的证书 (例如： `omit`, `same-origin`).
+ [`Request.redirect`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/redirect) 包含如何处理重定向模式，它可能是一个 ` follow `，`error` 或者 `manual`。
+ [`Request.integrity`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/integrity) 包含请求的 [子资源的完整性](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) 值 (例如： `sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=`).
+ [`Request.cache`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/cache) 包含请求的缓存模式 (例如： `default`, `reload`, `no-cache`).

### Request 实例方法

[`Request.clone()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/clone) 创建当前 request 的副本。

### Body

`Request` 实现了 [`Body`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body), 所以它还具有以下属性可用：

- [`Body.body`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/body) 只读

  一个简单 getter 用于曝光一个 [`ReadableStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/ReadableStream) 的主体内容.

- [`Body.bodyUsed`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/bodyUsed) 只读

  存储一个 [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/API/Boolean) 判断主体是否已经被用于一个响应中.

`Request` 实现 [`Body`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body), 因此它也有以下方法可用：

- [`Body.arrayBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/arrayBuffer)

  返回解决一个 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBuffer) 表示的请求主体的 promise.

- [`Body.blob()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/blob)

  返回解决一个 [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob) 表示的请求主体的 promise.

- [`Body.formData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/formData)

  返回解决一个 [`FormData`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData) 表示的请求主体的 promise.

- [`Body.json()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/json)

  返回解决一个 [`JSON`](https://developer.mozilla.org/zh-CN/docs/Web/API/JSON) 表示的请求主体的 promise.

- [`Body.text()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/text)

  返回解决一个 [`USVString`](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString)(文本) 表示的请求主体的 promise.

## Response 类

Response 实例是在 fentch() 处理完 promises 之后返回的。它的实例也可用通过 JavaScript 来创建，但只有在 ServiceWorkers 中才真正有用。

```js
var res = new Response(body, init);
```

其中 `body` 可以是 `Bolb`, `BufferSource`, `FormData`, `URLSearchParams`, `USVString` 这些类型的值。

init 是一个对象，可以包括以下这些字段：

+ status: 响应状态码
+ statusText: 状态信息
+ headers: 头部信息，可以是对象或者 `Headers` 实例

### Response 实例属性

均是只读属性，包括：

+ bodyUsed: 用于表示响应内容是否被使用过
+ headers: 头部信息
+ ok: 表明请求是否成功，响应状态为 200 ~ 299 时，值为 true
+ status: 状态码
+ statusText: 状态信息
+ type: 响应类型

	- basic: 同源
	- cors: 跨域
	- error: 出错
	- opaque: Request mode 设置为 “no-cors”的响应

+ url: 响应地址

### Response 实例方法

clone: 复制一个响应对象。

### Body

`Response` 实现了 [`Body`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body) 接口, 所以以下方法同样可用:

+ arrayBuffer: 将响应数据转换为 arrayBuffer 后 reslove 。
+ bolb: 把响应数据转换为 Bolb 后 reslove 。
+ formData: 把响应数据转换为 formData 后 reslove 。
+ json: 把响应内容解析为 json 对象后 reslove 。
+ text: 把响应数据当做字符串后 reslove 。

## Body

不管是请求还是响应都能够包含 body 对象. body 也可以是以下任意类型的实例.

+ [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBuffer)
+ [`ArrayBufferView`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBufferView) (Uint8Array 等)
+ [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)/File
+ string
+ [`URLSearchParams`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)
+ [`FormData`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)

[`Body`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body) 类定义了以下方法 (这些方法都被 [`Request`](https://developer.mozilla.org/zh-CN/docs/Web/API/Request) 和 [`Response`](https://developer.mozilla.org/zh-CN/docs/Web/API/Response) 所实现) 以获取 body 内容. 这些方法都会返回一个被解析后的 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/API/Promise) 对象和数据.

+ [`arrayBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/arrayBuffer)
+ [`blob()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/blob)
+ [`json()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/json)
+ [`text()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/text)
+ [`formData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/formData)
+ 比起 XHR 来，这些方法让非文本化的数据使用起来更加简单。
+ 请求体可以由传入 body 参数来进行设置:

  ```js
  var form = new FormData(document.getElementById('login-form'));
  fetch("/login", {
    method: "POST",
    body: form
  })
  ```

+ request 和 response（包括 `fetch()` 方法）都会试着自动设置 `Content-Type`。如果没有设置 `Content-Type` 值，发送的请求也会自动设值。

## 缺点

我们可以通过对 `window.fetch` 的能力检测，判断出浏览器是否支持 Fetch API。github 官方推出了一个 Fetch API 的 polyfill 库，可以让更多浏览器提前感受到 Fetch API 的便捷的开发体验。

虽然 Fecth API 使用方便符合语义化，但是现阶段它也有所限制。Fetch API 是基于 Promise，由于 Promise 没有处理 timeout 的机制，所以无法通过原生方式处理请求超时后的中断，和读取进度的能力。但是相信未来为了支持流，Fetch API 最终将会提供可以中断执行读取资源的能力，并且提供可以读取进度的 API。

## 注意

### 检测请求是否成功

当接收到一个代表错误的 HTTP 状态码时，从 `fetch()` 返回的 Promise **不会被标记为 reject，** 即使该 HTTP 响应的状态码是 404 或 500。相反，它会将 Promise 状态标记为 resolve （但是会将 resolve 的返回值的 `ok` 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。

如果遇到网络故障，[`fetch()`](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch) promise 将会 reject，带上一个 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) 对象。

想要精确的判断 `fetch()` 是否成功，需要包含 promise resolved 的情况，此时再判断 [`Response.ok`](https://developer.mozilla.org/zh-CN/docs/Web/API/Response/ok) 是不是为 true。类似以下代码：

```js
fetch('flowers.jpg').then(function(response) {
  if(response.ok) {
    return response.blob()
  }
  throw new Error('Network response was not ok.')
}).then(function(myBlob) { 
  var objectURL = URL.createObjectURL(myBlob); 
  myImage.src = objectURL
}).catch(function(error) {
  console.log('There has been a problem with your fetch operation: ', error.message)
});
```

### Post 请求

当我们使用 Fetch 发起 Post 请求时，需要手动设置 method 参数和 body 参数，如下：

```js
fetch(url, {
    method: 'post',
    headers: {
      "Content-type": "application/x-www-form-urlencoded; charset=UTF-8"
    },
    body: 'foo=bar&lorem=ipsum'
  })
  .then(function(response) {
     return response.json();
   })
  .then(function (result) {
    console.log('Request succeeded with JSON response', result);
  })
  .catch(function (error) {
    console.log('Request failed', error);
  });
```

### 发送带凭据的请求

默认情况下，`fetch` **不会从服务端发送或接收任何 cookies**, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 [credentials](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch#参数) 选项）。自从 2017 年 8 月 25 日后，默认的 credentials 政策变更为 `same-origin`Firefox 也在 61.0b13 中改变默认值

```js
fetch('https://example.com', {
  credentials: 'include'  
})
```

如果你只想在请求 URL 与调用脚本位于同一起源处时发送凭据，请添加 `credentials: 'same-origin'`。

```js
fetch('https://example.com', {
  credentials: 'same-origin'  
})
```

要改为确保浏览器不在请求中包含凭据，请使用 `credentials: 'omit'`

```js
fetch('https://example.com', {
  credentials: 'omit'  
})
```

### 流和克隆

非常重要的一点要说明，那就是 Request 和 Response 的 body 只能被读取一次！它们有一个属性叫 bodyUsed，读取一次之后设置为 true，就不能再读取了。

```js
var res = new Response("one time use");
console.log(res.bodyUsed); // false
res.text().then(function(v) {
  console.log(res.bodyUsed); // true
});
console.log(res.bodyUsed); // true

res.text().catch(function(e) {
  console.log("Tried to read already consumed Response");
});
```

这样设计的目的是为了之后兼容基于流的 API，让应用一次消费 data，这样就允许了 JavaScript 处理大文件例如视频，并且可以支持实时压缩和编辑。

所以，我们该如何让 body 能经得起多次读取呢 ？Request 和 Response API 提供了一个 clone() 方法。调用这个方法可以得到一个克隆对象。不过要记得，clone() 必须要在读取之前调用，也就是先 clone() 再读取。

## 封装 Fetch

```js
export default async function myFetch(
  url = null,
  params = {},
  method = 'GET',
){
  console.log(url,params,method)
  let result = null
  let response = null
  // 返回一个promise，外部可以通过then在获取到数据后再继续操作
  // 使用async就不需要包一层promise了，因为返回的是同步的
  
  // 无论是GET还是POST都需要拼接参数
  let query = ''
  for(const key in params){
    query += `${key}=${params[key]}&`
  }
  if(query) query = query.slice(0,query.length-1)
  if(method === 'GET') {
    if(query) url += `?${query}`
  }

  // 不同的请求不同的fetch
  try {
    switch(method){
      case 'GET':
        console.log('GET',url)
        response = await fetch(url)
      break
      case 'POST':
        response = await fetch(url, {
          method,
          headers: {
            "Content-type": "application/x-www-form-urlencoded; charset=UTF-8"
          },
          body: query,
          mode:'cors'
        })
      break
    }
  }
  catch (error) {
    console.log('Request Error: ', error)
  }
  result = response.json() //返回值是一个promise。可以在外部通过then方法获取
  console.log('Request succeeded with JSON response', result)
return result
}
```

# RESTful API

http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html

https://juejin.im/entry/59e460c951882542f578f2f0

## 方式 1: 路由路径携带参数 (**param**)

配置路由

```js
children: [
  {
    path: 'mdetail/:id', //占位符 param参数形式 相对的query参数 ？号形式
    /*path:’mdetail’*/
    component: MessageDetail
  }
]
```

路由路径

- `/home/message/mdetail/?id=$(m.id)`

  ```vue
  <router-link :to="'/home/message/mdetail/'+m.id">{{m.title}}</router-link>
  ```

- 路由组件中读取请求参数
- $route 代表当前路由

  ```js
  this.$route.params.id
  ```

# 路由路径携带参数 (**query**)

+ ctx.query.typeId
