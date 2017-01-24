# 本文介绍:webFrame类(自定义渲染页面)

> `web-frame` 模块可自定义渲染当前网页

进程: [渲染进程](../glossary.md#renderer-process)      

例如放大当前页至 200%.
```javascript
const {webFrame} = require('electron')

webFrame.setZoomFactor(2)
```

## 方法列表

`webFrame` 模块有如下方法:

### `webFrame.setZoomFactor(factor)`
* `factor` Number -缩放系数
设置页面的缩放系数。
注意:缩放系数是百分制的，如3.0=300％。

### `webFrame.getZoomFactor()`

返回 `Number` - 当前缩放系数

### `webFrame.setZoomLevel(level)`

* `level` Number -缩放级别

将缩放级别更改为指定级别。
原始大小为0，每个增量表示放大或缩小20％,默认限制为原始大小的300％至50％。

### `webFrame.getZoomLevel()`

返回 `Number` - 当前缩放级别。


### `webFrame.setVisualZoomLevelLimits(minimumLevel, maximumLevel)`

* `minimumLevel` Number
* `maximumLevel` Number

设置缩放级别的最大值和最小值.

### `webFrame.setLayoutZoomLevelLimits(minimumLevel, maximumLevel)`

* `minimumLevel` Number
* `maximumLevel` Number

设置基于布局(即非视觉)的缩放级别的最大值和最小值.

### `webFrame.setSpellCheckProvider(language, autoCorrectWord, provider)`

* `language` String
* `autoCorrectWord` Boolean
* `provider` Object
  * `spellCheck` Function - 返回 `Boolean`
    * `text` String

为输入框或文本域设置拼写检查的提供程序。
 `provider` 是具有 `spellCheck`方法的对象，该方法返回扫过的单词是否正确拼写。
 
使用[node-spellchecker] [spellchecker]作为提供者的示例：
```javascript
const {webFrame} = require('electron')
webFrame.setSpellCheckProvider('en-US', true, {
  spellCheck (text) {
    return !(require('spellchecker').isMisspelled(text))
  }
})
```

### `webFrame.registerURLSchemeAsSecure(scheme)`

* `scheme` String
将 `scheme` 注册为安全协议,而且安全协议不会引发 混合内容 警告。
例如， `https`和 `data`是安全的协议，它不能轻易的被黑客破坏。

### `webFrame.registerURLSchemeAsBypassingCSP(scheme)`

* `scheme` String

忽略当前页面的内容安全策略，资源全部从此 `scheme` 加载。

### `webFrame.registerURLSchemeAsPrivileged(scheme[, options])`

* `scheme` String
* `options` Object (可选)
  * `secure` Boolean - (可选) 默认为 `true`
  * `bypassCSP` Boolean - (可选) 默认为 `true`
  * `allowServiceWorkers` Boolean - (可选) 默认为 `true`
  * `supportFetchAPI` Boolean - (可选) 默认为 `true`
  * `corsEnabled` Boolean - (可选) 默认为 `true`

忽略内容安全策略,将 `scheme` 注册为安全协议且允许注册ServiceWorker和支持fetch API。
以上选项值如指定 `false`表示从注册中忽视该选项。

通过内容安全策略的情况下注册特权方案的示例：
```javascript
const {webFrame} = require('electron')
webFrame.registerURLSchemeAsPrivileged('foo', { bypassCSP: false })
```

### `webFrame.insertText(text)`

* `text` String

插入 `text` 到焦点元素。

### `webFrame.executeJavaScript(code[, userGesture, callback])`

* `code` String
* `userGesture` Boolean (可选) - 默认为 `false`.
* `callback` Function (可选) - script脚本执行后调用
  * `result` Any

在页面中评估(eval) `code` .
将 `userGesture` 设置为 `true`,可对去除 某些HTML API 只能通过 手势 进行调用 的限制,比如 `requestFullScreen`。

### `webFrame.getResourceUsage()`

返回 `Object`:

* `images` [MemoryUsageDetails](structures/memory-usage-details.md)
* `cssStyleSheets` [MemoryUsageDetails](structures/memory-usage-details.md)
* `xslStyleSheets` [MemoryUsageDetails](structures/memory-usage-details.md)
* `fonts` [MemoryUsageDetails](structures/memory-usage-details.md)
* `other` [MemoryUsageDetails](structures/memory-usage-details.md)

返回一个用来描述Blink内存的缓存信息的对象。

```javascript
const {webFrame} = require('electron')
console.log(webFrame.getResourceUsage())
```

将生成：
```javascript
{
  images: {
    count: 22,
    size: 2549,
    liveSize: 2542,
    decodedSize: 478,
    purgedSize: 0,
    purgeableSize: 0
  },
  cssStyleSheets: { /* 类同上行的 `images` */ },
  xslStyleSheets: { /* 类同上行的 `images` */ },
  fonts: { /* 类同上行的 `images` */ },
  other: { /* 类同上行的 `images` */ }
}
```

### `webFrame.clearCache()`

释放无用内存.

如果盲调用此方法,可能会使Electron较慢,因为它将不得不再次重新填充这些空的缓存!
建议在某些比较消耗内存的应用事件时才进行调用,比如加载了个很冗长复杂且需长时间停留使用的页面.

[spellchecker]: https://github.com/atom/node-spellchecker
