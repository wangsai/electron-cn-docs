# 本文介绍: Debugger类
> 一个Chrome的远程调试协议的替代传输。
进程: [主进程](../glossary.md#main-process)

JavaScript运行后,使用Chrome调试工具中的[special binding][rdp]与页面交互和检测它们.


```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

try {
  win.webContents.debugger.attach('1.1')
} catch (err) {
  console.log('Debugger attach failed : ', err)
}

win.webContents.debugger.on('detach', (event, reason) => {
  console.log('Debugger detached due to : ', reason)
})

win.webContents.debugger.on('message', (event, method, params) => {
  if (method === 'Network.requestWillBeSent') {
    if (params.request.url === 'https://www.github.com') {
      win.webContents.debugger.detach()
    }
  }
})

win.webContents.debugger.sendCommand('Network.enable')
```

###实例方法

#### `debugger.attach([protocolVersion])`
* `protocolVersion`  String（可选） - 请求的调试协议版本。
将调试器附加到`webContents'。

#### `debugger.isAttached()`
Returns `Boolean` - 调试器是否已附加到`webContents'。

#### `debugger.detach()`
从`webContents`里分离调试器。

#### `debugger.sendCommand(method[, commandParams, callback])`

* `method` String - 方法名, 名称通过远程调试协议定义.
* `commandParams` Object (optional) -带请求参数的JSON对象。
* `callback` Function (optional) - 响应方法
  * `error` Object - 错误后的指示命令
  * `result` Any -  由 'returns'属性 在远程调试协议命令描述中 定义 的响应

发送给定命令到调试目标。

###实例事件

#### 事件: 'detach'
* `event` Event
* `reason` String -分离调试器的原因。
在`webContents`关闭或devtools被调用为附加的`webContents`时终止调试会话。



#### 事件: 'message'
* `event` Event
* `method` String - 方法名
* `params` Object - 由远程调试协议中的 `parameters` 属性定义的事件参数。

当调试提交报告时触发

[rdp]: https://developer.chrome.com/devtools/docs/debugger-protocol
[`webContents.findInPage`]: web-contents.md#contentsfindinpagetext-options