# 进程

> process 对象扩展。

进程: [主进程](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process)
Electron 的 `process` 对象是[Node.js `process` 对象](https://nodejs.org/api/process.html)    的扩展。


## 事件

### 事件: 'loaded'
Electron加载其内部预置脚本并开始载入网页或主脚本时触发。

当Node被完全关闭时，预置脚本可以使用 这个事件 把 原本已删除的Node全局符号 重新添加回 全局范围：
```javascript
// 预置脚本
const _setImmediate = setImmediate
const _clearImmediate = clearImmediate
process.once('loaded', () => {
  global.setImmediate = _setImmediate
  global.clearImmediate = _clearImmediate
})
```

## 属性

### `process.noAsar`
设置为 `true` ,可使 `asar` 在node的内置模块中失效。

### `process.type`
当前 `process` 的类型，值为 ``browser`` (即主进程) 或 ``renderer``(即渲染进程)。

### `process.versions.electron`
Electron的版本号。

### `process.versions.chrome`
Chrome的版本号。

### `process.resourcesPath`
资源文件夹的路径。

### `process.mas`
在 Mac App Store 的构建中，该属性为 `true`, 其他平台的构建均为 `undefined`。

### `process.windowsStore`
如果 app 是运行在 Windows Store app (appx) 中，该属性为 `true`, 其他情况均为 `undefined`。

### `process.defaultApp`
当 app 在启动时，被作为参数传递给默认应用程序，在主进程中该属性为 `true`, 其他情况均为 `undefined`。

## 方法
`process` 对象有如下方法：

### `process.crash()`
使当前进程的主线程崩溃。

### `process.hang()`
使当前进程的主线程挂起。

### `process.setFdLimit(maxDescriptors)` _macOS_ _Linux_
* `maxDescriptors` Integer
将文件描述符软限制设置为`maxDescriptors`或OS硬限制，以当前进程的较低者为准。

### `process.getProcessMemoryInfo()`
* `workingSetSize` Integer  - 当前固定到实际物理RAM的内存量。
* `peakWorkingSetSize` Integer  - 已经固定到实际物理RAM的最大内存量。
* `privateBytes` Integer  - 其他进程不共享的内存量，例如JS堆或HTML内容。
* `sharedBytes` Integer  - 进程之间共享的内存量，通常是Electron代码本身消耗的内存
返回有关当前进程的内存使用统计信息的对象。请注意，所有统计信息都以千字节(KB)为单位。

### `process.getSystemMemoryInfo()`
返回 `Object`：
* `total` Integer  - 系统可用的物理内存总量。
* `free` Integer  - 应用程序或磁盘缓存未使用的内存总量。
* `swapTotal` Integer  - 系统可用的交换内存总量。 _Windows_ _Linux_
* `swapFree` Integer  - 系统可用的交换内存的自由量。 _Windows_ _Linux_
返回整个系统的内存使用统计信息的对象。。请注意，所有统计信息都以千字节(KB)为单位。