# 本文介绍:应用窗口的创建与控制

> 窗口的创建与控制

```javascript
// In the main process.
const {BrowserWindow} = require('electron')

// Or use `remote` from the renderer process.
// const {BrowserWindow} = require('electron').remote

let win = new BrowserWindow({width: 800, height: 600})
win.on('closed', () => {
  win = null
})

//加载远程URL
win.loadURL('https://github.com')

//或加载本地HTML文件
win.loadURL(`file://${__dirname}/app/index.html`)
```

##无框窗口
使用[Frameless Window](frameless-window.md) 即可创建仅内容本身或任意形状的透明窗口.

##显示前过渡办法
如果像浏览器一样边加载页面边显示界面,会令人觉得很丑,这里提供了一个办法:背景色一致与加载前事件.

### 使用 `ready-to-show` 事件
 加载页面时,先用 `ready-to-show`事件进程完成首次绘制,在该事件后显示窗口. 
 
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({show: false})
win.once('ready-to-show', () => {
  win.show()
})
```

这是事件通常发生在 `did-finish-load` 事件之后,但是页面有许多远程资源,它可能会在 `did-finish-load` 之前触发事件。

###设置背景`backgroundColor`
如果程序较复杂, `ready-to-show`事件可能好久才完成,窗口背景色 `backgroundColor` 应当网页的背景色一致,避免突兀的感觉。

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({backgroundColor：'＃2e2c29'})
win.loadURL('https://github.com')
```

###父子窗口

通过使用`parent`选项,可以创建子窗口：

```javascript
const {BrowserWindow} = require('electron')

let top = new BrowserWindow()
let child = new BrowserWindow({parent：top})
child.show()
top.show()
```
`child` 窗口将总是显示在 `top` 窗口的顶部。

###模态窗口

模态窗口必须设置`parent`和`modal`选项,它可以与父窗口无关联：

```javascript
const {BrowserWindow} = require('electron')

let child = new BrowserWindow({parent：top,modal：true,show：false})
child.loadURL('https://github.com')
child.once('ready-to-show',()=> {
  child.show()
})
```

###平台差异
* 在Windows上,不支持动态更改父窗口。
* 在MacOS上,模态窗口附属于父窗口。
* 在MacOS上,当父窗口移动时子窗口将保持与父窗口的相对位置,而在Windows和Linux子窗口则不会移动。
* 在Linux上,模态窗口的类型将更改为`对话框`,许多桌面环境不支持隐藏模态窗口。

##类：BrowserWindow

>创建和控制浏览器窗口。

过程：[Main](../ glossary.md＃main-process)

###`new BrowserWindow([options])`

* `options`Object(可选)
* `width` Integer(可选) - 窗口的宽度(以像素为单位)。默认值为`800`。
* `height` Integer(可选) - 窗口的高度(以像素为单位)。默认值为`600`。
* `x` Integer(可选) - 窗口的左偏移距屏幕。默认是将窗口居中。
* `y`Integer(可选) - 窗口的顶部偏移距屏幕。默认是将窗口居中。
* `useContentSize` Boolean(可选) - 是否采用内容宽高为当前窗口的宽高 。
    默认值为   `false`  。
* `center` Boolean(可选) - 在屏幕中心显示窗口。
* `minWidth` Integer(可选) - 窗口的最小宽度。
    默认值为0。
* `minHeight` Integer(可选) - 窗口的最小高度。
    默认值为0。
* `maxWidth` Integer(可选) - 窗口的最大宽度。
    默认是没有限制。
* `maxHeight` Integer(可选) - 窗口的最大高度。
    默认是没有限制。
* `resizable` Boolean(可选) - 窗口是否可调整大小。
    默认值为`true`。
* `movable` Boolean(可选) - 窗口是否可移动。这没有实现
    在Linux上。默认值为`true`。
* `minimizable` Boolean(可选) - 窗口是否可最小化。这不是
    在Linux上实现。默认值为`true`。
* `maximizable` Boolean(可选) - 窗口是否可以最大化。这不是
    在Linux上实现。默认值为`true`。
* `closable` Boolean(可选) - 窗口是否可关闭。这没有实现
    在Linux上。默认值为`true`。
* `focusable` Boolean(可选) - 窗口是否可以聚焦。
    默认值为`true'。
    在Windows设置 `focusable：false`也意味着设置`skipTaskbar：true`。
    在Linux设置 `focusable：false`使窗口停止与wm交互且窗口始终置顶。
* `alwaysOnTop` Boolean(可选) - 窗口是否置顶。
    默认值为  `false` 。
