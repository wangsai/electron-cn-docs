# 本文介绍:全局快捷键
> 应用于键盘无焦点时的键盘快捷键.

进程: [主进程](../glossary.md#main-process) 

`global-shortcut`模块可以便捷的为您设置(注册/注销)各种自定义操作的快捷键. 

 **注意:** 请在`ready`前注册快捷键,这种快捷键是全局性的. 

```javascript
const {app, globalShortcut} = require('electron')

app.on('ready', () => {
  // Register a 'CommandOrControl+X' shortcut listener.
  const ret = globalShortcut.register('CommandOrControl+X', () => {
    console.log('CommandOrControl+X is pressed')
  })

  if (!ret) {
    console.log('registration failed')
  }

  // 检查快捷方式是否已注册。
  console.log(globalShortcut.isRegistered('CommandOrControl+X'))
})

app.on('will-quit', () => {
//取消已注册的一个快捷键。
  globalShortcut.unregister('CommandOrControl+X')

//注销应用注册的所有快捷键
  globalShortcut.unregisterAll()
})
```

## 事件方法

### `globalShortcut.register(accelerator, callback)`
* `accelerator` [Accelerator](accelerator.md)
* `callback` Function
注册 `accelerator` 快捷键. 当用户按下注册的快捷键时将会调用 `callback` 函数.
如果快捷键已经被其他应用程序注册了，这个调用将静默失败。
此行为是受限于操作系统，因为它不希望应用程序为全局快捷键斗争。

### `globalShortcut.isRegistered(accelerator)`
* `accelerator` [Accelerator](accelerator.md)
返回 `Boolean` -  检查 `accelerator`是否已注册.
查询 `accelerator` 快捷键是否已经被注册过了,将会返回 `true`(已被注册) 或 `false`(未注册).

### `globalShortcut.unregister(accelerator)`
* `accelerator` [Accelerator](accelerator.md)
注销全局快捷键 `accelerator`.

### `globalShortcut.unregisterAll()`
注销本应用注册的所有全局快捷键.