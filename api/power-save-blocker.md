# 本文介绍:powerSaveBlocker类(阻止系统进入低功耗（睡眠）模式)

> 有时候,我们需要应用在前后台无间断工作,并不需要系统睡眠.

进程: [主进程](../glossary.md#main-process)     

```javascript
const {powerSaveBlocker} = require('electron')

const id = powerSaveBlocker.start('prevent-display-sleep')
console.log(powerSaveBlocker.isStarted(id))

powerSaveBlocker.stop(id)
```

## 方法
`powerSaveBlocker` 模块有如下方法:

###`powerSaveBlocker.start（type）`

* `type` String  - 节电模块类型。
  * `prevent-app-suspension`  -  阻止应用挂起.保持系统活跃，但允许屏幕关闭。示例用例：下载文件或播放音频。
  * `prevent-display-sleep`  - 防止显示器进入睡眠状态。保持系统和屏幕活动。示例用例：播放视频。

返回 `Integer`  - 开始阻止系统进入睡眠模式.返回一个整数，这个整数标识了保持活跃的blocker.

 **注意：**  `prevent-display-sleep`总是优先生效于 `prevent-app-suspension`
例如，一个API调用A请求 `prevent-app-suspension`，另一个调用B请求 `prevent-display-sleep`。 `prevent-display-sleep`将一直工作，直到B停止调用。之后，使用 `prevent-app-suspension`才起效.

### `powerSaveBlocker.stop(id)`

* `id` Integer - 通过 `powerSaveBlocker.start` 返回的保持活跃的 blocker id.

让指定的blocker 停止活跃.

### `powerSaveBlocker.isStarted(id)`

* `id` Integer - 通过 `powerSaveBlocker.start` 返回的保持活跃的 blocker id.

返回`Boolean`  -`powerSaveBlocker`是否已经开始。