* `fullscreen` Boolean(可选) - 是否全屏。`false'全屏按钮将被隐藏或禁用
    在macOS上。默认值为  `false` 。
* `fullscreenable` Boolean(可选) - 窗口是否可全屏。
    默认值为`true`。
* `skipTaskbar` Boolean(可选) - 是否在任务栏中显示窗口。
    默认值为  `false`。
* `kiosk` Boolean(可选) -  kiosk模式。
    默认值为  `false` 。
* `title` String(可选) - 默认窗口标题。
    默认为`Electron`。
* `icon`([NativeImage](native-image.md)| String)(可选) - 窗口图标。
    在Windows上推荐使用`ICO`图标来获得最佳的视觉效果。
   默认使用可执行文件的图标。
* `show` Boolean(可选) - 创建时是否显示窗口。
    默认值为`true'。
* `frame` Boolean(可选) - 指定  `false` 则创建一个[无框窗口](frameless-window.md) 。
    默认值为`true`。
* `parent` BrowserWindow(可选) - 指定父窗口。
    默认值为null。
* `modal` Boolean(可选) -是否模态窗口,子窗口时使用。
    默认值为  `false` 。
* `acceptFirstMouse` Boolean(可选) -   是否允许单击web view来激活窗口 。
    默认值为 `false`。
* `disableAutoHideCursor` Boolean(可选) - 是否在键入时隐藏光标。
    默认值为  `false` 。
* `autoHideMenuBar` Boolean(可选) - 自动隐藏菜单栏,除非`Alt`键。
    默认值为  `false` 。
* `enableLargerThanScreen` Boolean(可选) - 是否允许改变窗口大小大于屏幕。
    默认值为  `false` 。
* `backgroundColor` String(可选) - 窗口的背景颜色如`＃66CD00`或`＃FFF`(支持透明度)。
    默认值为 `＃FFF`(white)。
* `hasShadow` Boolean(可选) -窗口是否有阴影。仅macOS中有效。
    默认值为`true`。
* `darkTheme` Boolean(可选) - 强制使用深色dark主题的窗口,只适用于一些GTK + 3桌面环境。
    默认值为  `false` 。
* `transparent` Boolean(可选) - 窗口是否透明[transparent](frameless-window.md)。
    默认值为  `false` 。
* `type` String(可选) - 窗口的类型,可选值与效果根据不同系统展示不同效果。
 可选值有：
 * 在Linux上, 可选值 `desktop` , `dock` , `toolbar` , `splash`,
 * 在macOS上,可选值 `desktop` , `textured`.
  * `textured`类型添加金属渐变外观(`NSTexturedBackgroundWindowMask`)。
  * `desktop`类型将窗口放置在桌面背景窗口级别
    ( `kCGDesktopWindowLevel  -  1`)。注意桌面窗口不会收到
    焦点,键盘或鼠标事件,但您可以使用 `globalShortcut` 接收输入。
 * 在Windows上,可选值 `toolbar`。

* `titleBarStyle` String(可选) - 窗口标题栏的样式。
    默认值为`default`。
    可选值：
  * `default`以及无值, 显示在 Mac 标题栏上为不透明的标准灰色.
  * `hidden`隐藏标题栏,内容充满整个窗口, 然后它依然在左上角,仍然受标准窗口控制.
  * `hidden-inset`主体隐藏,显示小的控制按钮在窗口边缘.
  
