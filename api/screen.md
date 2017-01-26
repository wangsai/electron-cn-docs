# 本文介绍:screen(检索屏幕信息)

> `screen` 模块用于检索有关屏幕大小，显示，光标位置等的信息
进程: [主进程](../glossary.md#main-process)     
        [渲染进程](../glossary.md#renderer-process)      
           
 **注意:** `app`模块必须用在 `ready`事件后.

 `screen` 是一个 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter).

 **注意:** 在渲染进程 / 开发者工具栏, `window.screen` 是一个预设值的 DOM属性, 所以 `var screen = require('electron').screen` 这样写的话是无效的.
创建全屏窗口的例子 :

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

页面外部创建窗口的例子:
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

`Display`对象表示连接到系统的物理显示器。虚拟 `Display` 可以存在于无头系统上， `Display` 也可以是对应于远程的虚拟显示器。
* `display` object
  * `id` Integer -  与显示相关联的唯一标识符。
  * `rotation` Integer - 可选 `0`, `1`, `2`, `3`, 每个代表顺时针方向的屏幕旋转角度, 可选 `0`， `90`， `180`， `270`。
  * `scaleFactor` Number -输出设备的像素比例因子。
  * `touchSupport` String - 是否支持触摸,可选 `available`, `unavailable`, `unknown`.
  * `bounds` Object [Rectangle](rectangle.md)
  * `size` Object
   * `height` Number
   * `width` Number
  *  `workArea` [Rectangle](rectangle.md)
  * `workAreaSize` Object
   * `height` Number
   * `width` Number
  
## 事件列表

`screen` 模块有如下事件:

### 事件: 'display-added'
返回:
* `event` Event
* `newDisplay` [Display](structures/display.md)
添加 `newDisplay` 时触发.

### 事件: 'display-removed'
返回:
* `event` Event
* `oldDisplay` [Display](structures/display.md)

移除`oldDisplay` 时触发

### 事件: 'display-metrics-changed'
返回:
* `event` Event
* `display` [Display](structures/display.md)
* `changedMetrics` String[]  描述变化的字符串数组。
   可选 `bounds`， `workArea`， `scaleFactor`和 `rotation`。
   
当更改 `display` 中的 单个或多个 metrics 时触发。



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
