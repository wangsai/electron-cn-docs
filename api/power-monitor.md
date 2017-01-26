# 本文介绍:powerMonitor类(监视电源状态)

>电源状态更改时触发事件

进程: [主进程](../glossary.md#main-process)     

该模块在主进程`app` 模块的 `ready` 事件触发前使用.  
```javascript
const electron = require('electron')
const {app} = electron

app.on('ready', () => {
  electron.powerMonitor.on('suspend', () => {
    console.log('The system is going to sleep')
  })
})
```

## 事件列表

###事件：'suspend'
系统挂起时触发。

###事件：`resume`
系统恢复时触发。

###事件：'on-ac'_Windows_
当系统更改为交流电源时触发。

###事件：'on-battery'_Windows_
当系统更改为电池电源时触发。