* `thickFrame` Boolean(可选) - 对无框架窗口使用`WS_THICKFRAME'风格。
    设置为  `false` 将删除窗口阴影和窗口动画。
    默认值为`true`。
* `vibrancy` String(可选) - 窗口是否使用vibrancy动态效果,仅macOS中有效。
    可选值有: `appearance-based` , `light` , `dark` , `titlebar` , `selection`,
    `menu` , `popover` , `sidebar` , `medium-light`or `ultra-dark`.
* `zoomToPageWidth` Boolean(可选) - 单击工具栏上的绿色信号灯按钮或单击 窗口>缩放菜单项时的行为,仅macOS中有效。
    如果`true`,窗口将增长到首选放大时网页的宽度, `false` 会使它放大到屏幕宽度。
    `maximize()` Boolean(可选) - 是否最大化。
    默认值为  `false` 。
    
* `webPreferences` Object(可选) - 网页功能的设置,下方是它的对象属性:
  * `devTools` Boolean(可选) - 是否启用DevTools。
    如果它设置为  `false` ,不能使用`BrowserWindow.webContents.openDevTools()`来打开DevTools。
    默认值为`true`。
  * `nodeIntegration` Boolean(可选) - 是否完整支持node。
    默认值为`true`。
  * `preload` String(可选) - 预载脚本,其它脚本运行之前预先加载指定脚本. 这个脚本将一直可以使用 node APIs 无论 node integration 是否开启. 脚本路径为绝对路径.
  当 node integration 关闭, 预加载的脚本将从全局范围重新引入node的全局引用标志. [这里是例子](process.md＃event-loaded)。
  * `session`[Session](session.md#class-session) - 设置界面session. 而不是直接忽略session对象 , 也可用 `partition`来代替, 它接受一个 partition 字符串. 当同时使用 `session`和 `partition` , `session`优先级更高.
  默认使用默认 session.
  * `partition`String - 通过session的partition字符串来设置界面session. 如果 `partition`以 `persist:`开头, 这个界面将会为所有界面使用相同的 `partition`. 如果没有 `persist:`前缀, 界面使用历史session. 通过分享同一个 `partition` , 所有界面使用相同的session. 默认使用默认 session.
  * `zoomFactor` Number(可选) - 页面的默认缩放系数, `3.0`表示 `300％`。
      默认值为`1.0`。
  * `javascript`Boolean - 是否启用javascript支持. 
  默认为`true`.
  * `webSecurity`Boolean(可选) -  当设置为 `false` , 它将禁用同源策略 (通常用来测试网站), 并且如果有2个非用户设置的参数,就设置
  `allowDisplayingInsecureContent`和 `allowRunningInsecureContent`的值为 `true`. 
  默认为 `true`.
  * `allowDisplayingInsecureContent`Boolean -允许一个使用 https的界面来展示由 http URLs 传过来的资源. 
  默认`false`.
  * `allowRunningInsecureContent`Boolean - Boolean -允许一个使用 https的界面来渲染由 http URLs 提交的html,css,javascript. 
  默认为 `false`.
  * `images` Boolean(可选) - 启用图像支持。
  默认值为`true`。
  * `textAreasAreResizable` Boolean(可选) - 使TextArea元素可调整大小。
  默认值为`true`。
  * `webgl` Boolean(可选) - 启用WebGL支持。
  默认值为`true`。
  * `webaudio` Boolean(可选) - 启用WebAudio支持。
  默认值为`true`。
  * `plugins` Boolean(可选) - 是否应启用插件。
  默认值为  `false` 。
  * `experimentalFeatures` Boolean(可选) - 启用Chromium的实验功能。
  默认值为  `false` 。
  * `experimentalCanvasFeatures` Boolean(可选) - 启用Chromium的实验画布功能。
  默认值为  `false` 。
  * `scrollBounce` Boolean(可选) - 启用弹力动画(橡皮筋)效果,macOS中有效.
  默认值为  `false` 。
  * `blinkFeatures` String(可选) -  以 `, `分隔的特性列表, 如
      `CSSVariables,KeyboardEventKey`。在 [setFeatureEnabledFromString][blink-feature-string]文件中查看被支持的所有特性。
  * `disableBlinkFeatures` String(可选) -  以 `, `分隔的特性列表, 如
      `CSSVariables,KeyboardEventKey`。在 [RuntimeEnabledFeatures.in] [blink-feature-string]文件中查看被支持的所有特性。
  * `defaultFontFamily` Object(可选) - 设置font-family的默认字体。
    * `standard` String(可选) - 默认为 'Times New Roman`。
    * `serif` String(可选) - 默认为'Times New Roman`。
    * `sansSerif` String(可选) - 默认为`Arial`。
    * `monospace` String(可选) - 默认为`Courier New`。
    * `cursive` String(可选) - 默认为`Script`。
    * `fantasy` String(可选) - 默认为`Impact`。
  * `defaultFontSize` Integer(可选) - 默认为`16`。
  * `defaultMonospaceFontSize` Integer(可选) - 默认为`13`。
  * `minimumFontSize` Integer(可选) - 默认为`0`。
  * `defaultEncoding` String(可选) - 默认为 `ISO-8859-1`。
  * `backgroundThrottling` Boolean(可选) - 页面变成背景时是否限制动画和计时器。
      默认为 `true`。
  * `offscreen` Boolean(可选) - 是否绘制和渲染可视区域外的窗口。[更多细节](../ tutorial / offscreen-rendering.md)
    默认为  `false` 。
  * `sandbox` Boolean(可选) - 是否启用Chromium操作系统级沙盒。


###实例事件

使用`new BrowserWindow`创建的对象会触发以下事件：

**注意:** 一些事件只能在特定os环境中触发,已经尽可能地标出.

####事件：'page-title-updated'
返回：
* `event` Event
* `title` String
当文档改变标题时触发,使用 `event.preventDefault()` 可以阻止原窗口的标题改变.

####事件：'close'
返回：
* `event` Event

