## 本文介绍: 应用中的文件下载

> 控制从远程源的文件下载。

进程: [主进程](../glossary.md#main-process) 

 `DownloadItem`(下载项)是一个在Electron中展示下载项的
[EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)。
它被用于 `Session`模块中的 `will-download`事件，允许用户控制下载项。
```javascript
//主进程中
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.webContents.session.on('will-download', (event, item, webContents) => {
//设置保存路径，使Electron不提示保存对话框。
  item.setSavePath('/tmp/save.pdf')

  item.on('updated', (event, state) => {
    if (state === 'interrupted') {
      console.log('Download is interrupted but can be resumed')
    } else if (state === 'progressing') {
      if (item.isPaused()) {
        console.log('Download is paused')
      } else {
        console.log(`Received bytes: ${item.getReceivedBytes()}`)
      }
    }
  })
  item.once('done', (event, state) => {
    if (state === 'completed') {
      console.log('Download successfully')
    } else {
      console.log(`Download failed: ${state}`)
    }
  })
})
```

###实例事件

#### 事件: 'updated'
返回:
* `event` Event
* `state` String,可选以下状态:
 * `progressing`  - 下载正在进行中。
 * `interrupted`  - 下载已中断但可以恢复。
当下载获得更新且未完成时触发。


#### 事件: 'done'
返回:
* `event` Event
* `state` String,可选以下状态:
 * `completed`  - 下载已成功完成。
 * `cancelled`  - 下载已取消。
 * `interrupted`- 下载已中断，无法恢复。
当下载处于一个终止状态时触发。这包括了一个完成的下载，一个被取消的下载(via `downloadItem.cancel()`),
和一个被意外中断的下载(无法恢复)。

###实例方法

#### `downloadItem.setSavePath(path)`
* `path` String - 设置下载项目的保存文件路径。
API仅在会话的 `will-download` 回调函数中可用。
如果用户未通过API设置保存路径，Electron将使用原始保存路径(通常的提示保存对话框)。

#### `downloadItem.getSavePath()`
返回 `String`  - 下载项目的保存路径。
这将是通过 `downloadItem.setSavePath(path)` 设置的路径或从显示的保存对话框中选择的路径。

#### `downloadItem.pause()`
暂停下载

#### `downloadItem.isPaused()`
返回 `Boolean`  - 下载是否已暂停。

####`downloadItem.resume()`
恢复已暂停的下载。

####`downloadItem.canResume()`
返回 `Boolean`  - 下载是否可以恢复。

####`downloadItem.cancel()`
取消下载操作。

####`downloadItem.getURL()`
返回 `String`  -下载项目的原始网址。

####`downloadItem.getMimeType()`
返回 `String`  - 文件mime类型。

####`downloadItem.hasUserGesture()`
返回 `Boolean`  - 下载是否具有用户手势。

####`downloadItem.getFilename()`
返回 `String`  - 下载项目的文件名。
 **注意：**文件名不一定与保存在本地磁盘中的实际文件名相同。
 如果用户在提示的下载保存对话框中更改文件名，则保存文件的实际名称将不同。

####`downloadItem.getTotalBytes()`
返回 `Integer`  - 下载项目的总大小(以字节为单位)。
如果大小未知，则返回0。

####`downloadItem.getReceivedBytes()`
返回 `Integer`  - 下载项目的接收字节数。

####`downloadItem.getContentDisposition()`
返回 `String`  - 来自响应头的Content-Disposition字段。

####`downloadItem.getState()`
返回 `String`  - 当前状态。
可以是 `progressing`, `completed`, `cancelled`, `interrupted`.
**注意：**以下方法特别适用于在会话重新启动时恢复`cancelled`项。

####`downloadItem.getURLChain()`
返回 `String []` - 包括任何重定向的项目完整url链。

####`downloadItem.getLastModifiedTime()`
返回 `String`  -  Last-Modified标头值。

####`downloadItem.getETag()`
返回 `String`  -  ETag头值。

####`downloadItem.getStartTime()`
返回 `Double`  - 获得开始的下载时间(秒级)。
