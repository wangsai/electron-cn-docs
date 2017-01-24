#本文介绍:Tray(系统通知区域如Win右下角)类 的应用

>将图标和上下文菜单添加到系统的通知区域。

进程: [主进程](../glossary.md#main-process)           

`Tray`是一个[EventEmitter][event-emitter].

```javascript
const {app, Menu, Tray} = require('electron')

let tray = null
app.on('ready', () => {
  tray = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    {label: 'Item1', type: 'radio'},
    {label: 'Item2', type: 'radio'},
    {label: 'Item3', type: 'radio', checked: true},
    {label: 'Item4', type: 'radio'}
  ])
  tray.setToolTip('This is my application.')
  tray.setContextMenu(contextMenu)
})
```

 __平台限制:__
* 在Windows上，建议使用ICO图标以获得最佳的视觉效果。
* 如果应用程序指示器没有一个上下文菜单时,它将不会显示。
* 在Linux中，如果支持应用程序指示器则使用它，否则使用 `GtkStatusIcon`。
* 在Linux的发行版中,仅支持应用程序指示器，您需要安装 `libappindicator1` ,以便使用托盘图标(tray icon)。
* 在Linux中，如果使用了应用指示器, `click`事件则被忽略.
* 在Linux中，为了让单独的 `MenuItem` 起效，需要再次调用 `setContextMenu` .例如:
```javascript
const {app, Menu, Tray} = require('electron')

let appIcon = null
app.on('ready', () => {
  appIcon = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    {label: 'Item1', type: 'radio'},
    {label: 'Item2', type: 'radio'}
  ])

//更改上下文菜单
  contextMenu.items[1].checked = false
//因为我们修改了上下文菜单，所以再次为Linux调用
  appIcon.setContextMenu(contextMenu)
})
```
如果你想在所有平台上保持完全相同的行为，你不应该依赖于click事件，而是附加上下文菜单到托盘图标。

### `new Tray(image)`
* `image` ([NativeImage](native-image.md) | String)
创建一个与 `image` 相关的 icon.

### 实例事件

 `Tray` 模块内含下列事件:

#### 事件: 'click'

* `event` Event
  * `altKey` Boolean
  * `shiftKey` Boolean
  * `ctrlKey` Boolean
  * `metaKey` Boolean
* `bounds` [Rectangle](structures/rectangle.md) - 托盘图标的边界

单击托盘图标时触发

#### 事件: 'right-click' _macOS_ _Windows_

* `event` Event
  * `altKey` Boolean
  * `shiftKey` Boolean
  * `ctrlKey` Boolean
  * `metaKey` Boolean
* `bounds` [Rectangle](structures/rectangle.md) - 托盘图标的边界

右击托盘图标时触发

#### 事件: 'double-click' _macOS_ _Windows_

* `event` Event
  * `altKey` Boolean
  * `shiftKey` Boolean
  * `ctrlKey` Boolean
  * `metaKey` Boolean
* `bounds` [Rectangle](structures/rectangle.md) - 托盘图标的边界

双击托盘图标时触发

#### 事件: 'balloon-show' _Windows_
显示托盘气泡时触发

#### 事件: 'balloon-click' _Windows_
点击托盘气泡时触发

#### 事件: 'balloon-closed' _Windows_
托盘气泡因超时而关闭或用户手动进行关闭时触发

#### 事件: 'drop' _macOS_

托盘图标的任何可拖动项被删除的时候触发

#### 事件: 'drop-files' _macOS_

* `event` Event
* `files` String[] - 已删除文件的路径.

把文件拖进托盘图标时触发

#### 事件: 'drop-text' _macOS_

* `event` Event
* `text` String - 被拖动的文本

把文本拖进托盘图标中时触发

#### 事件: 'drag-enter' _macOS_

拖动操作进入托盘图标时触发

#### 事件: 'drag-leave' _macOS_

拖动操作退出托盘图标时触发

#### 事件: 'drag-end' _macOS_
拖动操作在托盘上或其它位置停止拖动时触发

## 实例方法

 `Tray` 模块有以下方法:

#### `tray.destroy()`
销毁托盘图标

#### `tray.setImage(image)`
* `image` ([NativeImage](native-image.md) | String)

让 `image` 与托盘图标关联起来.

#### `tray.setPressedImage(image)` _macOS_
* `image` [NativeImage](native-image.md)

在 macOS 上点触托盘图标的时候， 让 `image` 与之相关联.


#### `tray.setToolTip(toolTip)`
* `toolTip` String

鼠标悬停在托盘图标时的提示文本.

#### `tray.setTitle(title)` _macOS_
* `title` String

状态栏里托盘图标旁边需要显示的标题。

#### `tray.setHighlightMode(mode)` _macOS_

* `mode` String - 高亮方式,可选以下:
  * `selection` - 单击托盘图标或其上下文菜单处于打开状态时,进行高亮。这是默认值。
  * `always` - 始终高亮
  * `never` - 禁止高亮
  
当托盘图标被点击的时候，是否高亮它的背景色(蓝色).

**注意:** 当窗口可见性进行更改时,你可以使用 `highlightMode`模块让一个 [`BrowserWindow`](browser-window.md) 切换为 `'never'` 或 `'always'` 模式.
```javascript
const {BrowserWindow, Tray} = require('electron')
const win = new BrowserWindow({width: 800, height: 600})
const tray = new Tray('/path/to/my/icon')
tray.on('click', () => {
  win.isVisible() ? win.hide() : win.show()
})
win.on('show', () => {
  tray.setHighlightMode('always')
})
win.on('hide', () => {
  tray.setHighlightMode('never')
})
```

#### `tray.displayBalloon(options)` _Windows_

* `options` Object
  * `icon` ([NativeImage](native-image.md) | String) - (可选)
  * `title` String - (可选)
  * `content` String - (可选)
  
显示托盘气球。

#### `tray.popUpContextMenu([menu, position])` _macOS_ _Windows_

* `menu` Menu (可选)
* `position` Object (可选) - 弹出菜单的position位置。
  * `x` Integer
  * `y` Integer

弹出托盘图标的上下文菜单。
划过`menu`时则显示`menu`,而不是托盘图标的上下文菜单。
`position`仅在Windows上可用，默认为(0，0)。

#### `tray.setContextMenu(menu)`

* `menu` Menu
设置此图标的上下文菜单。

#### `tray.getBounds()` _macOS_ _Windows_
返回 [`Rectangle`](structures/rectangle.md)
这个图标的 `bounds` 是个 `Object`对象

#### `tray.isDestroyed()`
返回 `Boolean` - 托盘图标是否已被销毁.

[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