在窗口要关闭的时候触发. 它在DOM的 `beforeunload` and `unload` 事件之前触发.使用 `event.preventDefault()` 可以取消这个操作
通常你想通过 `beforeunload` 处理器来决定是否关闭窗口,但是它也会在窗口重载的时候被触发. 在 Electron 中,返回一个空的字符串或 `false` 可以取消关闭.例如:

```javascript
window.onbeforeunload =(e)=> {
  console.log('I do not want to be closed')

  //与通常的浏览器不同,一个消息框会提示给用户,返回
  //非空值将默认取消关闭。
  //建议使用对话框API让用户确认关闭应用程序。
  e.returnValue = false
}}
```

####事件：'closed'
当窗口已经关闭的时候触发.当你接收到这个事件的时候,你应当删除对已经关闭的窗口的引用对象和避免再次使用它.

####事件：'unresponsive'
当网页变得无响应时触发。

####事件：`responsive`
当无响应的网页再次变得响应时触发。

####事件：'blur'
窗口失去焦点时触发。

####事件：'focus'
当窗口获得焦点时触发。

####事件：'show'
在显示窗口时触发。

####事件：'hide'
窗口隐藏时触发。

####事件：'ready-to-show'
网页即将呈现时触发

####事件：`maximize`
窗口最大化时触发。

####事件：'unmaximize'
当窗口从最大化状态退出时触发。

####事件：'minimize'
窗口最小化时触发。

####事件：'restore'
当窗口从最小化状态恢复时触发。

####事件：'resize'
在调整窗口大小时触发。

####事件：'move'
窗口移动到新位置时触发。
 **注意:** 在macOS上,此事件只是`moved`的别名。

####事件：'moved'_macOS_
当窗口移动到新位置时触发一次。

####事件：'enter-full-screen'
当窗口进入全屏状态时触发。

####事件：'leave-full-screen'
窗口离开全屏状态时触发。

####事件：'enter-html-full-screen'
当窗口进入由HTML API触发的全屏状态时触发。

####事件：'leave-html-full-screen'
在窗口离开由HTML API触发的全屏状态时触发。

####事件：'app-command'_Windows_
返回：

* `event` Event
* `command` String

