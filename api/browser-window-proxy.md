# 本文介绍:BrowserWindowProxy类(子窗口的控制与应用)

>操纵子窗口

进程：[渲染进程](../ glossary.md＃renderer-process)        

使用 `window.open` 创建一个新窗口时，会自动创建一个 `BrowserWindowProxy`  的实例将返回一个标识，可通过标识对这个新窗口进行少量功能的控制.

###实例方法

`BrowserWindowProxy`对象具有以下实例方法：

####`win.blur()`
从子窗口中去焦。

####`win.close()`
强制关闭子窗口，而不调用其卸载事件(unload event)。

####`win.eval(code)`
* `code`String
eval子窗口中的代码。

####`win.focus()`
聚焦子窗口(将窗口置顶)。

####`win.print()`
调用子窗口上的打印对话框。

####`win.postMessage(message，targetOrigin)`
* `message` String
* `targetOrigin` String
通过指定位置或用 `*` 来代替没有明确位置来向父窗口发送信息.
除了这些方法，子窗口还可以无特性和使用单一方法来实现  `window.opener` 对象.

###实例属性
`BrowserWindowProxy`对象具有以下实例属性：

####`win.closed`
子窗口关闭后正常项恢复设置为true。