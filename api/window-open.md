# 本文介绍:`window.open` 函数详解

> 用于打开url或载入url至 `BrowserWindow` ,并对该窗口进行少量功能的控制.

当在界面中使用 `window.open` 来创建一个新的窗口时候，将会创建一个 `BrowserWindow` 的实例，并且将返回一个标识，这个界面通过标识来对这个新的窗口进行有限的控制.

这个标识对传统的web界面来说，通过它能对子窗口进行有限的功能性兼容控制.
想要完全的控制这个窗口，可以直接创建一个 `BrowserWindow` .

新创建的 `BrowserWindow` 默认为继承父窗口的属性参数，想重写属性的话可以在 `features` 中设置他们.

### `window.open(url[, frameName][, features])`
* `url` String
* `frameName` String (可选)
* `features` String (可选)
返回 [`BrowserWindowProxy`](browser-window-proxy.md)
创建一个新的window并且返回一个 `BrowserWindowProxy` 类的实例.

 `features` 遵循标准浏览器的格式，但是每个feature 应该作为 `BrowserWindow` 参数的一个字段.

### `window.opener.postMessage(message, targetOrigin)`

* `message` String
* `targetOrigin` String

通过指定位置或用 `*` 来代替没有明确位置来向父窗口发送信息.
