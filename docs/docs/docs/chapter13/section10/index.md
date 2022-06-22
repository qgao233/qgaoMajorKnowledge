# es6 fetch

https://www.cnblogs.com/libin-1/p/6853677.html
## 概述
1. XMLHttpRequest和axios

* 所有的功能全部集中在同一个对象中，容易书写出混乱不易维护的代码.
* 采用传统的事件驱动模式，无法适配新的Promise API.

2. Fetch API

并非取代AJAX，而是对AJAX传统 API的改进。

精细的功能分割：头部信息、请求信息、响应信息等均分布到不同的对象，更利于处理各种复杂的 AJAX 场景。

使用 Promise API，更利于异步代码的书写。

Fetch API 并非 ES6内容，而是属于 HTML5 新增 Web API。

需要掌握网络通信知识。


## 基本使用
使用fetch函数即可立即向服务器发送网络请求

参数：
* 必填, 字符串, 请求地址
* 选填, 对象, 请求配置

### **请求配置对象：**

* method: 字符串, 请求方法, 默认值是 GET
* headers: 对象, 请求头信息
* body: 请求体的内容, 必须匹配请求头中的 Content-Type
* mode: 字符串, 请求模式
* credentials: 如何携带凭据( cookie )
* cache: 配置缓存模式
* default: 表示 fetch 请求之前将检查下http的缓存
* no-store: 表示 fetch 请求将完全忽略 http 缓存的存在. 这意味着请求之前将不再检查下http 的缓存, 拿到响应后, 它也不会更新 http 缓存.
* no-cache: 如果存在缓存, 那么 fetch 将发送一个条件查询 request 和一个正常的 request, 拿到响应后, 它会更新 http 缓存.
* reload: 表示 fetch 请求之前将忽略 http 缓存的存在, 但是请求拿到响应后, 它将主动更新 http 缓存
* force-cache: 表示 fetch 请求不顾一切的依赖缓存, 即使缓存过期了, 它依然从缓存中读取, 除非没有任何缓存, 那么它将发送一个正常的 request
* only-if-cached: 表示 fetch 请求不顾一切的依赖缓存, 即使缓存过期了, 它依然从缓存中读取. 如果没有缓存, 它将抛出网络错误( 该设置只是 mode为"same-origin"时有效 )

### **fetch 函数返回一个 Promise 对象**

当收到服务器的返回结果后, Promise 进入 resolved 状态, 状态数据为 Response 对象，
当网络发生错误( 或其他导致无法完成交互的错误 ) 时, Promise 进入 rejected 状态, 状态数据为错误信息

**Response 对象**

* ok : boolean, 当响应消息码在 200 ~ 299 之间时为 true, 其他为 false
* status: number, 响应的状态码
* text(): 用于处理文本格式 Ajax 响应. 它从响应中获取文本流, 将其读完, 然后返回一个被解决为 String对象的 Promise
* blob(): 用于处理二进制文件格式 (比如图片或电子表格) 的 Ajax 响应. 它读取文件的原始数据, 一旦读取完整个文件, 就返回一个对解决为 blob 对象的 Promise.
* json(): 用于处理 JSON 格式的 Ajax 的响应. 它将 JSON 数据流转换为一个被解决为 JavaScript 对象的 Promise.
* redirect(): 可以用于重定向到另一个 url. 它会创建一个新的 Promise, 以解决来自重定向的 URL 的响应. 

## 使用样例
```
fetch('some-url')  
  .then(handleResponse)  
  .then(data => console.log(data))  
  . catch (error => console.log(error))  
  
function handleResponse (response) {  
  let contentType = response.headers.get('content-type')  
  if (contentType.includes('application/json')) {  
    return handleJSONResponse(response)  
  } else if (contentType.includes('text/html')) {  
    return handleTextResponse(response)  
  } else {  
    // Other response types as necessary. I haven't found a need for them yet though.  
    throw new Error(`Sorry, content-type ${contentType} not supported`)  
  }  
}  
  
function handleJSONResponse (response) {  
  return response.json()  
    .then(json => {  
      if (response.ok) {  
	return json  
      } else {  
	return Promise.reject(Object.assign({}, json, {  
	  status: response.status,  
	  statusText: response.statusText  
	}))  
      }  
    })  
}  
function handleTextResponse (response) {  
  return response.text()  
    .then(text => {  
      if (response.ok) {  
	return json  
      } else {  
	return Promise.reject({  
	  status: response.status,  
	  statusText: response.statusText,  
	  err: text  
	})  
      }  
    })  
}  
```