在请求一个[App Command](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx)的时候触发.
典型的是键盘媒体或浏览器命令, Windows上的 `Back` 按钮用作鼠标也会触发.
命令是小写的,下划线用连字符替换,
`APPCOMMAND_`前缀被删除。
例如`APPCOMMAND_BROWSER_BACKWARD`被作为`browser-backward`触发。
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.on('app-command',(e,cmd)=> {
  //当用户点击鼠标返回按钮时返回窗口
  if(cmd ==='browser-backward'&& win.webContents.canGoBack()){
    win.webContents.goBack()
  }}
})
```

####事件：'scroll-touch-begin'_macOS_
在滚轮事件阶段开始时触发。

####事件：'scroll-touch-end'_macOS_
在滚轮事件阶段结束时触发。

####事件：'scroll-touch-edge'_macOS_
在滚轮事件阶段到达元素边缘时触发。

####事件：'swipe'_macOS_
返回：
* `event` Event
* `direction` String
三指拖移时触发。可能的方向是`up`,`right`,`down`,`left`。

##静态方法

`BrowserWindow`类有以下静态方法：

####`BrowserWindow.getAllWindows()`
返回所有打开的浏览器窗口的数组。

####`BrowserWindow.getFocusedWindow()`
返回应用当前获得焦点的窗口,如果没有就返回 `null`.


### `BrowserWindow.fromWebContents(webContents)`
* `webContents` [WebContents](web-contents.md)
根据 `webContents` 查找窗口.


####`BrowserWindow.fromId(id)`
* `id`Integer
根据 id 查找窗口.

####`BrowserWindow.addDevToolsExtension(path)`
* `path` String
添加位于 `path` 的开发者工具栏扩展,并且返回扩展项的名字.
这个扩展会被添加到历史,所以只需要使用这个API一次,这个api不可用作编程使用.
如果扩展程序的清单丢失或不完整,该方法也不会返回。
**注意：**这个API不能在`app`模块的`ready`事件之前调用。

####`BrowserWindow.removeDevToolsExtension(name)`
* `name` String
删除开发者工具栏名为 `name` 的扩展.
**注意：**这个API不能在`app`模块的`ready`事件之前调用。

####`BrowserWindow.getDevToolsExtensions()`

返回`Object` - 键是扩展名,每个值都是一个包含`name`和`version`属性的对象。
要检查是否安装了开发者工具栏扩展,您可以运行以下命令：
```javascript
const {BrowserWindow} = require('electron')
let installed = BrowserWindow.getDevToolsExtensions()。hasOwnProperty('devtron')
console.log(已安装)
```
**注意：**这个API不能在`app`模块的`ready`事件之前调用。

##实例属性
使用`new BrowserWindow`创建的对象具有以下属性：

```javascript
const {BrowserWindow} = require('electron')
//在这个例子中,win是我们的实例
let win = new BrowserWindow({width：800,height：600})
win.loadURL('https://github.com')
```

####`win.webContents`

这个窗口的 `WebContents` 对象,所有与界面相关的事件和方法都通过它完成的.
查看 [`webContents` documentation](web-contents.md) 的方法和事件.

####`win.id`
窗口的唯一ID。

##实例方法
使用 `new BrowserWindow` 创建的对象有以下实例方法：
 **注意:**  一些方法只能在特定os环境中调用,已经尽可能地标出.
 
####`win.destroy()`
强制关闭窗口, `unload` and `beforeunload` 不会触发,并且 `close` 也不会触发, 但是它保证了 `closed` 触发.

####`win.close()`
尝试关闭窗口,这与用户点击关闭按钮的效果一样. 虽然网页可能会取消关闭,查看 [close event](#event-close).

####`win.focus()`
窗口获得焦点。

####`win.blur()`
窗口中删除焦点。

####`win.isFocused()`
返回`Boolean` - 窗口是否聚焦。

####`win.isDestroyed()`
返回`Boolean` - 窗口是否被销毁。

####`win.show()`
展示并且使窗口获得焦点.

####`win.showInactive()`
展示窗口但是不获得焦点.

####`win.hide()`
隐藏窗口。

####`win.isVisible()`
返回`Boolean` - 窗口是否对用户可见。

####`win.isModal()`
返回`Boolean` - 当前窗口是否为模态窗口。

####`win.maximize()`
窗口最大化.

####`win.unmaximize()`
取消窗口最大化.

####`win.isMaximized()`
返回`Boolean` - 窗口是否最大化。

####`win.minimize()`
窗口最小化. 在一些os中,它将在dock中显示.

####`win.restore()`
将窗口从最小化状态恢复到之前的状态。

####`win.isMinimized()`
返回`Boolean` - 窗口是否最小化。

####`win.setFullScreen(flag)`
* `flag` Boolean
是否允许窗口全屏。

### `win.isFullScreen()`
返回 Boolean, 窗口当前是否全屏化.

### `win.setAspectRatio(aspectRatio[, extraSize])` _macOS_
* `aspectRatio` 维持部分视图内容窗口的高宽比值.
* `extraSize` Object (可选) - 维持高宽比值时不包含的额外size.
  * `width` Integer
  * `height` Integer
由一个窗口来维持高宽比值. `extraSize` 允许开发者使用它,它的单位为像素,不包含在 `aspectRatio` 中.这个 API 可用来区分窗口的size和内容的size .
想象一个普通可控的HD video 播放器窗口. 假如左边缘有15px,右边缘有25px,在播放器下面有50px.为了在播放器内保持一个 16:9 的高宽比例,我们可以调用这个api传入参数16/9 和
[ 40, 50 ].第二个参数不管网页中的额外的宽度和高度在什么位置,只要它们存在就行.只需要把网页中的所有额外的高度和宽度加起来就行.

#### `win.closeFilePreview()` _macOS_
关闭当前打开的 快速查看 面板。

### `win.setBounds(options[, animate])`
* `options` Object
  * `x` Integer
  * `y` Integer
  * `width` Integer
  * `height` Integer
* `animate` Boolean (可选) _macOS_
重新设置窗口的宽高值,并且移动到指定的 `x` , `y` 位置.

### `win.getBounds()`
返回一个对象,它包含了窗口的宽,高,x坐标,y坐标.

### `win.setSize(width, height[, animate])`
* `width` Integer
* `height` Integer
* `animate` Boolean (可选) _macOS_
重新设置窗口的宽高值.

### `win.getSize()`
返回一个数组,它包含了窗口的宽,高.

####`win.setContentSize(width,height [,animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean(可选)_macOS_
重新设置窗口客户端的宽高值(例如网页界面).

####`win.getContentSize()`
返回一个数组,它包含了窗口客户端的宽,高.

####`win.setMinimumSize(width,height)`
* `width`Integer
* `height`Integer
设置窗口最小化的宽高值.

####`win.getMinimumSize()`
返回一个数组,它包含了窗口最小化的宽,高.

####`win.setMaximumSize(width,height)`
* `width`Integer
* `height`Integer
设置窗口最大化的宽高值.

####`win.getMaximumSize()`
返回一个数组,它包含了窗口最大化的宽,高.

####`win.setResizable(resizable)`
* `resizable` Boolean
设置窗口是否可以由用户手动调整大小。

####`win.isResizable()`
返回`Boolean`  - 窗口是否可以由用户手动调整大小。

####`win.setMovable(movable)`_macOS_ _Windows_
* `movable` Boolean
设置窗口是否可以被用户拖动. Linux 无效.

####`win.isMovable()`_macOS_ _Windows_
返回 Boolean,窗口是否可以被用户拖动. Linux 总是返回 `true`.

####`win.setMinimizable(minimizable)`_macOS_ _Windows_
* `minimizable` Boolean
设置窗口是否可以最小化. Linux 无效.

####`win.isMinimizable()`_macOS_ _Windows_
返回 Boolean,窗口是否可以最小化. Linux 总是返回 `true`.

####`win.setMaximizable(maximizable)`_macOS_ _Windows_
* `maximizable` Boolean
返回 Boolean,窗口是否可以最小化. Linux 总是返回 `true`.

####`win.isMaximizable()`_macOS_ _Windows_
返回 Boolean,窗口是否可以最大化. Linux 总是返回 `true`.

####`win.setFullScreenable(fullscreenable)`
* `fullscreenable` Boolean
设置点击最大化按钮是否可以全屏或最大化窗口.

####`win.isFullScreenable()`
返回 Boolean,点击最大化按钮是否可以全屏或最大化窗口.

####`win.setClosable(closable)`_macOS_ _Windows_
* `closable` Boolean
设置窗口是否可以人为关闭. Linux 无效.

####`win.isClosable()`_macOS_ _Windows_
返回 Boolean,窗口是否可以人为关闭. Linux 总是返回 `true`.
####`win.setAlwaysOnTop(flag [,level])`

* `flag`Boolean
* `level` String(可选)_macOS_  - 值包括 `normal`, `floating` , `torn-off-menu`, `modal-panel`, `main-menu`, `status`, `pop-up-menu`,
  `screen-saver`。默认是`floating`。到[macOS docs] [window-levels]了解更多信息。
是否设置这个窗口始终在其他窗口之上.设置之后,这个窗口仍然是一个普通的窗口,不是一个不可以获得焦点的工具箱窗口.

####`win.isAlwaysOnTop()`
返回 Boolean,当前窗口是否始终在其它窗口之前.

####`win.center()`
窗口居中.

####`win.setPosition(x,y [,animate])`
* `x`Integer
* `y`Integer
* `animate` Boolean(可选)_macOS_
移动窗口到对应的 `x` and `y` 坐标.

####`win.getPosition()`
返回一个包含当前窗口位置的数组.

####`win.setTitle(title)`
* `title` String
将本机窗口的标题更改为 `title`。

####`win.getTitle()`
返回`String`  - 本机窗口标题。
**注意：**网页的标题可以不同于本机标题。

####`win.setSheetOffset(offsetY [,offsetX])`_macOS_

* `offsetY`Float
* `offsetX` Float(可选)
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

let toolbarRect = document.getElementById('toolbar')。getBoundingClientRect()
win.setSheetOffset(toolbarRect.height)
```

####`win.flashFrame(flag)`
* `flag`Boolean
开始或停止闪烁窗口以吸引用户的注意。

####`win.setSkipTaskbar(skip)`

* `skip` Boolean
使窗口不显示在任务栏中。

####`win.setKiosk(flag)`
* `flag`Boolean
进入或离开 kiosk 模式.

####`win.isKiosk()`
返回`Boolean`  - 窗口是否处于kiosk模式。

####`win.getNativeWindowHandle()`
以 `Buffer` 形式返回这个具体平台的窗口的句柄.
windows上句柄类型为 `HWND` ,macOS `NSView* ` , Linux `Window`.

#### `win.hookWindowMessage(message, callback)` _Windows_
* `message` Integer
* `callback` Function
拦截windows 消息,在 WndProc 接收到消息时触发 `callback`函数.

####`win.isWindowMessageHooked(message)` _Windows_
* `message` Integer
返回 `true` or `false` 来代表是否拦截到消息.

####`win.unhookWindowMessage(message)` _Windows_
* `message` Integer
不拦截窗口消息.

####`win.unhookAllWindowMessages()`_Windows_
窗口消息全部不拦截.

### `win.setRepresentedFilename(filename)` _macOS_
* `filename` String
设置窗口当前文件路径,并且将这个文件的图标放在窗口标题栏上.

####`win.getRepresentedFilename()`_macOS_
获取窗口当前文件路径.

####`win.setDocumentEdited(edited)`_macOS_
* `edited`Boolean
明确指出窗口文档是否可以编辑,如果可以编辑则将标题栏的图标变成灰色.

####`win.isDocumentEdited()`_macOS_
返回 Boolean,当前窗口文档是否可编辑.

####`win.focusOnWebView()`

####`win.blurWebView()`

####`win.capturePage([rect,] callback)`

* `rect` Object (可选) - 捕获Page位置
  * `x` Integer
  * `y` Integer
  * `width` Integer
  * `height` Integer
* `callback` Function
  * `image` [NativeImage](native-image.md)

与 `webContents.capturePage([rect,] callback)`相同。

####`win.loadURL(url [,options])`

* `url` String
* `options` Object(可选)
  * `httpReferrer` String(可选) - HTTP来源网址。
  * `userAgent` String(可选) - 发起请求的userAgent。
  * `extraHeaders`String(可选) - 用 `\ n`分割的额外标题
  * `postData`([UploadRawData](structures / upload-raw-data.md)| [UploadFile](structures / upload-file.md)| [UploadFileSystem](structures / upload-file-system.md)| [UploadBlob ](structures / upload-blob.md))[] - (可选)

与 `webContents.loadURL(url [,options])`相同。

`url`可以是远程地址(例如`http：//`)或本地路径
HTML文件使用`file：//`协议。
要确保文件URL格式正确,建议使用
节点的[`url.format`](https://nodejs.org/api/url.html#url_url_format_urlobject)
方法：

```javascript
let url = require('url')。format({
  protocol：'file',
  斜杠：true,
  pathname：require('path')。join(__ dirname,'index.html')
})
win.loadURL(url)
```
您可以使用带有URL编码数据的`POST`请求​​加载URL
下列：
```javascript
win.loadURL('http：// localhost：8000 / post',{
  发布数据： [{
    类型：'rawData',
    bytes：Buffer.from('hello = world')
  }],
  extraHeaders：'Content-Type：application / x-www-form-urlencoded'
})
```

####`win.reload()`
与 `webContents.reload` 相同。

####`win.setMenu(menu)`_Linux_ _Windows_
* `menu`菜单
设置菜单栏的 `menu` ，设置它为 `null` 则表示不设置菜单栏.

####`win.setProgressBar(progress [,options])`
* `progress` Double
* `options` Object(可选)
  * `mode` String _Windows_ - 进度条的模式。可以是 `none`, `normal`, `indeterminate`, `error`或 `paused`。
在进度栏中设置进度值。有效范围为[0,1.0]。
当进度小于0时则不显示进度;
当进度大于0时显示结果不确定.

在Linux平台上,只支持Unity桌面环境,需要指定
在libux上，只支持Unity桌面环境，需要指明 `*.desktop` 文件并且在 `package.json` 中添加文件名字.默认它为 `app.getName().desktop`.

在Windows上,可以传递模式。接受的值是 `none`, `normal`, `indeterminate`, `error`和 `paused`。
如果 `setProgressBar`没有设置模式(但是值在有效范围内),将假定为 `normal`。

####`win.setOverlayIcon(overlay,description)`_Windows_
* `overlay` [NativeImage](native-image.md) -  在底部任务栏右边显示图标.16 x 16像素
* `description` String - 描述


####`win.setHasShadow(hasShadow)`_macOS_
* `hasShadow` Boolean
设置窗口是否应该有阴影.在Windows和Linux系统无效.

####`win.hasShadow()`_macOS_
返回 boolean,设置窗口是否有阴影.在Windows和Linux系统始终返回`true`.

####`win.setThumbarButtons(buttons)`_Windows_
* `buttons` [ThumbarButton []](structures / thumbar-button.md)
在窗口的任务栏button布局出为缩略图添加一个有特殊button的缩略图工具栏. 返回一个 `Boolean` 对象来指示是否成功添加这个缩略图工具栏.

因为空间有限，缩略图工具栏上的 button 数量不应该超过7个.一旦设置了，由于平台限制，就不能移动它了.但是你可使用一个空数组来调用api来清除 buttons .

所有 `buttons` 是一个 `Button` 对象数组:

* `Button` Object
  * `icon` [NativeImage](native-image.md) - 在工具栏上显示的图标.
  * `click` Function
  * `tooltip` String (可选) - tooltip 文字.
  * `flags` Array (可选) - 控制button的状态和行为. 默认它是 `['enabled']`.

`flags` 是一个数组，它包含下面这些 `String`s:

* `enabled` - button 为激活状态并且开放给用户.
* `disabled` -button 不可用. 目前它有一个可见的状态来表示它不会响应你的行为.
* `dismissonclick` - 点击button，这个缩略窗口直接关闭.
* `nobackground` - 不绘制边框，仅仅使用图像.
* `hidden` - button 对用户不可见.
* `noninteractive` - button 可用但是不可响应; 也不显示按下的状态. 它的值意味着这是一个在通知单使用 button 的实例.


####`win.setThumbnailClip(region)`_Windows_
* `region` [Rectangle](structures / rectangle.md) - 选定窗口区域以当做任务栏悬停时的窗口缩图.
为空则: `{x：0,y：0,width：0,height：0}`。

####`win.setThumbnailToolTip(toolTip)`_Windows_
* `toolTip` String
任务栏悬停时的文字提示

####`win.setAppDetails(options)`_Windows_
* `options`对象
  * `appId` String(可选) - Window的[App User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391569(v = vs.85).aspx)。
    它必须设置,否则其他选项将没有效果。
  * `appIconPath` String(可选) - 窗口的[重新启动图标](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391573(v = vs.85).aspx)。
  * `appIconIndex` Integer(可选) - `appIconPath`中的图标索引。
    未设置`appIconPath`时忽略。默认值为0。
  * `relaunchCommand` String(可选) - Window的[重新启动命令](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391571(v = vs.85).aspx)。

  * `relaunchDisplayName` String(可选) - 窗口的[重新启动显示名称](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391572(v = vs.85).aspx)。
  
设置窗口的任务栏按钮的属性。

**注意：** `relaunchCommand`和`relaunchDisplayName`必须始终设置
一起。如果没有设置这些属性中的一个,那么两者都不会被使用。

####`win.showDefinitionForSelection()`_macOS_
与 `webContents.showDefinitionForSelection()`相同。

####`win.setIcon(icon)`_Windows_ _Linux_
* `icon` [NativeImage](native-image.md)
窗口图标。


### `win.setAutoHideMenuBar(hide)`
* `hide` Boolean
设置窗口的菜单栏是否可以自动隐藏. 一旦设置了，只有当用户按下 `Alt` 键时则显示.
如果菜单栏已经可见，调用 `setAutoHideMenuBar(true)` 则不会立刻隐藏.

### `win.isMenuBarAutoHide()`
返回 boolean,窗口的菜单栏是否可以自动隐藏.

### `win.setMenuBarVisibility(visible)` _Windows_ _Linux_
* `visible` Boolean
设置菜单栏是否可见.如果菜单栏自动隐藏，用户仍然可以按下 `Alt` 键来显示.

### `win.isMenuBarVisible()`
返回 boolean,菜单栏是否可见.

### `win.setVisibleOnAllWorkspaces(visible)`
* `visible` Boolean
设置窗口是否在所有地方都可见.
**注意:** 这个api 在windows无效.

####`win.isVisibleOnAllWorkspaces()`
返回 boolean,窗口是否在所有地方都可见.
**注意：**此API在Windows上始终返回false。

####`win.setIgnoreMouseEvents(ignore)`
* `ignore` Boolean
使窗口忽略所有鼠标事件。
在这个窗口中发生的所有鼠标事件将被传递到下面的窗口
如果这个窗口有焦点,它仍然会接收键盘事件。

####`win.setContentProtection(enable)`_macOS_ _Windows_
* `enable` Boolean
防止窗口内容被其他应用捕获。
在macOS它设置NSWindow的sharingType为NSWindowSharingNone。
在Windows上,它使用`WDA_MONITOR`调用SetWindowDisplayAffinity。

####`win.setFocusable(focusable)`_Windows_
* `focusable` Boolean
窗口是否可以聚焦。

####`win.setParentWindow(parent)`_Linux_ _macOS_
* `parent` BrowserWindow
将`parent`设置为当前窗口的父窗口,传递`null`将会转向
当前窗口进入顶级窗口。

####`win.getParentWindow()`
返回`BrowserWindow` - 父窗口。

####`win.getChildWindows()`
返回`BrowserWindow []` - 所有子窗口。

####`win.setAutoHideCursor(autoHide)`_macOS_
* `autoHide` Boolean
控制在输入时是否隐藏光标。

####`win.setVibrancy(type)`_macOS_
* `type` String - Can be `appearance-based`, `light`, `dark`, `titlebar`,
  `selection`, `menu`, `popover`, `sidebar`, `medium-light` , `ultra-dark`. 更多细节请前往[macos documentation] [vibrancy-docs]。

[blink-feature-string]: https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.in
[quick-look]: https://en.wikipedia.org/wiki/Quick_Look
[vibrancy-docs]: https://developer.apple.com/reference/appkit/nsvisualeffectview?language=objc
[window-levels]: https://developer.apple.com/reference/appkit/nswindow/1664726-window_levels
[chrome-content-scripts]: https://developer.chrome.com/extensions/content_scripts#execution-environment
