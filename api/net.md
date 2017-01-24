# 本文介绍:原生 Chromium 的 networking 库的应用

> 使用Chromium的原生网络库解决HTTP / HTTPS请求

进程: [主进程](../glossary.md#main-process)      
 `net` 模块是一个客户端 API 用于解决 HTTP(S) 请求问题。它和 Node.js 中的 [HTTP](https://nodejs.org/api/http.html) 和 [HTTPS](https://nodejs.org/api/https.html) 模块比较相似，但是它使用了 Chromium 的原生 API 来替代 Node.js 的方案 ，相对而言更加适合 web 端的请求处理。

以下是部分简要的原因来阐述为什么你需要考虑使用 `net` 来替代 Node.js 的原生模块：

* 自动管理系统代理设置，支持 wpad 协议和 pac 代理配置文件。
* 自动使用隧道通过 HTTPS 请求。
* 支持 basic, digest, NTLM, Kerberos 或 negotiate 等身份验证模式作为身份验证代理。
* 支持 traffic monitoring proxies: 类似于 Fiddler 的代理用于监控和操作网络请求。

`net` 模块的 API 在设计上特别的模仿了 Node.js 的 API 从而达到比较接近的体验。在 API 组件中包含了类，方法，属性还有事件等名称，这些都像是在使用 Node.js 的 API。

例如下面一个例子就快速展示了如何使用 `net` API ：
```javascript
const {app} = require('electron')
app.on('ready', () => {
  const {net} = require('electron')
  const request = net.request('https://github.com')
  request.on('response', (response) => {
    console.log(`STATUS: ${response.statusCode}`)
    console.log(`HEADERS: ${JSON.stringify(response.headers)}`)
    response.on('data', (chunk) => {
      console.log(`BODY: ${chunk}`)
    })
    response.on('end', () => {
      console.log('No more data in response.')
    })
  })
  request.end()
})
```

也就是说，它对于你使用过 Node.js 的 [HTTP](https://nodejs.org/api/http.html)/[HTTPS](https://nodejs.org/api/https.html) 模块来说是非常的相似的。

`net` API 只有在应用触发 `ready` 事件后才可以使用。如果在 `ready` 事件前尝试使用会抛出一个错误。

## 方法

### `net.request(options)`

* `options` (Object | String) - `ClientRequest` 的构造参数。

返回 [`ClientRequest`](./client-request.md)

使用提供的 `options` 创建一个[`ClientRequest`](./ client-request.md)实例，它们直接转发给 `ClientRequest`构造函数。
 `net.request`方法将用于根据`options`对象中指定的协议方案发出安全和不安全的HTTP请求。
