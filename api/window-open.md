# 本文介绍:`window.open` (用于新窗口打开的函数)

> 用于打开url或载入url至 `BrowserWindow` ,并对该窗口进行少量功能的控制.

使用 `window.open` 创建一个新窗口时，会自动创建一个 `BrowserWindowProxy`  的实例将返回一个标识，可通过标识对这个新窗口进行少量功能的控制.
不过希望完全的控制这个窗口，请直接创建一个 `BrowserWindow` .

新创建的 `BrowserWindow` 默认为继承父窗口的属性参数，想重写属性的话可以在 `features` 中设置它们.

### `window.open(url[, frameName][, features])`
* `url` String
* `frameName` String (可选)
* `features` String (可选)

返回一个[`BrowserWindowProxy`](browser-window-proxy.md)实例的新窗口.

 `features` 遵循标准浏览器的格式，但是每个feature 应该是 `BrowserWindow` 参数的一个字段.

### `window.opener.postMessage(message, targetOrigin)`

* `message` String
* `targetOrigin` String

通过指定位置或用 `*` 来代替没有明确位置来向父窗口发送信息.
