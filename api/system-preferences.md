# 本文介绍:配置系统

> 配置系统

进程: [主进程](../glossary.md#main-process)         
```javascript
const {systemPreferences} = require('electron')
console.log(systemPreferences.isDarkMode())
```

## 事件列表

 `systemPreferences` 对象提供以下事件:

### 事件: 'accent-color-changed' _Windows_
返回:
* `event` Event
* `newColor` String - 用户给系统颜色设置的新的 RGBA 色值。

### 事件: 'color-changed' _Windows_
返回:
* `event` Event

### 事件: 'inverted-color-scheme-changed' _Windows_
返回:
* `event` Event
* `invertedColorScheme`  Boolean - `true` 代表的是如果正在使用的是反色方案(高对比度主题等), 否则为 `false`

## 方法列表

### `systemPreferences.isDarkMode()` _macOS_
返回 `Boolean` - 系统是否处于深色模式.

### `systemPreferences.isSwipeTrackingFromScrollEventsEnabled()` _macOS_
返回 `Boolean` - 是否开启页面间滑动功能。

### `systemPreferences.postNotification(event, userInfo)` _macOS_
* `event` String
* `userInfo` Object
在 macOS 上使用原生系统通知来发布 `event` 。
 `userInfo` 是个对象,它包含了用户向通知发送的信息字典。

### `systemPreferences.postLocalNotification(event, userInfo)` _macOS_
* `event` String
* `userInfo` Object
在 macOS 上使用原生系统通知来发布 `event` 。
 `userInfo` 是个对象,它包含了用户向通知发送的信息字典。

### `systemPreferences.subscribeNotification(event, callback)` _macOS_
* `event` String
* `callback` Function
  * `event` String
  * `userInfo` Object
在 macOS 上订阅一个原生消息。
当相关 `event` 发生， `callback` 将会被 `callback(event, userInfo)` 调用。
 `userInfo` 是个对象,它包含了用户向通知发送的信息字典。

订阅者的 `id` 将会被返回，它能够用于取消订阅相关 `event` 。

这个API在底层上订阅于 `NSDistributedNotificationCenter`，
 `event` 的一些实例值如下：

* `AppleInterfaceThemeChangedNotification`
* `AppleAquaColorVariantChanged`
* `AppleColorPreferencesChangedNotification`
* `AppleShowScrollBarsSettingChanged`

### `systemPreferences.unsubscribeNotification(id)` _macOS_

* `id` Integer

移除当前 `id` 下的订阅。

### `systemPreferences.subscribeLocalNotification(event, callback)` _macOS_

* `event` String
* `callback` Function
  * `event` String
  * `userInfo` Object

大体上如同 `subscribeNotification` ，但默认使用本地 `subscribeNotification` 。一些事件必须使用它，比如 `NSUserDefaultsDidChangeNotification` 。

### `systemPreferences.unsubscribeLocalNotification(id)` _macOS_

* `id` Integer

大体上如同 `unsubscribeNotification` ， 但是是从 `NSNotificationCenter` 中移除订阅者。

### `systemPreferences.getUserDefault(key, type)` _macOS_

* `key` String
* `type` String - Can be `string`, `boolean`, `integer`, `float`, `double`,
  `url`, `array`, `dictionary`

获取系统偏好中相应的 `key` 的值。

这个 API 在 macOS 系统中使用的是 `NSUserDefaults` 。以下是一些热门的 `key` 和 `type`：

* `AppleInterfaceStyle`:  `string`
* `AppleAquaColorVariant`:  `integer`
* `AppleHighlightColor`:  `string`
* `AppleShowScrollBars`:  `string`
* `NSNavRecentPlaces`:  `array`
* `NSPreferredWebServices`:  `dictionary`
* `NSUserDictionaryReplacementItems`:  `array`

### `systemPreferences.setUserDefault(key, type, value)` _macOS_

* `key` String
* `type` String - 详见 [`getUserDefault`][#systempreferencesgetuserdefaultkey-type-macos]
* `value` String

设置系统偏好中相应的 `key` 的值。

需要注意的是 `type` 需要与实际的类型的 `value` 对应。不然会抛出一个异常。

这个 API 在 macOS 系统中使用的是 `NSUserDefaults` 。以下是一些热门的 `key` 和 `type`：

* `ApplePressAndHoldEnabled`:  `boolean`


### `systemPreferences.isAeroGlassEnabled()` _Windows_

如果 [DWM composition][dwm-composition] (毛玻璃效果)开启则会返回 `true` 否则返回 `false` 。

以下是一个实例去使用它来确定是否应该创建一个透明的窗口(透明的窗口在 DWM composition 禁止的情况下无法正确运行)：

```javascript
const {BrowserWindow, systemPreferences} = require('electron')
let browserOptions = {width: 1000, height: 800}

// 如果平台支持的话便新建一个透明的窗口。
if (process.platform !== 'win32' || systemPreferences.isAeroGlassEnabled()) {
  browserOptions.transparent = true
  browserOptions.frame = false
}

// 创建窗口。
let win = new BrowserWindow(browserOptions)

// 区分环境进行加载。
if (browserOptions.transparent) {
  win.loadURL(`file://${__dirname}/index.html`)
} else {
//没有透明度则加载基本样式
  win.loadURL(`file://${__dirname}/fallback.html`)
}
```

[dwm-composition]:https://msdn.microsoft.com/en-us/library/windows/desktop/aa969540.aspx

### `systemPreferences.getAccentColor()` _Windows_
返回 `String` - 用户当前系统颜色偏好的16进制 RGBA 色值。
```js
const color = systemPreferences.getAccentColor() // ``aabbccdd``
const red = color.substr(0, 2) // `aa`
const green = color.substr(2, 2) // `bb`
const blue = color.substr(4, 2) // `cc`
const alpha = color.substr(6, 2) // `dd`
```

### `systemPreferences.getColor(color)` _Windows_

* `color` String - 可选值:
  * `3d-dark-shadow` - 3D 元素的暗部的色值。
  * `3d-face` - 3D 肤色，以及对话框背景。
  * `3d-highlight` - 高亮的3D元素。
  * `3d-light` - 3D 元素亮部。
  * `3d-shadow` - 3D 元素的阴影。
  * `active-border` - 活跃窗口边框。
  * `active-caption` - 活跃窗口标题。 具体来说是指开启渐变效果下活跃窗口中左侧色彩梯度。
  * `active-caption-gradient` - 活跃窗口标题栏中右侧颜色梯度。
  * `app-workspace` - 多文档(MDI)应用界面背景颜色。
  * `button-text` - 推送按钮的文本。
  * `caption-text` - 标题栏，尺寸框，滚动条尖头框上的文本。
  * `desktop` - 桌面背景颜色。
  * `disabled-text` - 灰色(禁止的)文本。
  * `highlight` - 元素的元素。
  * `highlight-text` - 选中的文本。
  * `hotlight` - 热链或者超链接的色值。
  * `inactive-border` - 不活跃窗口的边框。
  * `inactive-caption` - 不活跃窗口标题。具体来说是指开启渐变效果下不活跃窗口中左侧色彩梯度。
  * `inactive-caption-gradient` - 不活跃窗口标题栏中右侧色彩梯度。
  * `inactive-caption-text` - 不活跃的标题文本。
  * `info-background` - 工具栏背景。
  * `info-text` - 工具栏文本。
  * `menu` - 菜单背景。
  * `menu-highlight` - 当菜单作为平面菜单时高亮的菜单颜色。
  * `menubar` - 当菜单作为平面菜单时菜单栏背景。
  * `menu-text` - 菜单文本。
  * `scrollbar` - 滚动条灰色区域。
  * `window` - 窗口背景。
  * `window-frame` - 窗口框架。
  * `window-text` - 窗口内文本。

返回 `String` - 系统设置色值的16进制形式(`#ABCDEF`)。
详见 [Windows docs][windows-colors] 获取更多细节。

### `systemPreferences.isInvertedColorScheme()` _Windows_
返回  `Boolean` - `true` 代表的是如果正在使用的是反色方案(高对比度主题等), 否则为 `false`

[windows-colors]:https://msdn.microsoft.com/en-us/library/windows/desktop/ms724371(v=vs.85).aspx