# 本文介绍:使用系统原生的对话框

> 例如打开或保存文件等对话框

进程: [主进程](../glossary.md#main-process) 
对话框例子，展示了选择文件和目录:
```javascript
const {dialog} = require('electron')
console.log(dialog.showOpenDialog({properties: ['openFile', 'openDirectory', 'multiSelections']}))
```

你还可以通过remote在渲染进程中调用已打开的对话框:
```javascript
const {dialog} = require('electron').remote
console.log(dialog)
```

## 方法

### `dialog.showOpenDialog([browserWindow, ]options[, callback])`

* `browserWindow` BrowserWindow (可选)
* `options` Object
  * `title` String (可选)
  * `defaultPath` String (可选)
  * `buttonLabel` String (可选) - 确认按钮的自定义标签，当留空时将使用默认标签。
  * `filters` [FileFilter[]](structures/file-filter.md) (可选)
  * `properties` String[] (可选) - 包含对话框应该使用的功能，可以包含 `openFile`， `openDirectory`， `multiSelections`， `createDirectory`和 `showHiddenFiles`。
  * `normalizeAccessKeys` Boolean (可选) - 规范化跨平台的键盘访问键。默认值为`false`。
    用 `&` 连接和转换键盘访问键，以便它们在每个平台上正常工作
    `&` 字符在macOS上会被删除，而在Linux上被转换为  `_` ，在Windows上则保持不变。
    例如， `Vie＆w`的按钮标签将在Linux上转换为 `Vie_w`，在macOS上转换为 `View`，而Windows和Linux上表示 `Alt-W`。
* `callback` Function (可选)
  * `filePaths` String[] - 用户选择的文件路径数组

成功使用这个方法的话，就返回一个可供用户选择的文件路径数组 `String[]`，失败返回 `undefined`.

 `browserWindow`参数允许对话框将自身附加到父窗口，使其成为模态。
 `filters` 表示限定文件类型的数组，当你想限制用户到特定类型比如图片格式时，可以显示或选择它。
 例如：
```javascript
{
  filters: [
    {name: 'Images', extensions: ['jpg', 'png', 'gif']},
    {name: 'Movies', extensions: ['mkv', 'avi', 'mp4']},
    {name: 'Custom File Type', extensions: ['as']},
    {name: 'All Files', extensions: ['*']}
  ]
}
```
 `extensions` 数组不可包括通配符或含有点号的扩展名 (比如 `'png'`是正确的,而 `'.png'` 或 `'*.png'` 都是错的). 
 你可以直接用`'*'` 通配符显示所有文件
如果 `callback` 已通过传递,  API 结果将通过 `callback(filenames)`异步传递.
 **注意：**在Windows和Linux上，打开的对话框不能同时是文件选择器和目录选择器，因此如果在这些平台上将`properties`设置为`['openFile'，'openDirectory']` 选择器。

### `dialog.showSaveDialog([browserWindow, ]options[, callback])`

* `browserWindow` BrowserWindow (可选)
* `options` Object
  * `title` String (可选)
  * `defaultPath` String (可选)
  * `buttonLabel` String (可选) - 确认按钮的自定义标签，当留空时将使用默认标签。
  * `filters` [FileFilter[]](structures/file-filter.md) (可选)
* `callback` Function (可选)
  * `filename` String

返回 `String`，用户选择的文件的路径，如果提供回调，则返回 `undefined`。
 `browserWindow`参数允许对话框将自身附加到父窗口，使其成为模态。
 
 `filters`指定一个可以显示的文件类型数组，参见 `dialog.showOpenDialog`一个例子。
如果 `callback` 已通过传递,  API 结果将通过 `callback(filenames)`异步传递.

### `dialog.showMessageBox([browserWindow, ]options[, callback])`

* `browserWindow` BrowserWindow (可选)
* `options` Object
  * `type` String (可选) - 可选 `"none"`, `"info"`, `"error"`, `"question"` 或
  `"warning"`. On Windows, 在Windows上， `"question"`显示与 `"info"`相同的图标，除非您使用“图标”选项设置图标。
  * `buttons` String[] (可选) - 按钮的文本数组。在Windows上，空数组将导致一个按钮标记为`"确定"`。
  * `defaultId` Integer (可选) - 当消息框打开时，按钮数组中的按钮的索引将默认选择。
  * `title` String (可选) - 消息框的标题，一些平台可能不会显示。
  * `message` String - 消息框的内容.
  * `detail` String (可选) - 消息的额外信息.
  * `icon` [NativeImage](native-image.md) (可选)
  * `cancelId` Integer (可选) - 当用户取消对话框而不是单击对话框的按钮时，将返回该值。
    默认情况下，它是具有 `"cancel"`或 `"no"`作为标签的按钮的索引，如果没有这样的按钮，则为0。
    在macOS和Windows上，`"取消"`按钮的索引将始终用作 `cancelId`，即使它被指定。
  * `noLink` Boolean (可选) - 在Windows上,应用将尝试找出哪个 `按钮` 是常用按钮（如 `"取消"`或 `"是"`），并在对话框中显示其他作为命令链接。
    这可以使对话框以现代Windows应用程序的风格显示。
    如果你不喜欢这个行为，你可以设置 `noLink`为 `true`。
* `callback` Function (可选)
  * `response` Number - 被点击按钮的索引

返回`Integer`，即被点击的按钮的索引，如果提供回调，它返回undefined。

作用:显示消息框时,将阻止进程直到消息框关闭。最终返回点击的按钮的索引。

`browserWindow`参数允许对话框将自身附加到父窗口，使其成为模态。
如果 `callback` 已通过传递,  API 结果将通过 `callback(response)`异步传递.


### `dialog.showErrorBox(title, content)`

* `title` String - 显示在错误框中的标题
* `content` String - 显示在错误框中的文本内容

作用:显示一个显示错误消息的模态对话框。

这个API可以在 `app`模块触发 `ready`事件之前被安全地调用，它通常用在启动时报告错误。
在Linux上, `ready` 事件之前调用这个API，消息将被发送到stderr，并且不会出现GUI对话框。

## Sheets

在macOS上，如果您在 `browserWindow`参数中提供 `BrowserWindow`引用，或者如果没有提供窗口的话，对话框将显示为附加到窗口的工作表。
您可以调用 `BrowserWindow.getCurrentWindow（）。setSheetOffset（offset）`来更改与附加工作表的窗口框架的偏移量。