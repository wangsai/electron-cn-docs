# 本文介绍:screen(检索屏幕信息)

> `screen` 模块用于检索有关屏幕大小，显示，光标位置等的信息
进程: [主进程](../glossary.md#main-process) 和 [渲染进程](../glossary.md#renderer-process)        
**注意:** `app`模块的`ready`事件之后才能使用本模块.

`screen` 是一个 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter).

 **注意:** 在渲染进程 / 开发者工具栏, `window.screen` 是一个预设值的 DOM
属性, 所以这样写 `var screen = require('electron').screen` 将不会工作.
在我们下面的例子, 我们取代使用可变名字的 `electronScreen`.
一个例子，创建一个充满整个屏幕的窗口 :

```javascript
const electron = require('electron')
const {app, BrowserWindow} = electron

let win

app.on('ready', () => {
  const {width, height} = electron.screen.getPrimaryDisplay().workAreaSize
  win = new BrowserWindow({width, height})
  win.loadURL('https://github.com')
})
```

另一个例子，在此页外创建一个窗口:

```javascript
const electron = require('electron')
const {app, BrowserWindow} = require('electron')

let win

app.on('ready', () => {
  let displays = electron.screen.getAllDisplays()
  let externalDisplay = displays.find((display) => {
    return display.bounds.x !== 0 || display.bounds.y !== 0
  })

  if (externalDisplay) {
    win = new BrowserWindow({
      x: externalDisplay.bounds.x + 50,
      y: externalDisplay.bounds.y + 50
    })
    win.loadURL('https://github.com')
  }
})
```
## `Display` 对象

`Display` 对象表示一个连接到系统的物理显示. 一个虚设的 `Display` 或许存在于一个无头系统(headless system)中，或者一个 `Display` 对应一个远程的, 虚拟的display.

* `display` object
  * `id` Integer - 与display 相关的唯一性标志.
  * `rotation` Integer - 可以是 0, 1, 2, 3, 每个代表了屏幕旋转的度数 0, 90, 180, 270.
  * `scaleFactor` Number - Output device's pixel scale factor.
  * `touchSupport` String - 可以是 `available`, `unavailable`, `unknown`.
  * `bounds` Object
  * `size` Object
  * `workArea` Object
  * `workAreaSize` Object
  
## 事件列表

`screen` 模块有如下事件:

### 事件: 'display-added'
返回:
* `event` Event
* `newDisplay` [Display](structures/display.md)
`newDisplay` 被添加时触发.

### 事件: 'display-removed'
返回:
* `event` Event
* `oldDisplay` [Display](structures/display.md)
移除`oldDisplay` 后触发

### 事件: 'display-metrics-changed'
返回:
* `event` Event
* `display` [Display](structures/display.md)
* `changedMetrics` String[]  描述变化的字符串数组。
   可选 `bounds`， `workArea`， `scaleFactor`和 `rotation`。
当一个 `display` 中的一个或更多的 metrics 更改时触发。



## 方法列表
The `screen` 模块有如下方法:

### `screen.getCursorScreenPoint()`
返回 `Object`:
* `x` Integer
* `y` Integer
当前鼠标指针的绝对位置。

### `screen.getPrimaryDisplay()`
返回 [`Display`](structures/display.md) - 主显示屏

### `screen.getAllDisplays()`

返回 [`Display[]`](structures/display.md) - 所有可用显示屏组成的数组

### `screen.getDisplayNearestPoint(point)`

* `point` Object
  * `x` Integer
  * `y` Integer

返回 [`Display`](structures/display.md) - 返回离指定点最近的显示屏

### `screen.getDisplayMatching(rect)`

* `rect` [Rectangle](structures/rectangle.md)

返回 [`Display`](structures/display.md) - 与提供的边界范围最密切相关的显示屏
