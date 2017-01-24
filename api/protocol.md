# 本文介绍:协议的注册和处理

> `protocol` 模块可以注册一个自定义协议，或者使用一个已经存在的协议.

进程: [主进程](../glossary.md#main-process)         

使用一个与 `file://` 功能相似的协议 :
```javascript
const {app, protocol} = require('electron')
const path = require('path')

app.on('ready', () => {
  protocol.registerFileProtocol('atom', (request, callback) => {
    const url = request.url.substr(7)
    callback({path: path.normalize(`${__dirname}/${url}`)})
  }, (error) => {
    if (error) console.error('Failed to register protocol')
  })
})
```
 **注意:** 这个模块应用在 `app` 模块的 `ready` 事件触发之后

## 方法列表
`protocol` 模块有如下方法:

### `protocol.registerStandardSchemes(schemes[, options])`

* `schemes` String[] - 自定义协议注册为标准协议.
* `options` Object (可选)
  * `secure` Boolean (可选) - 是否使用安全协议
    默认为 `false`.

标准协议遵循RFC 3986调用[通用URI语法](https://tools.ietf.org/html/rfc3986#section-3)。
例如`http`和`https`是标准协议，而`file`不是。

注册标准协议后，在服务时可正确解析相对和绝对资源。
否则，该协议将表现得像`file`协议没有能力来解析相对URL。

例如，下文将不会加载图像，，因为非标准协议无法识别相对URL：
```html
<body>
  <img src='test.png'>
</body>
```

注册为标准协议后,允许通过[FileSystem API] [file-system-api]访问文件。否则渲染器会抛出安全错误。

默认情况下，非标准协议禁用Web存储apis(localStorage，sessionStorage，webSQL，indexedDB，cookies)。
所以一般来说，如果你想注册一个自定义协议来替换`http`协议，你必须将它注册成标准协议：
```javascript
const {app, protocol} = require('electron')
protocol.registerStandardSchemes(['atom'])
app.on('ready', () => {
  protocol.registerHttpProtocol('atom', '...')
})
```

**注意:** 以上方法必须在 `app` 模块的 `ready` 事件之前使用

### `protocol.registerServiceWorkerSchemes(schemes)`

* `schemes` String[] -把自定义协议注册成 service workers.
service worker是运行于浏览器后台的一种脚本，它可以无需Web页面或者用户交互就能提供额外的功能.通常用在离线开发上.
举个例子:比如断网了,你仍然可以使用本地缓存数据.

### `protocol.registerFileProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `filePath` String (可选)
* `completion` Function (可选)
  * `error` Error

注册将发送文件作为响应的 `scheme`协议。
当使用 `scheme`创建一个 `request`时， `handler`将被 `handler(request，callback)`调用。
当 `scheme` 被成功注册或者完成(错误)时失败， `completion(error)`会使用 `completion(null)`来调用 `completion`。

要处理 `request`， `callback`应该用文件的路径或一个具有 `path`属性的对象来调用。 `callback(filePath)` 或 `callback({path：filePath})`。

如果 `callback`为空或一个数字或存在`error`属性的对象， `request` 都将失败并显示为你指定的错误号。
有关可以使用的可用错误号，请参阅[net error list] [net-error]。

默认情况下， `scheme`类似于 `http：`，它的解析方式不同于如 `file：`之类的 `通用资源标识符`( `generic URI syntax` )协议.
所以你或许应该调用 `protocol.registerStandardSchemes` 来创建一个标准协议。

### `protocol.registerBufferProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `buffer` (Buffer | [MimeTypedBuffer](structures/mime-typed-buffer.md)) (可选)
* `completion` Function (可选)
  * `error` Error

注册一个 `scheme` 协议，用来发送响应 `Buffer` 。
这个方法的用法类似 `registerFileProtocol`，除非使用一个 `Buffer` 对象，或一个有 `data`,
`mimeType`, 和 `charset` 属性的对象来调用 `callback` .

```javascript
const {protocol} = require('electron')

protocol.registerBufferProtocol('atom', (request, callback) => {
  callback({mimeType: 'text/html', data: new Buffer('<h5>Response</h5>')})
}, (error) => {
  if (error) console.error('Failed to register protocol')
})
```

### `protocol.registerStringProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `data` String (可选)
* `completion` Function (可选)
  * `error` Error

注册一个 `scheme` 协议，用来发送响应 `String` .

这个方法的用法类似 `registerFileProtocol`，除非使用一个 `String` 对象，或一个有 `data`,
`mimeType`, 和 `charset` 属性的对象来调用 `callback` .

### `protocol.registerHttpProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `redirectRequest` Object
      * `url` String
      * `method` String
      * `session` Object (可选)
      * `uploadData` Object (可选)
        * `contentType` String - 内容的MIME类型
        * `data` String -要发送的内容
* `completion` Function (可选)
  * `error` Error

注册一个 `scheme` 协议，用来发送 HTTP 请求作为响应.

这个方法的用法类似 `registerFileProtocol`，除非使用一个 `redirectRequest` 对象，或一个有 `url`, `method`,
`referrer`, `uploadData` 和 `session` 属性的对象来调用 `callback` .

默认这个 HTTP 请求会使用当前 session .如果你想使用不同的session值，你应该设置 `session` 为 `null`.
POST 请求应当包含 `uploadData` 对象.

### `protocol.unregisterProtocol(scheme[, completion])`

* `scheme` String
* `completion` Function (可选)
  * `error` Error

注销自定义协议 `scheme`.

### `protocol.isProtocolHandled(scheme, callback)`

* `scheme` String
* `callback` Function
  * `error` Error

将使用一个布尔值来调用 `callback` ，这个布尔值表示是否已经有一个 `scheme`的句柄

### `protocol.interceptFileProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `filePath` String
* `completion` Function (可选)
  * `error` Error
拦截 `scheme` 协议并且使用 `handler` 作为协议的新的句柄来发送响应文件.

### `protocol.interceptStringProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `data` String (可选)
* `completion` Function (可选)
  * `error` Error

拦截 `scheme` 协议并且使用 `handler` 作为协议的新的句柄来发送响应 `String`.

### `protocol.interceptBufferProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `buffer` Buffer (可选)
* `completion` Function (可选)
  * `error` Error

拦截 `scheme` 协议并且使用 `handler` 作为协议的新的句柄来发送响应 `Buffer`.

### `protocol.interceptHttpProtocol(scheme, handler[, completion])`

* `scheme` String
* `handler` Function
  * `request` Object
    * `url` String
    * `referrer` String
    * `method` String
    * `uploadData` [UploadData[]](structures/upload-data.md)
  * `callback` Function
    * `redirectRequest` Object
      * `url` String
      * `method` String
      * `session` Object (可选)
      * `uploadData` Object (可选)
        * `contentType` String - 内容的MIME类型
        * `data` String -要发送的内容
* `completion` Function (可选)
  * `error` Error

拦截 `scheme` 协议并且使用 `handler` 作为协议的新的句柄来发送新的响应 HTTP 请求.

### `protocol.uninterceptProtocol(scheme[, completion])`

* `scheme` String
* `completion` Function (可选)
  * `error` Error

取消对 `scheme` 的拦截，使用它的原始句柄进行处理.

[net-error]: https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h
[file-system-api]: https://developer.mozilla.org/en-US/docs/Web/API/LocalFileSystem
