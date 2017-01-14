## 前言:

本文采用官方electron翻译并提供相关教程与资源,较之官译本更加精简和本土化.

## 初学者入门指导(Win为例):

* 1.设置环境变量:
```
方法:计算机右键-属性-高级系统设置-环境变量-新建
变量名:ELECTRON_MIRROR,变量值:http://npm.taobao.org/mirrors/electron/
```
* 2.[Node.js安装](https://nodejs.org/en/download/)
* 3.[git安装](https://git-for-windows.github.io/)
* 4.[Electron安装](tutorial/quick-start.md)

## 常见:

提交issue前,请详读以下常见问题:
* [Electron 常见问题](faq.md)

## 特别注意:

* 与Jquery等第三方js插件的兼容相关
```
<script src="jquery.min.js"></script>
<script>if (typeof module === 'object') {window.jQuery = window.$ = module.exports;};</script>
```

## 指南:

* [术语词汇](glossary.md)
* [支持平台](tutorial/supported-platforms.md)
* [安全](tutorial/security.md)
* [版本](tutorial/electron-versioning.md)
* [应用打包](tutorial/application-packaging.md)
* [应用分发](tutorial/application-distribution.md)
* [Mac App Store 提交指南](tutorial/mac-app-store-submission-guide.md)
* [Windows Store 提交指南](tutorial/windows-store-guide.md)
* [使用 Node 原生模块](tutorial/using-native-node-modules.md)
* [主进程调试](tutorial/debugging-main-process.md)
* [使用 Selenium 和 WebDriver](tutorial/using-selenium-and-webdriver.md)
* [使用开发人员工具扩展](tutorial/devtools-extension.md)
* [使用 Pepper Flash 插件](tutorial/using-pepper-flash-plugin.md)
* [使用 Widevine CDM 插件](tutorial/using-widevine-cdm-plugin.md)
* [通过自动化持续集成系统（CI）进行测试 (Travis, Jenkins)](tutorial/testing-on-headless-ci.md)
* [离屏渲染](tutorial/offscreen-rendering.md)

## 向导:

* [快速启动](tutorial/quick-start.md)
* [桌面环境集成](tutorial/desktop-environment-integration.md)
* [线上/下事件侦测](tutorial/online-offline-events.md)
* [交互式解释器](tutorial/repl.md)

## API参考:

* [简介](api/synopsis.md)
* [进程对象](api/process.md)
* [支持的 Chrome 命令行开关](api/chrome-command-line-switches.md)
* [环境变量对象](api/environment-variables.md)

### 自定义DOM元素:

* [`File` 对象](api/file-object.md)
* [`<webview>`  标签](api/webview-tag.md)
* [`window.open` 函数](api/window-open.md)

### 主进程内可用的模块:

* [app](api/app.md)
* [autoUpdater](api/auto-updater.md)
* [BrowserWindow](api/browser-window.md)
* [contentTracing](api/content-tracing.md)
* [dialog](api/dialog.md)
* [globalShortcut](api/global-shortcut.md)
* [ipcMain](api/ipc-main.md)
* [Menu](api/menu.md)
* [MenuItem](api/menu-item.md)
* [net](api/net.md)
* [powerMonitor](api/power-monitor.md)
* [powerSaveBlocker](api/power-save-blocker.md)
* [protocol](api/protocol.md)
* [session](api/session.md)
* [systemPreferences](api/system-preferences.md)
* [Tray](api/tray.md)
* [webContents](api/web-contents.md)

### 渲染进程（网页）内可用的模块:

* [desktopCapturer](api/desktop-capturer.md)
* [ipcRenderer](api/ipc-renderer.md)
* [remote](api/remote.md)
* [webFrame](api/web-frame.md)

### 两种进程中都可用的模块开发:

* [clipboard](api/clipboard.md)
* [crashReporter](api/crash-reporter.md)
* [nativeImage](api/native-image.md)
* [screen](api/screen.md)
* [shell](api/shell.md)

## 开发:

* [代码规范](development/coding-style.md)
* [在 C++ 中使用 clang格式化工具](development/clang-format.md)
* [源码目录结构](development/source-code-directory-structure.md)
* [与 NW.js（原 node-webkit）在技术上的差异](development/atom-shell-vs-node-webkit.md)
* [构建系统概览](development/build-system-overview.md)
* [构建步骤 (macOS)](development/build-instructions-osx.md)
* [构建步骤 (Windows)](development/build-instructions-windows.md)
* [构建步骤 (Linux)](development/build-instructions-linux.md)
* [调试步骤 (macOS)](development/debugging-instructions-macos.md)
* [调试步骤 (Windows)](development/debug-instructions-windows.md)
* [在调试中使用 Symbol Server](development/setting-up-symbol-server.md)
* [文档风格规范](styleguide.md)
