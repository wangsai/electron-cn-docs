# 本文介绍:环境变量
> 不必更改代码的控制应用程序配置和行为。
某些Electron行为由环境变量控制，因为它们比命令行和应用程序代码都更早地初始化。

POSIX shell示例：
```bash
$ export ELECTRON_ENABLE_LOGGING=true
$ electron
```

Windows控制台示例：
```powershell
> set ELECTRON_ENABLE_LOGGING=true
> electron
```

##生产变量
下面的环境变量主要用于应用运行时。

### `GOOGLE_API_KEY`
Electron 的硬编码API密钥(包括提出请求谷歌的地理编码Web服务)。
因为这个API密钥包含在Electron的每一个版本，它往往超过其使用配额。
要解决这个问题，您可以提供自己的谷歌API密钥至环境中。
在你的主进程文件引入下面的代码，地址解析请求之前可打开任何浏览器窗口：
```javascript
process.env.GOOGLE_API_KEY = 'YOUR_KEY_HERE'
```
有关如何获取谷歌API密钥的说明，请访问：[此页]（https://www.chromium.org/developers/how-tos/api-keys）。通过默认情况下，新生成的谷歌API密钥可能不允许进行地址解析请求。要启用地址解析请求，请访问：[此页]（https://console.developers.google.com/apis/api/geolocation/overview）。

### `ELECTRON_NO_ASAR`
禁用ASAR支持。这个变量只有在设置 `ELECTRON_RUN_AS_NODE`派生的子进程并催生子进程的支持。

### `ELECTRON_RUN_AS_NODE`
普通的Node.js启动进程

### `ELECTRON_NO_ATTACH_CONSOLE` _Windows_
不要附加到当前控制台会话。

### `ELECTRON_FORCE_WINDOW_MENU_BAR` _Linux_
不要使用Linux上的全局菜单栏。

##发展变量
下面的环境变量主要用于开发和调试的目的。

### `ELECTRON_ENABLE_LOGGING`
打印Chrome的内部记录到控制台。

### `ELECTRON_LOG_ASAR_READS`
当Electron从ASAR文件读取，记录所读取的偏移量和文件路径到系统 `tmpdir`。生成的文件可被提供给该ASAR模块来优化文件排序。

### `ELECTRON_ENABLE_STACK_DUMPING`
Electron崩溃时,打印堆栈跟踪到控制台。
如果 `crashReporter`启动,这个环境变量将无法工作。

### `ELECTRON_DEFAULT_ERROR_MODE` _Windows_
Electron崩溃时,展示崩溃对话框
如果 `crashReporter`启动,这个环境变量将无法工作。