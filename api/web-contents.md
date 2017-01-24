# 本文介绍: webContents网页内容的渲染与控制方法

> 渲染并控制网页

进程: [主进程](../glossary.md#main-process)         

 `webContents`是个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter),也是[`BrowserWindow`](browser-window.md) 对象的属性,专门负责渲染和控制页面
 
例子:
```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({width: 800, height: 1500})
win.loadURL('http://github.com')

let contents = win.webContents
console.log(contents)
```

## 方法列表
`webContents` 对象有下列事件:
```javascript
const {webContents} = require('electron')
console.log(webContents)
```

### `webContents.getAllWebContents()`

返回 `WebContents[]` - 所有 `WebContents` 实例组成的数组。
包含了全部窗口，网页视图，打开的devtools和devtools背景页面的所有web内容。

### `webContents.getFocusedWebContents()`

返回 `WebContents` - 应用程序里已聚焦(Focused)的Web内容，否则返回 `null`。

### `webContents.fromId(id)`

* `id` Integer

返回 `WebContents` -具有给定ID的WebContents实例。

### 实例事件

#### 事件: 'did-finish-load'

导航完成时(即选项卡中的微调框(spinner)已停止旋转，并且已分派 `onload`事件)触发。

#### 事件: 'did-fail-load'

返回:

* `event` Event
* `errorCode` Integer
* `errorDescription` String
* `validatedURL` String
* `isMainFrame` Boolean

类似`done-finish-load`，不过它是当加载失败或者是被取消(比如调用了 `window.stop()`)时触发,。
[这里](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)列出了所有错误代码及其含义.
由于重定向响应也会触发`errorCode` -3,也许您会希望忽略该错误。
 
#### 事件: 'did-frame-finish-load'
返回:
* `event` Event
* `isMainFrame` Boolean
当一个 frame窗口 完成导航时触发

#### 事件: 'did-start-loading'
选项卡中的微调框(spinner)开始旋转(loading)时触发

#### 事件: 'did-stop-loading'
选项卡中的微调框(spinner)结束了旋转(loading)时触发

#### 事件: 'did-get-response-details'

返回:

* `event` Event
* `status` Boolean
* `newURL` String
* `originalURL` String
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object
* `resourceType` String

在所请求资源的相关详细信息可用时触发。
 `status`标识了使用套接字连接( socket connection)来下载资源。

#### 事件: 'did-get-redirect-request'
返回:
* `event` Event
* `oldURL` String
* `newURL` String
* `isMainFrame` Boolean
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object

在请求资源时接收到重定向时触发

#### 事件: 'dom-ready'
返回:
* `event` Event

当指定 frame 中的文档 加载完成时触发

#### 事件: 'page-favicon-updated'
返回:
* `event` Event
* `favicons` String[] - URL数组

网页接收到(图标的url)favicon url时触发

#### 事件: 'new-window'

返回:

* `event` Event
* `url` String
* `frameName` String
* `disposition` String - 可为 `default`, `foreground-tab`, `background-tab`, `new-window`, `save-to-disk` 和 `other`.
* `options` Object - 创建新的 `BrowserWindow`时使用的参数.
* `additionalFeatures` String[] - 给予`window.open()`的非标准句柄(不是由Chromium或Electron处理的句柄)。

当页面通过 `window.open`或外部链接(如 `<a target='_blank'>`)来 **请求 `url`打开一个新窗口**(默认创建一个新的 `BrowserWindow`)时触发。
当然,您也可以调用 `event.preventDefault()`,阻止创建新窗口。
在这种情况下， `event.newGuest`可以设置为一个 `BrowserWindow`实例的引用，以便Electron运行时使用它。

#### 事件: 'will-navigate'

返回:

* `event` Event
* `url` String

 **用户或页面想要开始导航(比如 `window.location`对象更改或用户单击页面中的链接时)时触发**。
`webContents.loadURL`和 `webContents.back`之类的API以编程方式启动导航时，该事件不会触发。
页内跳转如 点击了锚链接 或 更新 `window.location.hash`, 该事件也不会触发,不您可以使用 `did-navigate-in-page`事件达到目的。

需要阻止导航,请调用 `event.preventDefault()`。

#### 事件: 'did-navigate'

返回:

* `event` Event
* `url` String

 **导航完成时触发**.

