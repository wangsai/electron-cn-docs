## 本文介绍: HTTP/HTTPS请求的握手响应

> 通常用于HTTP/HTTPS请求失败/成功等处理. 

进程: [主进程](../glossary.md#main-process)      
`IncomingMessage`是由 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)响应可读流[Readable Stream](https://nodejs.org/api/stream.html#stream_readable_streams)接口

###实例事件

#### 事件: 'data'
返回:
* `chunk` Buffer - 响应主体的数据块.
`data` 事件通常用来响或回调数据传送到应用。

#### 事件: 'end'
表示响应主体已经结束。

#### 事件: 'aborted'
当一个正在进行HTTP事务期间的请求被取消时触发

#### 事件: 'error'
返回:
`error` Error - 错误字符串，标识失败原因。
当流响应数据事件时遇到错误时触发

###实例属性
#### `response.statusCode`
HTTP响应状态的数字代码。

#### `response.statusMessage`
HTTP状态消息文本。

#### `response.headers`
响应头对象. 该`headers`对象的格式如下：
* 响应名称都要小写。
* 每个响应名称必须是对象中的键.
* 每个响应名称和它的值是可匹配的

#### `response.httpVersion`
HTTP协议的版本号。典型值是'1.0'或'1.1'。
 另外 `httpVersionMajor` 和 `httpVersionMinor` 分别返回HTTP主要和次要版本号

#### `response.httpVersionMajor`
HTTP协议的主版本号。

#### `response.httpVersionMinor`
HTTP协议的次要版本号。