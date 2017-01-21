# 本文介绍:crashReporter类
> 提交应用奔溃报告

进程: [主进程](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process)

下面是一个自动提交崩溃报告给服务器的例子 :

```javascript
const {crashReporter} = require('electron')

crashReporter.start({
  productName: 'YourName',
  companyName: 'YourCompany',
  submitURL: 'https://your-domain.com/url-to-submit',
  uploadToServer: true
})
```

可以使用下面的项目来创建一个服务器，用来接收和处理崩溃报告 :

* [socorro](https://github.com/mozilla/socorro)
* [mini-breakpad-server](https://github.com/electron/mini-breakpad-server)

崩溃报告保存在相对于应用程序的临时目录文件夹中。
对于 `YourName`的 `productName`，崩溃报告将存储在临时目录里的 `YourName Crashes`的文件夹中
你也可以通过 `app.setPath('temp', '/my/custom/temp')`定义相应目录.

## 方法


### `crashReporter.start(options)`

* `options` Object
  * `companyName` String (可选)
  * `submitURL` String - 以POST接收数据的URL
  * `productName` String (可选) -  默认为 `app.getName()`.
  * `uploadToServer` Boolean (可选) _macOS_ - 是否上传奔溃报告至系统服务,默认为 `true`.
  * `ignoreSystemCrashHandler` Boolean (可选) - 默认为 `false`.
  * `extra` Object (可选) - 一个你可以定义的对象，附带在崩溃报告上一起发送 . 只有字符串属性可以被正确发送，不支持嵌套对象.
  
此方法在使用任何其他 `crashReporter` API之前调用.
您可从中收集崩溃报告的每个进程（主/渲染器）。
当从不同进程调用时，可以向 `crashReporter.start`传递不同的选项。

 **注意：**在Windows和Linux上，Electron使用`breakpad'进行崩溃收集和报告。
可以从主进程和渲染器进程收集崩溃，但不能使用通过 `child_process`模块从子进程中收集

 **注意：**在macOS上，Electron是使用新的`crashpad'客户端进行崩溃收集和报告。
可以从主，渲染器和通过`child_process`模块创建的任何子进程中收集崩溃。
如果要启用崩溃报告，则需要使用 `crashReporter.start`从主进程初始化 `crashpad`。
一旦这样初始化，crashpad处理程序收集来自所有进程的崩溃。
你必须从渲染器进程调用 `crashReporter.start`，否则渲染器进程的奔溃报告将不含 `companyName`， `productName`或任何 `extra`信息。

### `crashReporter.getLastCrashReport()`

返回最后一个崩溃报告的日期和 ID[`CrashReport`](structures/crash-report.md).
如果没有过崩溃报告发送过来，或者还没有开始崩溃报告搜集，将返回 `null` .

### `crashReporter.getUploadedReports()`
返回所有上载的崩溃报告[`CrashReport[]`](structures/crash-report.md)
每个报告包含了上载日期和 ID.

### `crashReporter.getUploadToServer()` _macOS_
返回 `Boolean` - 是否应该将报告提交到服务器.  通过设置 `start`或 `setUploadToServer`方法.

 **注意:** 该API仅限主进程内调用

### `crashReporter.setUploadToServer(uploadToServer)` _macOS_
* `uploadToServer` Boolean _macOS_ - 是否应将报告提交到服务器
通常,是否提交是由用户对系统进行偏好设置而决定的.且不能在 `start`之前调用该方法,否则无效.

 **注意:** 该API仅限主进程内调用

## Crash Report Payload

崩溃报告将发送下面 `multipart/form-data` `POST` 型的数据给 `submitURL` :

* `ver` String - Electron 版本.
* `platform` String - 例如 'win32'.
* `process_type` String - 例如 'renderer'.
* `guid` String - 例如 '5e1286fc-da97-479e-918b-6bfb0c3d1c72'
* `_version` String - `package.json` 版本.
* `_productName` String - `crashReporter` `options`对象中的产品名字.
* `prod` String - 基础产品名字. 这种情况为 Electron.
* `_companyName` String - `crashReporter` `options`对象中的公司名字.
* `upload_file_minidump` File - `minidump` 格式的崩溃报告.
* `crashReporter` 中的 `extra` 对象的所有等级和一个属性.
  `options` object