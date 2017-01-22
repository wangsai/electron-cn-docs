## 本文介绍类: WebRequest(拦截或修改web请求)

> 在其生命周期的不同阶段， `webRequest` API 设置允许拦截并修改请求内容.

进程: [主进程](../glossary.md#main-process)         

 `WebRequest`类的实例是通过使用 `Session`的 `webRequest`属性来访问。
 
每个 API 接收一可选的 `filter` 和 `listener`，当 API 事件发生的时候使用 `listener(details)` 调用 `listener`，`details` 是一个用来描述请求的对象.为 `listener` 使用 `null` 则会退定事件.

`filter` 是一个拥有 `urls` 属性的对象，这是一个 url 模式数组，这用来过滤掉不匹配指定 url 模式的请求.如果忽略 `filter` ，那么所有请求都将可以成功匹配.

所有事件的 `listener` 都有一个回调事件，当 `listener` 完成它的工作的时候，它将使用一个 `response` 对象来调用.

为请求添加`User-Agent`标头的示例：

```javascript
const {session} = require('electron')

// 将所有请求的代理都修改为下列 url.
const filter = {
  urls: ['https://*.github.com/*', '*://electron.github.io']
}

session.defaultSession.webRequest.onBeforeSendHeaders(filter, (details, callback) => {
  details.requestHeaders['User-Agent'] = 'MyAgent'
  callback({cancel: false, requestHeaders: details.requestHeaders})
})
```

###实例方法
以下方法适用于 `WebRequest`实例：

#### `webRequest.onBeforeRequest([filter, ]listener)`

* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `response` Object
      * `cancel` Boolean (可选)
      * `redirectURL` String (可选) -  阻止发送或完成原始请求而不是重定向.

在一个请求被发送到服务器之前，使用 `listener(details)` 来调用 `listener` 
 `uploadData`是一个 `UploadData`对象的数组。
必须使用一个 `response` 对象来调用 `callback` 

#### `webRequest.onBeforeSendHeaders([filter, ]listener)`

* `filter` Object
* `listener` Function
在发送 HTTP 请求的之前，一旦请求报文头可用, `listener(details, callback)` 则调用 `listener`.
可能会在发送任何 http 数据之前向服务器发起一个tcp 连接.
* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `requestHeaders` Object
* `callback` Function
  * `response` Object
    * `cancel` Boolean (可选)
    * `requestHeaders` Object (可选) - 如果提供了,将使用这些 headers 来创建请求.

必须使用一个 `response` 对象来调用 `callback` 

#### `webRequest.onSendHeaders([filter, ]listener)`

* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `requestHeaders` Object
在一个请求被发送到服务器之前，使用 `listener(details)` 来调用 `listener` ，以前的 `onBeforeSendHeaders` 响应的修改在这个监听器触发时是可见的。

#### `webRequest.onHeadersReceived([filter, ]listener)`

* `filter` Object
* `listener` Function
当接收到请求的HTTP响应头时，使用 `listener(details，callback)`来调用 `listener`。
* `details` Object
  * `id` String
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `statusLine` String
  * `statusCode` Integer
  * `responseHeaders` Object
* `callback` Function
  * `response` Object
    * `cancel` Boolean
    * `responseHeaders` Object (可选) - 如果提供, 服务器将假定使用这些头来响应.
    * `statusLine` String (可选) - 应该在覆盖 `responseHeaders`时提供更改标头状态，否则将使用原始响应标头的状态。

必须使用一个 `response` 对象来调用 `callback` :

#### `webRequest.onResponseStarted([filter, ]listener)`

* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `responseHeaders` Object
    * `fromCache` Boolean - Indicates whether the response was fetched from disk
      cache.
    * `statusCode` Integer
    * `statusLine` String

当响应body的首字节到达的时候，使用 `listener(details)` 调用 `listener`.对 http 请求来说，这意味着状态线和响应头可用了.

#### `webRequest.onBeforeRedirect([filter, ]listener)`

* `filter` Object
* `listener` Function
  * `details` Object
    * `id` String
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `redirectURL` String
    * `statusCode` Integer
    * `ip` String (可选) - 请求的真实服务器ip
      actually sent to.
    * `fromCache` Boolean
    * `responseHeaders` Object

当服务器的重定向初始化正要启动时，使用 `listener(details)` 调用 `listener`.

#### `webRequest.onCompleted([filter, ]listener)`

* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `responseHeaders` Object
    * `fromCache` Boolean
    * `statusCode` Integer
    * `statusLine` String

当请求完成的时候，使用 `listener(details)` 调用 `listener`.

#### `webRequest.onErrorOccurred([filter, ]listener)`

* `filter` Object
* `listener` Function
  * `details` Object
    * `id` Integer
    * `url` String
    * `method` String
    * `resourceType` String
    * `timestamp` Double
    * `fromCache` Boolean
    * `error` String - 错误描述.
    
当一个错误发生的时候，使用 `listener(details)` 调用 `listener`.