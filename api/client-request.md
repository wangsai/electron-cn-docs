# 本文介绍:ClientRequest类
>客户端的HTTP / HTTPS请求。

进程：[主进程](../ glossary.md＃main-process)     
 `ClientRequest`是由[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)来实现[Writable Stream](https://nodejs.org/api/stream.html#stream_writable_streams)

###`new ClientRequest(options)`

* `options`(Object | String) -  `options`是String，它被解释为请求URL。 `options` 是Object，则按以下属性请求：
  * `method` String(可选) - HTTP请求方法。默认为GET方法。
  * `url` String(可选) - 请求URL。协议为http或https。
  * `session` Object(可选) - [`Session`](session.md)与请求相关联。 `session` 优先于 `partition`。
  * `partition` String(可选) - [`partition`]的名称(session.md)与请求相关联。默认为空字符串。
 如果 `session`是显式的指定， `partition`被忽略。
  * `protocol` String(可选) - “scheme：”形式的协议方案。可选“http：”或“https：”。默认为“http：”。
  * `host` String(可选) - 作为连接提供的服务器主机,主机名和端口号“hostname：port”
  * `hostname` String(可选) - 服务器主机名。
  * `port` Integer(可选) - 服务器的侦听端口号。
  * `path` String(可选) - 请求URL的路径部分。

`options`属性，如 `protocol`， `host`， `hostname`， `port`和 `path`严格遵循Node.js模型中描述的
[URL](https://nodejs.org/api/url.html)模块。

例如，我们可以创建与“github.com”相同的请求如下：

```JavaScript
const request = net.request({
  方法：'GET'，
  protocol：'https：'，
  hostname：'github.com'，
  端口：443，
  路径：'/'
})
```

###实例事件

####事件：'response'

返回：

* `response` (IncomingMessage) - 表示HTTP响应消息的对象。

####事件：'login'
返回：
* `authInfo`对象
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback`函数

当认证代理请求用户凭据时发出。
`callback`函数期望用用户凭据回调：

* `username` String
* `password` String

```JavaScript
request.on('login'，(authInfo，callback)=> {
  callback('username'，'password')
})
```
提供空凭证将取消请求并报告身份验证
错误响应对象：

```JavaScript
request.on('response'，(response)=> {
  console.log(`STATUS：$ {response.statusCode}`);
  response.on('error'，(error)=> {
    console.log(`ERROR：$ {JSON.stringify(error)}`)
  })
})
request.on('login'，(authInfo，callback)=> {
  回电话()
})
```

####事件：'finish'
在请求的数据的最后一个数据块被写入之后发出`request`对象。

####事件：'abort'
当'请求'被中止时发出。 如果 `request`已经关闭, `abort`事件不会被触发。

####事件：'error'
返回：
* `error` Error - 一个错误对象，提供关于失败的一些信息。

`net`模块无法发出网络请求时发出。通常在 `request`对象发出一个 `error`事件，一个 `close`事件随后
跟随并且不提供响应对象。

####事件：'close'

作为HTTP请求 - 响应事务中的最后一个事件。 “close”
事件表示没有更多的事件将发出在 `request`或者
`response`对象。

###实例属性

####`request.chunkedEncoding`
指定请求是否将使用HTTP分块传输编码,默认为false。
该属性是可读写的，但它可以只在第一次写操作之前设置，在第一次写入后设置 `chunkedEncoding`属性后会抛出一个错误。
如果请求较大且复杂时,使用分块可有效提高效率.

###实例方法

####`request.setHeader(name，value)`
* `name` String - 一个额外的HTTP头名称。
* `value` String - 一个额外的HTTP头值。
添加额外的HTTP标头。标题名称将按原样发出
小写。它只能在第一次写入之前调用。
第一次写入之后调用此方法将抛出一个错误。

####`request.getHeader(name)`
* `name` String - 指定一个额外的标题名称。
返回String - 以前设置的额外头名称的值。

####`request.removeHeader(name)`
* `name` String - 指定一个额外的标题名称。

删除先前设置的额外标题名称。这遇到了
删除先前设置的额外标题名称。此方法只能调用
先写入。试图在第一次写之后调用它会抛出一个错误。

####`request.write(chunk [，encoding] [，callback])`
* `chunk`(String | Buffer) - 请求主体数据的一个块。如果是
字符串，它将使用指定的编码转换为Buffer。
* `encoding` String(可选) - 用于将字符串块转换为Buffer
对象。默认为'utf-8'。
* `callback` Function(可选) - 在写操作结束后调用。

`callback` 本质上是一个在保持目的中引入的虚拟函数
与Node.js API的相似性。它在下一个tick中异步调用
之后`chunk`内容已传送到Chromium网络层。
与Node.js实现相反，它不能保证 `chunk`
在调用 `callback`之前，内容已经被刷新了。

向请求正文添加一个数据块。第一次写操作可能导致
请求头在电线上发出。在第一写入操作之后，
它不允许添加或删除自定义标题。

####`request.end([chunk] [，encoding] [，callback])`

* `chunk`(String | Buffer)(可选)
* `encoding` String(可选)
* `callback`函数(可选)

发送请求数据的最后一个块。后续写入或结束操作
将不被允许。 `finish`事件是在结束操作之后发出的。

####`request.abort()`

取消正在进行的HTTP事务。如果请求已经发出了
`close`事件，中止操作将没有效果。否则正在进行
事件将发出`abort`和`close`事件。此外，如果有正在进行
响应对象，它会发出`aborted`事件。