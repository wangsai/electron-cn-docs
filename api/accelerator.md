# 本文介绍:快捷键字符串

使用[`globalShortcut`](global-shortcut.md)中的[`register`](global-shortcut.md#globalshortcutregisteraccelerator-callback)绑定快捷键.

多个则采用 `+` 连接,例如：
* `CommandOrControl+A`
* `CommandOrControl+Shift+Z`

例子:
```javascript
const {app, globalShortcut} = require('electron')

app.on('ready', () => {
  // Register a 'CommandOrControl+Y' shortcut listener.
  globalShortcut.register('CommandOrControl+Y', () => {
    // Do stuff when Y and either Command/Control is pressed.
  })
})
```
## 运行平台相关的提示

在 Linux 和 Windows 上， `Command`  键并不存在，因此我们通常用 `CommandOrControl` 来表示“在 macOS 下为 `Command` 键，但在
Linux 和 Windows 下为 `Control` 键。

`Super` 键是指 Linux 和 Windows 上的 `Windows` 键，但是在 macOS 下为 `Command` 键。

## 可用的功能按键

* `Command`（缩写为 `Cmd`）
* `Control`（缩写为 `Ctrl`）
* `CommandOrControl`（缩写为 `CmdOrCtrl`）
* `Alt`
* `Option`
* `AltGr`
* `Shift`
* `Super`

## 可用的普通按键

* `0` 到 `9`
* `A` 到 `Z`
* `F1` 到 `F24`
* 类似与 `~`、`!`、`@`、`#`、`$` 的标点符号。
* `Plus`
* `Space`
* `Backspace`
* `Delete`
* `Insert`
* `Return`（和 `Enter` 等同）
* `Up`、`Down`、`Left` 和 `Right`
* `Home` 和 `End`
* `PageUp` 和 `PageDown`
* `Escape`（缩写为 `Esc`）
* `VolumeUp` 、 `VolumeDown`  和 `VolumeMute`
* `MediaNextTrack` 、 `MediaPreviousTrack` 、 `MediaStop`  和   `MediaPlayPause`
* `PrintScreen`