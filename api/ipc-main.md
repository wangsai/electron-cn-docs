# 本文介绍: ipcMain(主进程回复或接收渲染进程发送的消息)
> 主进程中进行处理由渲染进程发送的异步或同步消息.

浅显的打个比方,这就是渲染进程给主进程挂个号,这里就开始忙活起来.当然,你也可以从主进程中向渲染进程发送消息.

进程: [主进程](../glossary.md#main-process)       

`ipcMain` 模块是类[EventEmitter](https://nodejs.org/api/events.html) 的实例.


## 发送消息
同样也可以从主进程向渲染进程发送消息，查看更多 [webContents.send][web-contents-send] .

* 发送消息，事件名为 `channel`.
* 回应同步消息, 你可以设置 `event.returnValue`.
* 回应异步消息, 你可以使用 `event.sender.send(...)`.

在主进程和渲染进程之间发送和处理消息的例子:
```javascript
// 主进程中
const {ipcMain} = require('electron')
ipcMain.on('asynchronous-message', (event, arg) => {
  console.log(arg)  // 输出 "ping"
  event.sender.send('asynchronous-reply', 'pong')
})

ipcMain.on('synchronous-message', (event, arg) => {
  console.log(arg)  // 输出 "ping"
  event.returnValue = 'pong'
})
```

```javascript
// 渲染进程中(即网页).
const {ipcRenderer} = require('electron')
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')) // 输出 "pong"

ipcRenderer.on('asynchronous-reply', (event, arg) => {
  console.log(arg) // 输出 "pong"
})
ipcRenderer.send('asynchronous-message', 'ping')
```

## 监听事件

### `ipcMain.on(channel, listener)`
* `channel` String
* `listener` Function
监听 `channel`, 当新消息到达，将通过 `listener(event, args...)` 调用 `listener`.

### `ipcMain.once(channel, listener)`
* `channel` String
* `listener` Function - 一次性的 `listener` 方法.
当消息到达 `channel` 时, `listener` 在被请求调用后删除监听.

### `ipcMain.removeListener(channel, listener)`
* `channel` String
* `listener` Function
从指定 `channel` 的监听队列中删除特定的 `listener` .

### `ipcMain.removeAllListeners([channel])`
* `channel` String (optional)
删除所有监听，或指定 `channel` 的所有监听

## 事件对象
传递给 `callback` 的 `event` 对象有如下方法:

### `event.returnValue`
将此设置成同步消息中返回的值.

### `event.sender`
返回发送消息的 `webContents` ，你可以调用 `event.sender.send`  来回复异步消息，更多信息 [webContents.send][web-contents-send].

[web-contents-send](web-contents.md#webcontentssendchannel-arg1-arg2-)