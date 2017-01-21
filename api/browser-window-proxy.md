##类：BrowserWindowProxy
>操纵子窗口
率属进程：[Renderer](../ glossary.md＃renderer-process)
当在界面中使用 `window.open` 来创建一个新的窗口时候，将会创建一个 `BrowserWindowProxy`  的实例，并且将返回一个标识，这个界面通过标识来对这个新的窗口进行有限的控制.

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
子窗口关闭后恢复设置为true的正常项。