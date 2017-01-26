# 本文介绍:shell(使用默认应用程序管理文件和URL,如点击url时调用默认浏览器)

> `shell`模块提供与 桌面集成 相关的功能

进程: [主进程](../glossary.md#main-process)         , [Renderer](../glossary.md#renderer-process)

在用户的默认浏览器中打开网址的示例：

```javascript
const {shell} = require('electron')

shell.openExternal('https://github.com')
```

## 方法列表

The `shell` 模块有如下方法:

### `shell.showItemInFolder(fullPath)`
* `fullPath` String
返回 `Boolean` - 项目是否已成功显示
在文件管理器中显示给定的文件。一般情况下还会选中它.

### `shell.openItem(fullPath)`
* `fullPath` String
返回 `Boolean` - 项目是否已成功打开
以默认方式打开给定的文件。

### `shell.openExternal(url[, options, callback])`
* `url` String
* `options` Object (可选) _macOS_
  * `activate` Boolean - `true`将打包默认的应用程序。默认值为 `true`。
* `callback` Function (可选) -如果指定将执行异步打开. _macOS_
  * `error` Error
返回 `Boolean` - 是否有应用程序可用于打开URL。如果指定回调，则始终返回true。

以系统默认设置打开给定的外部协议URL。 (例如，mailto：用户的默认邮件代理中的URL)。

### `shell.moveItemToTrash(fullPath)`
* `fullPath` String
返回 `Boolean` - 项目是否已成功移至垃圾桶

将给定文件移动到回收站，并返回操作的布尔状态。

### `shell.beep()`

播放蜂鸣声。

### `shell.writeShortcutLink(shortcutPath[, operation], options)` _Windows_
* `shortcutPath` String
* `operation` String (可选) - 操作方式,默认为 `create`,可选:
  * `create` - 创建新的快捷方式，已存在则覆盖。
  * `update` -仅在现有快捷方式上更新指定的属性
  * `replace` - 覆盖现有快捷方式，如果快捷方式不存在则失败。
* `options` [ShortcutDetails](structures/shortcut-details.md)
返回 `Boolean` - 是否成功创建快捷方式

在 `shortcutPath`处创建或更新快捷方式链接。

### `shell.readShortcutLink(shortcutPath)` _Windows_
* `shortcutPath` String
返回 [`ShortcutDetails`](structures/shortcut-details.md)

解析链接至 `shortcutPath`的快捷方式。
发生任何错误时将抛出异常。