类似'will-navigate',页内跳转如 点击了锚链接 或 更新 `window.location.hash`, 该事件也不会触发,不过您可以使用 `did-navigate-in-page`事件达到目的。

#### 事件: 'did-navigate-in-page'

返回:

* `event` Event
* `url` String
* `isMainFrame` Boolean
 **页内跳转时触发(虽然页面的url已改变但它不会跳出界面,如点击了锚链接 或 DOM的`hashchange`事件被触发时)**

#### 事件: 'crashed'
返回:
* `event` Event
* `killed` Boolean
 **渲染进程崩溃的时候触发**

#### 事件: 'plugin-crashed'
返回:
* `event` Event
* `name` String
* `version` String
 **插件进程崩溃时候触发**

#### 事件: 'destroyed'
 **`webContents`被销毁后触发**

#### 事件: 'before-input-event'
返回:
* `event` Event
* `input` Object -输入属性
  * `type` String - 如 `keyUp` 或 `keyDown`
  * `key` String - 相当于 [KeyboardEvent.key][keyboardevent]
  * `isAutoRepeat` Boolean - 相当于 [KeyboardEvent.repeat][keyboardevent]
  * `shift` Boolean - 相当于 [KeyboardEvent.shiftKey][keyboardevent]
  * `control` Boolean - 相当于 [KeyboardEvent.controlKey][keyboardevent]
  * `alt` Boolean - 相当于 [KeyboardEvent.altKey][keyboardevent]
  * `meta` Boolean - 相当于 [KeyboardEvent.metaKey][keyboardevent]

 **进行分派 `keydown`和 `keyup`事件之前触发**。
调用`event.preventDefault`则禁止页面调用`keydown`或`keyup`事件。

#### 事件: 'devtools-opened'

 **开发调试工具打开时触发**

#### 事件: 'devtools-closed'

 **开发调试工具关闭时触发**

#### 事件: 'devtools-focused'

 **开发调试工具获得焦点或当前已打开后触发**

#### 事件: 'certificate-error'
返回:
* `event` Event
* `url` String
* `error` String - 错误代码
* `certificate` [Certificate](structures/certificate.md)
* `callback` Function
  * `isTrusted` Boolean -证书是否受信任
 **验证 `url`的证书( `certificate`) 失败或错误时触发**
