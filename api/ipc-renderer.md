# 本文介绍: ipcRenderer(从渲染进程发送消息至主进程)
> 捕获渲染进程发送至主进程的同步或异步消息  

 **注意**ipcRenderer和ipcMain的区别非常简单,顾名思义,ipcRenderer是在渲染进程中定义并使用,
 以便捕获发送的消息,而ipcMain则是在主进程中接收或回复消息.

进程: [渲染进程](../glossary.md#renderer-process)     
 `ipcRenderer`模块是[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)类的一个实例。
 它是渲染进程与主进程之间同步和异步消息的主要过程。更多示例常见[ipcMain](ipc-main.md)的代码。

## 事件方法

### `ipcRenderer.on(channel, listener)`
* `channel` String
* `listener` Function
监听 `channel`, 当有新消息到达，使用 `listener(event, args...)` 调用 `listener` .

### `ipcRenderer.once(channel, listener)`
* `channel` String
* `listener` Function - 一次性的 `listener` 方法.
当消息到达 `channel` 时, `listener` 在被请求调用后删除监听.

### `ipcRenderer.removeListener(channel, listener)`
* `channel` String
* `listener` Function
从指定 `channel` 的监听队列中删除特定的 `listener` .

### `ipcRenderer.removeAllListeners([channel])`
* `channel` String (optional)
删除所有监听，或指定 `channel` 的所有监听

### `ipcRenderer.send(channel[, arg1][, arg2][, ...])`
* `channel` String
* `...args` any[]
通过 `channel` 向主进程发送异步消息，也可以发送任意参数.参数会被JSON序列化，之后就不会包含函数或原型链.
主进程通过使用 `ipcMain` 模块来监听 `channel`，从而处理消息.

### `ipcRenderer.sendSync(channel[, arg1][, arg2][, ...])`
* `channel` String
* `...args` any[]
通过 `channel` 向主进程发送同步消息，也可以发送任意参数.参数会被JSON序列化，之后就不会包含函数或原型链.
主进程通过使用 `ipcMain` 模块来监听 `channel`，从而处理消息,
通过 `event.returnValue` 来响应.
__注意:__ 发送同步消息将会阻塞整个渲染进程,除非你知道你在做什么，否则就永远不要用它 .

### `ipcRenderer.sendToHost(channel[, arg1][, arg2][, ...])`
* `channel` String
* `...args` any[]
类似 `ipcRenderer.send` ，但是它的事件将发往 host page 的 `<webview>` 元素，而不是主进程.