该事件使用方法类似 [ `app`模块的 `certificate-error`事件](app.md#event-certificate-error)        


#### 事件: 'select-client-certificate'
返回:
* `event` Event
* `url` URL
* `certificateList` [Certificate[]](structures/certificate.md)
* `callback` Function
  * `certificate` [Certificate](structures/certificate.md) - 必须是来自给定列表的证书
 **请求客户端证书时触发**
该事件使用方法类似 [ `app`模块的 `select-client-certificate`事件](app.md#event-select-client-certificate)         

#### 事件: 'login'
返回:
* `event` Event
* `request` Object
  * `method` String
  * `url` URL
  * `referrer` URL
* `authInfo` Object
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function
  * `username` String
  * `password` String
 
 **当`webContents`进行基本验证时触发**
该事件使用方法类似 [`app`模块的 `login` 事件](app.md#event-login).       

#### 事件: 'found-in-page'
返回:
* `event` Event
* `result` Object
  * `requestId` Integer
  * `activeMatchOrdinal` Integer - 活动匹配位置
  * `matches` Integer -匹配数
  * `selectionArea` Object -第一个匹配区域的坐标。
 **当[`webContents.findInPage`]进行页内查找并且找到可用值触发**

#### 事件: 'media-started-playing'
 **媒体开始播放触发**

#### 事件: 'media-paused'
 **媒体暂停或播放完毕时触发**

#### 事件: 'did-change-theme-color'
 **页面的主题颜色被更改时触发**
通常是因为使用了元标记如theme-color引起:
```html
<meta name='theme-color' content='#ff0000'>
```

#### 事件: 'update-target-url'
返回:
* `event` Event
* `url` String
 **鼠标移动到链接或键盘将焦点移动到链接时触发**

#### 事件: 'cursor-changed'

返回:

* `event` Event
* `type` String
* `image` NativeImage (可选)
* `scale` Float (可选) - 光标缩放系数
* `size` Object (可选) -  `image`的尺寸
  * `width` Integer
  * `height` Integer
* `hotspot` Object (可选) - 光标的hotspot坐标
  * `x` Integer - x 坐标
  * `y` Integer - y 坐标

 **光标类型被更改时触发**
  `type` 参数可选值有 `default`, `crosshair`, `pointer`, `text`, `wait`, `help`, `e-resize`, `n-resize`,
 `ne-resize`, `nw-resize`, `s-resize`, `se-resize`, `sw-resize`, `w-resize`, `ns-resize`, `ew-resize`, `nesw-resize`, 
 `nwse-resize`, `col-resize`, `row-resize`, `m-panning`, `e-panning`, `n-panning`, `ne-panning`, `nw-panning`,
 `s-panning`, `se-panning`, `sw-panning`, `w-panning`, `move`, `vertical-text`,
 `cell`, `context-menu`, `alias`, `progress`, `nodrop`, `copy`, `none`,
 `not-allowed`, `zoom-in`, `zoom-out`, `grab`, `grabbing`, `custom`.

如果 `type`参数是 `custom`， `image`参数会在`NativeImage`中保存自定义光标图像.
 `scale`， `size`和 `hotspot`则保存关于自定义光标的相关信息。

#### 事件: 'context-menu'

返回:

* `event` Event
* `params` Object
  * `x` Integer - x 坐标
  * `y` Integer - y 坐标
  * `linkURL` String - 包含调用上下文菜单的上级菜单节点的URL
  * `linkText` String - 与以上链接相关联的文本。如果链接内容是图像，请留空。
  * `pageURL` String -  调用上下文菜单的顶级页面链接
  * `frameURL` String -  调用上下文菜单的子框架的URL
  * `srcURL` String -  调用上下文菜单的元素的源URL,如图片,音频或视频等链接
  * `mediaType` String - 节点类型,如 `none`, `image`, `audio`, `video`, `canvas`, `file` 或 `plugin`.
  * `hasImageContents` Boolean - 是否在具有非空内容的图像上调用上下文菜单。
  * `isEditable` Boolean - 上下文是否可编辑。
  * `selectionText` String - 调用上下文菜单的选择的文本。
  * `titleText` String -调用上下文菜单的标题或替代文本。
  * `misspelledWord` String - 光标下的拼写错误的单词(如果有)。
  * `frameCharset` String -调用上下文菜单的框架的字符编码。
  * `inputFieldType` String -该字段的类型(如果在输入字段上调用了上下文菜单)。可选值为 `none`, `plainText`, `password`, `other`.
  * `menuSourceType` String - 调用上下文菜单的输入源。可选值为 `none`, `mouse`, `keyboard`, `touch`, `touchMenu`.
  * `mediaFlags` Object - 调用上下文菜单的媒体元素标志
    * `inError` Boolean - 媒体元素是否已崩溃
    * `isPaused` Boolean - 媒体元素是否已暂停。
    * `isMuted` Boolean - 媒体元素是否静音。
    * `hasAudio` Boolean - 媒体元素是否具有音频。
    * `isLooping` Boolean - 媒体元素是否循环。
    * `isControlsVisible` Boolean  - 媒体元素的控件是否可见。
    * `canToggleControls` Boolean  - 媒体元素的控件是否可以切换。
    * `canRotate` Boolean  - 是否可以旋转媒体元素。
  * `editFlags` Object  -渲染器是否相信它能够执行相应的操作。
    * `canUndo` Boolean  - 渲染器是否相信它可以撤消。
    * `canRedo` Boolean  - 渲染器是否相信它可以重做。
    * `canCut` Boolean  - 渲染器是否相信它可以裁剪。
    * `canCopy` Boolean  - 渲染器是否相信它可以复制
    * `canPaste` Boolean  - 渲染器是否相信它可以粘贴。
    * `canDelete` Boolean  - 渲染器是否相信它可以删除。
    * `canSelectAll` Boolean  - 渲染器是否相信它可以选择所有。

 **当有一个新的上下文菜单需要处理时触发**

#### 事件: 'select-bluetooth-device'
返回:
* `event` Event
* `devices` [BluetoothDevice[]](structures/bluetooth-device.md)
* `callback` Function
  * `deviceId` String

 **调用 `navigator.bluetooth.requestDevice` 后进行选择蓝牙设备时触发。**
必须先启用 `webBluetooth`,才能调用 `navigator.bluetooth` .
如果没有调用 `event.preventDefault`，则默认选择第一个可用的设备。
 `callback`应该用 `deviceId`来调用被选择，如果传递空的字符串到 `callback`则意味着取消请求。
```javascript
const {app, webContents} = require('electron')
app.commandLine.appendSwitch('enable-web-bluetooth')

app.on('ready', () => {
  webContents.on('select-bluetooth-device', (event, deviceList, callback) => {
    event.preventDefault()
    let result = deviceList.find((device) => {
      return device.deviceName === 'test'
    })
    if (!result) {
      callback('')
    } else {
      callback(result.deviceId)
    }
  })
})
```

#### 事件: 'paint'
返回:
* `event` Event
* `dirtyRect` [Rectangle](structures/rectangle.md)
* `image` [NativeImage](native-image.md) - 整个 frame 的图像数据

 **生成新frame时触发**。
只有脏区(dirty area)可传递至缓冲区。

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({webPreferences: {offscreen: true}})
win.webContents.on('paint', (event, dirty, image) => {
  // updateBitmap(dirty, image.getBitmap())
})
win.loadURL('http://github.com')
```

#### 事件: 'devtools-reload-page'

 **在开发者调试工具栏中重新加载webContents时触发**

## 实例方法

#### `contents.loadURL(url[, options])`

* `url` String
* `options` Object (可选)
  * `httpReferrer` String (可选) - 来源网址
  * `userAgent` String (可选) - 发起请求的userAgent.
  * `extraHeaders` String (可选) - 用`\ n`分隔的额外头
  * `postData` ([UploadRawData](structures/upload-raw-data.md) | [UploadFile](structures/upload-file.md) | [UploadFileSystem](structures/upload-file-system.md) | [UploadBlob](structures/upload-blob.md))[] - (可选)

 **作用:加载 `url`到窗口中** 
 `url` 必须包含协议前缀,比如 `http://` 或 `file://`. 如果加载想要忽略 http 缓存，请使用 `pragma` 头实现..
 
```javascript
const {webContents} = require('electron')
const options = {extraHeaders: 'pragma: no-cache\n'}
webContents.loadURL('https://github.com', options)
```

#### `contents.downloadURL(url)`
* `url` String

 **作用: `url` 作为下载链接进行直接下载,无需在标签页中打开**
此时 `session` 的 `will-download` 事件会触发.

#### `contents.getURL()`

返回 `String` - 当前页面的url链接
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

let currentURL = win.webContents.getURL()
console.log(currentURL)
```

#### `contents.getTitle()`
返回 `String` - 当前网页的标题。

#### `contents.isDestroyed()`
返回 `Boolean` - 网页是否被销毁。

#### `contents.isFocused()`
返回 `Boolean` -网页是否已聚焦

#### `contents.isLoading()`
返回 `Boolean` - 网页是否仍在加载资源

#### `contents.isLoadingMainFrame()`
返回 `Boolean` - 主框架(而不仅仅是其中的iframe或框架)是否仍在加载。

#### `contents.isWaitingForResponse()`
返回`Boolean`  - 网页是否等待来自页面的主资源的第一响应。

####`content.stop()`
停止任何待处理的导航。

####`contents.reload()`
重新加载当前网页。

####`contents.reloadIgnoringCache()`
忽略缓存的重新加载当前 guest page。

####`content.canGoBack()`
返回 `Boolean`  - 浏览器是否可以可后退至上个网页。

####`contents.canGoForward()`
返回 `Boolean`  - 浏览器是否可以前进到下一个网页。

####`contents.canGoToOffset(offset)`
* `offset`String
返回 `Boolean`  - 网页是否可以转到 `offset`这个页面。

####`contents.clearHistory()`
清除历史记录。

####`contents.goBack()`
使浏览器后退往上一页

####`contents.goForward()`
使浏览器前进往下一页

####`contents.goToIndex(index)`
* `index`String
将浏览器导航到指定索引 `index` 的网页。

####`contents.goToOffset(offset)`
* `offset`String
从 当前页 到指定 `offset`的偏移量。

####`contents.isCrashed()`
返回 `Boolean`  - 渲染器进程是否崩溃。

####`contents.setUserAgent(userAgent)`
* `userAgent` String
重定义此网页的userAgent。

####`contents.getUserAgent()`
返回 `String`  - 此网页的userAgent信息

####`contents.insertCSS(css)`
* `css` String
将CSS插入到当前网页中。

#### `contents.executeJavaScript(code[, userGesture, callback])`
* `code` String
* `userGesture` Boolean (可选)  - 是否支持用户手势 默认为 `false`.
* `callback` Function (可选) - script脚本执行后调用
  * `result` Any
返回 `Promise` - 是否可解析执行代码 `code`

作用:在页面中评估(eval) `code` .

将 `userGesture` 设置为 `true`,可对去除 某些HTML API 只能通过 手势 进行调用 的限制,比如 `requestFullScreen`。
例子,返回一个代码解析结果:
```js
contents.executeJavaScript('fetch(`https://jsonplaceholder.typicode.com/users/1`).then(resp => resp.json())', true)
  .then((result) => {
    console.log(result) // 来自fetch调用的JSON对象
  })
```

#### `contents.setAudioMuted(muted)`
* `muted` Boolean
使当前网页上的音频静音

#### `contents.isAudioMuted()`
返回 `Boolean` -此网页是否已静音。

#### `contents.setZoomFactor(factor)`
* `factor` Number - 缩放系数

设置页面的缩放系数。
注意:缩放系数是百分制的，如3.0=300％。

#### `contents.getZoomFactor(callback)`
* `callback` Function
  * `zoomFactor` Number
获得当前缩放系数后,通过 `callback(zoomFactor)`调用 `callback` .

#### `contents.setZoomLevel(level)`
* `level` Number - 缩放级别
将缩放级别更改为指定级别。
原始大小为0，每个增量表示放大或缩小20％,默认限制为原始大小的300％至50％。

#### `contents.getZoomLevel(callback)`
* `callback` Function
  * `zoomLevel` Number
获得当前缩放级别后,通过 `callback(zoomFactor)`调用 `callback` .

#### `contents.setVisualZoomLevelLimits(minimumLevel, maximumLevel)`
* `minimumLevel` Number
* `maximumLevel` Number
设置缩放级别的最大值和最小值.

#### `contents.setLayoutZoomLevelLimits(minimumLevel, maximumLevel)`
* `minimumLevel` Number
* `maximumLevel` Number
设置基于布局(即非视觉)的缩放级别的最大值和最小值.

#### `contents.undo()`
在网页中执行撤销( `undo`)。

#### `contents.redo()`
在网页中执行重做( `redo`)。

#### `contents.cut()`
在网页中执行剪切( `cut`)。

#### `contents.copy()`
在网页中执行复制( `copy`)。

#### `contents.copyImageAt(x, y)`
* `x` Integer
* `y` Integer
将指定位置的图像复制到剪贴板。

#### `contents.paste()`
在网页中执行粘贴( `paste`)。

#### `contents.pasteAndMatchStyle()`
在网页中执行粘贴并清除样式( `pasteAndMatchStyle`)。

#### `contents.delete()`
在网页中执行删除( `delete`)。

#### `contents.selectAll()`
在网页中执行全选( `selectAll`)。

#### `contents.unselect()`
在网页中执行取消选择( `unselect`)。

#### `contents.replace(text)`
* `text` String
在网页中执行替换( `replace`)文本( `text`)。

#### `contents.replaceMisspelling(text)`
* `text` String
在网页中执行校正( `replaceMisspelling`)错别字( `text`)。

#### `contents.insertText(text)`
* `text` String
插入`text`到焦点元素。

#### `contents.findInPage(text[, options])`

* `text` String -  查找的内容, 不能为空.
* `options` Object (可选)
  * `forward` Boolean - (可选) 是否向前或向后查找, 默认为 `true`.
  * `findNext` Boolean - (可选) 是否连续查找, 默认为 `false`.
  * `matchCase` Boolean - (可选) 查找是否区分大小写,,  默认为 `false`.
  * `wordStart` Boolean - (可选) 是否仅以首字母查找.. 默认为 `false`.
  * `medialCapitalAsWordStart` Boolean - (可选) 如果按大写字母开头匹配,那么后面的小写字母或无字母或多词合成等都视为匹配,默认为 `false`.

在页面中发起一个请求进行查找 `text` 并返回一个 `Integer` 当做请求id. 您可以通过[`found-in-page`](web-contents.md#event-found-in-page) 事件获取相应的请求结果.

#### `contents.stopFindInPage(action)`

* `action` String - 指派[`webContents.findInPage`]请求结束时要执行的操作。
  * `clearSelection` - 清除选择。
  * `keepSelection` - 转为没有任何action等等的普通选择
  * `activateSelection` - 聚焦并单击该选择
  
使用给定的 `action` 来为 `webContents` 停止任何 `findInPage` 请求.
```javascript
const {webContents} = require('electron')
webContents.on('found-in-page', (event, result) => {
  if (result.finalUpdate) webContents.stopFindInPage('clearSelection')
})

const requestId = webContents.findInPage('api')
console.log(requestId)
```

#### `contents.capturePage([rect, ]callback)`

* `rect` [Rectangle](structures/rectangle.md) (可选) -截图 `rect`区域, 如果为空,意味着截图整个页面.
* `callback` Function
  * `image` [NativeImage](native-image.md)

截图 `rect`区域后, 通过 `callback(image)`调用 `callback`.
参数 `image` 是个[NativeImage](native-image.md) 实例,用于存储截图.

#### `contents.hasServiceWorker(callback)`

* `callback` Function
  * `hasWorker` Boolean
  
检查是否有已注册的ServiceWorker，返回 `hasWorker` 响应 `callback`。


#### `contents.unregisterServiceWorker(callback)`

* `callback` Function
  * `success` Boolean
注销所有ServiceWorker后,返回 `success` 响应 `callback`。

#### `contents.print([options])`

* `options` Object (可选)
  * `silent` Boolean - 静默打印即将选择系统的默认打印机和默认设置进行打印.一般默认为 `false`.
  * `printBackground` Boolean - 同时打印网页的背景颜色和图像,默认为 `false`.

在网页中调用 `window.print()` 即等同于 `webContents.print({silent: false, printBackground: false})`.
您可以使用 `page-break-before: always;` 这个CSS样式打输出打印预览到新页面中.
 **注意:** 在 Windows, 打印 API 依赖于 `pdf.dll`. 如果你的应用不使用任何的打印, 你可以安全删除 `pdf.dll` 来减少文件大小.

#### `contents.printToPDF(options, callback)`

* `options` Object
  * `marginsType` Integer - (可选) 边距类型, `0`为默认边距, `1`为无边距, and `2`为中等边距.
  * `pageSize` String - (可选) 大小类型, 可选 `A3`,
    `A4`, `A5`, `Legal`, `Letter`, `Tabloid` 或包含有 `height`和 `width`属性的对象
  * `printBackground` Boolean - (可选) 是否打印页面CSS背景
  * `printSelectionOnly` Boolean - (可选) 是否仅打印已选择的内容
  * `landscape` Boolean - (可选) `true`为横向， `false`为纵向。
* `callback` Function
  * `error` Error
  * `data` Buffer
  
完成时使用 `callback(error, data)` 调用 `callback` . `data` 是一个 `Buffer` ，包含了生成的 pdf 数据.

如果css样式中强制定义了 `@page` , `landscape`会被忽略.

默认情况下，`options`为空时将被视为：
```javascript
{
  marginsType: 0,
  printBackground: false,
  printSelectionOnly: false,
  landscape: false
}
```

您可以使用 `page-break-before: always;` 这个CSS样式打输出打印预览到新页面中.
```javascript
const {BrowserWindow} = require('electron')
const fs = require('fs')

let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

win.webContents.on('did-finish-load', () => {
//使用默认打印选项
  win.webContents.printToPDF({}, (error, data) => {
    if (error) throw error
    fs.writeFile('/tmp/print.pdf', data, (error) => {
      if (error) throw error
      console.log('Write PDF successfully.')
    })
  })
})
```

#### `contents.addWorkSpace(path)`
* `path` String
为开发者工具栏的 workspace 添加指定的路径.但必须在 DevTools 创建之后才能使用它 :
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.webContents.on('devtools-opened', () => {
  win.webContents.addWorkSpace(__dirname)
})
```

#### `contents.removeWorkSpace(path)`
* `path` String
从开发者工具栏的 workspace 移除指定的路径.

#### `contents.openDevTools([options])`
* `options` Object (可选)
  * `mode` String - 停靠方向类型, 可选 `right`(右侧), `bottom`(下方), `undocked`(窗口与工具栏分离), `detach`(新窗口打开工具栏). 默认为上次(最后一次)的方向
  
打开开发者工具栏

#### `contents.closeDevTools()`

关闭开发者工具栏

#### `contents.isDevToolsOpened()`

返回 `Boolean` - 开发者工具栏是否已打开

#### `contents.isDevToolsFocused()`
返回 `Boolean` - 开发者工具栏是否已聚焦

#### `contents.toggleDevTools()`
切换开发者工具栏

#### `contents.inspectElement(x, y)`
* `x` Integer
* `y` Integer
在 ( `x`,  `y`) 这个位置开始检查元素

#### `contents.inspectServiceWorker()`
为 service worker 上下文打开开发者工具栏.

#### `contents.send(channel[, arg1][, arg2][, ...])`

* `channel` String
* `...args` any[]

通过 `channel` 向主进程发送异步消息，也可以发送任意参数.参数会被JSON序列化，之后就不会包含函数或原型链.
主进程通过使用 `ipcRenderer` 模块来监听 `channel`，从而处理消息,

如从主进程向渲染进程发送消息 :
```javascript
// 主进程.
const {app, BrowserWindow} = require('electron')
let win = null

app.on('ready', () => {
  win = new BrowserWindow({width: 800, height: 600})
  win.loadURL(`file://${__dirname}/index.html`)
  win.webContents.on('did-finish-load', () => {
    win.webContents.send('ping', 'whoooooooh!')
  })
})
```

```html
<!-- index.html -->
<html>
<body>
  <script>
    require('electron').ipcRenderer.on('ping', (event, message) => {
      console.log(message)  // Prints 'whoooooooh!'
    })
  </script>
</body>
</html>
```

#### `contents.enableDeviceEmulation(parameters)`

* `parameters` Object
  * `screenPosition` String - S指定要模拟的屏幕类型(默认: `desktop`)
    * `desktop` - 桌面屏幕类型
    * `mobile` - 移动屏幕类型
  * `screenSize` Object - 屏幕大小(screenPosition == mobile)
    * `width` Integer - 屏幕宽度
    * `height` Integer - 屏幕高度
  * `viewPosition` Object - 将视图定位在屏幕上(screenPosition == mobile) (默认: `{x: 0, y: 0}`)
    * `x` Integer -从左上角设置x轴偏移
    * `y` Integer - 从左上角设置y轴偏移
  * `deviceScaleFactor` Integer - 设备缩放系数 (为0默认为设备原始缩放系数) (默认: `0`)
  * `viewSize` Object - 视图大小 (空表示不覆盖)
    * `width` Integer - 视图宽度
    * `height` Integer - 视图高度
  * `fitToView` Boolean - 需要时视图可按比例缩放以适应可用空间(默认：`false`)
  * `offset` Object - 可用空间内的模拟视图偏移 (不在适应模式)(默认: `{x: 0, y: 0}`)
  * `x` Float - 设置相对左上角的x轴偏移值
  * `y` Float - 设置相对左上角的y轴偏移值
* `scale` Float - 可用空间内的模拟视图偏移 (不在适应视图模式) (默认: `1`)

使用给定的参数来开启设备模拟.

#### `contents.disableDeviceEmulation()`
使用 `webContents.enableDeviceEmulation` 关闭设备模拟.

#### `contents.sendInputEvent(event)`

* `event` Object
  * `type` String (**必填**) - 事件类型,可选 `mouseDown`,
    `mouseUp`, `mouseEnter`, `mouseLeave`, `contextMenu`, `mouseWheel`,
    `mouseMove`, `keyDown`, `keyUp`, `char`.
  * `modifiers` String[] - 事件的 modifiers 数组, 可选 `shift`, `control`, `alt`, `meta`, `isKeypad`, `isAutoRepeat`,
    `leftButtonDown`, `middleButtonDown`, `rightButtonDown`, `capsLock`,
    `numLock`, `left`, `right`.

向页面发送输入 `event` .

对键盘事件来说，`event` 对象还有如下属性 :
* `keyCode` String (**必需**) - 特点是将作为键盘事件发送. 可用的 key codes 请参考[Accelerator](accelerator.md).

对于鼠标事件， `event`对象也有以下属性：

* `x` Integer (**必填**)
* `y` Integer (**必填**)
* `button` String - 被按下的按钮,可选 `left`, `middle`, `right`
* `globalX` Integer
* `globalY` Integer
* `movementX` Integer
* `movementY` Integer
* `clickCount` Integer

对鼠标滚轮事件来说， `event` 对象还有如下属性 :

* `deltaX` Integer
* `deltaY` Integer
* `wheelTicksX` Integer
* `wheelTicksY` Integer
* `accelerationRatioX` Integer
* `accelerationRatioY` Integer
* `hasPreciseScrollingDeltas` Boolean
* `canScroll` Boolean

#### `contents.beginFrameSubscription([onlyDirty ,]callback)`

* `onlyDirty` Boolean (可选) - 默认为 `false`
* `callback` Function
  * `frameBuffer` Buffer
  * `dirtyRect` [Rectangle](structures/rectangle.md)

开始订阅 提交 事件和捕获数据帧，当有 提交 事件时，使用 `callback(frameBuffer)` 调用 `callback`.

`frameBuffer` 是一个包含原始像素数据的 `Buffer`,像素数据是按照 32bit BGRA 格式有效存储的，但是实际情况是取决于处理器的字节顺序的(大多数的处理器是存放小端序的，如果是在大端序的处理器上，数据是 32bit ARGB 格式).


#### `contents.endFrameSubscription()`

停止订阅帧提交事件.

#### `contents.startDrag(item)`

* `item` Object
  * `file` String
  * `icon` [NativeImage](native-image.md)

将 `item`设置为当前拖放操作的拖动项， `file`是要拖动的文件的绝对路径， `icon`是拖动时显示在光标下的图像。

#### `contents.savePage(fullPath, saveType, callback)`

* `fullPath` String - 文件的完整路径.
* `saveType` String - 指定保存类型.
  * `HTMLOnly` - 只保存html.
  * `HTMLComplete` - 保存整个 page 内容.
  * `MHTML` - 保存完整的 html 为 MHTML.
* `callback` Function - `function(error) {}`.
  * `error` Error

如果保存界面过程初始化成功，返回 true.

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

win.loadURL('https://github.com')

win.webContents.on('did-finish-load', () => {
  win.webContents.savePage('/tmp/test.html', 'HTMLComplete', (error) => {
    if (!error) console.log('Save page successfully')
  })
})
```

#### `contents.showDefinitionForSelection()` _macOS_
在页面上搜索所选字词时弹出字典。

#### `contents.setSize(options)`
* `options` Object
  * `normal` Object (可选) - 页面的正常大小.   结合 [`disableguestresize`](web-view-tag.md#disableguestresize)属性，可手动重新调整 `<webview>`内容尺寸
    * `width` Integer
    * `height` Integer
设置页面的大小。仅支持`<webview>`访客内容

#### `contents.isOffscreen()`

返回 `Boolean` -是否启用了*offscreen rendering* (离屏渲染)

#### `contents.startPainting()`

如果启用*offscreen rendering* (离屏渲染)但未绘制则开始绘制

#### `contents.stopPainting()`

如果启用和正在绘制*offscreen rendering* (离屏渲染)则停止绘制.

#### `contents.isPainting()`

返回 `Boolean` - 如果启用*offscreen rendering* (离屏渲染)则返回它当前是否正在绘制。

#### `contents.setFrameRate(fps)`

* `fps` Integer

如果启用*offscreen rendering* (离屏渲染)则将帧率设置为1-60之间的值。

#### `contents.getFrameRate()`

返回 `Integer` - 如果*offscreen rendering* (离屏渲染)被启用则返回当前frame的rate(帧率).

#### `contents.invalidate()`
*offscreen rendering* (离屏渲染)被启用后，将通过 'paint'` 事件生成一个新的框架。

### 实例属性

#### `contents.id`
WebContents的唯一ID

#### `contents.session`
这个webContents的Session object ([session](session.md))

#### `contents.hostWebContents`
可能拥有`WebContents`的[`WebContents`](web-contents.md) 实例

#### `contents.devToolsWebContents`
 `WebContents`的开发调试工具栏
**注意:** 用户不应存储此对象，因为当DevTools已关闭时，它可能变为`null`.

#### `contents.debugger`
这里的webContents的[Debugger](debugger.md)实例。
[keyboardevent]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent
