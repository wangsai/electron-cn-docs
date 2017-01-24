# 本文介绍: `<webview>` 标签
>在隔离的框架和过程中显示外部Web内容。

 `webview` 标签可将 `guest` 内容（例如外部网页）嵌入到应用中. 

与 `iframe` 不同, `webview` 和你的应用运行的是不同的进程. 它不拥有渲染进程的权限，为确保应用不受嵌入内容的影响,它和嵌入内容之间的交互全部都是异步的.


## 使用例子

将`webview`嵌入的应用中.下文是个包括了src和style样式的范例:

```html
<webview id="foo" src="https://www.github.com/" style="display:inline-flex; width:640px; height:480px"></webview>
```

如果你要控制 `guest` 内容,你可以使用下文的 `webview`事件监听或方法进行响应
这是个loading条示例,它在页面开始加载时进行了监听,显示了`loading`提示,加载完成后则停止继续监听:

```html
<script>
  onload = () => {
    const webview = document.getElementById('foo')
    const indicator = document.querySelector('.indicator')

    const loadstart = () => {
      indicator.innerText = 'loading...'
    }

    const loadstop = () => {
      indicator.innerText = ''
    }

    webview.addEventListener('did-start-loading', loadstart)
    webview.addEventListener('did-stop-loading', loadstop)
  }
</script>
```

## CSS样式的两个注意点

 第一点: 不要轻易覆盖 `webview`的`display:flex`
 
 `webview`默认使用 `display:flex`来确保内部元素填充宽高。 除非你为内联布局指定了 `display:inline-flex`,否则别覆盖默认的 `display:flex` CSS属性。
 
第二点:隐藏webview的正确方式

不要用 `hidden`属性或使用 `display:none`来隐藏  `webview`,则会导致内部 `browserplugin`出现行为.同事,当webview被取消隐藏时，web页面实际上是进行重新加载而不是如`display:block`样式呈现的可见.

建议的方式是将 `webview`的宽高设置为零，并通过`flex`将元素缩小到0px尺寸,示例:

```html
<style>
  webview {
    display:inline-flex;
    width:640px;
    height:480px;
  }
  webview.hide {
    flex: 0 1;
    width: 0px;
    height: 0px;
  }
</style>
```

##标签属性

`webview`标签具有以下属性：

### `src`

```html
<webview src="https://www.github.com/"></webview>
```

加载到 `webview` 的 `src` 还支持data协议,比如 `data:text/plain,Hello, world!`.

### `autosize`

```html
<webview src="https://www.github.com/" autosize="on" minwidth="576" minheight="432"></webview>
```

 `on` 表示 `webview` 在 `minwidth`, `minheight`, `maxwidth`, 和 `maxheight`这些值范围内自适应尺寸.
 
### `nodeintegration`
```html
<webview src="http://www.google.com/" nodeintegration></webview>
```

`on`表示 `webview`中的guest page 将整合 Node，并且拥有可以使用如 `require` 和 `process`等系统底层资源

### `plugins`
```html
<webview src="https://www.github.com/" plugins></webview>
```

`on`表示`webview`中的guest page 可以使用浏览器插件。

### `preload`

```html
<webview src="https://www.github.com/" preload="./test.js"></webview>
```
 预载脚本,其它脚本运行之前预先加载指定脚本.
script的URL协议必须是 `file:` 或 `asar:`,因为它是由 `require` 加载到 guest page. 
无论页面是否集成Node，此脚本都可以访问所有Node API，但Node插入的全局对象会在此脚本完成执行后被删除。

### `httpreferrer`

```html
<webview src="https://www.github.com/" httpreferrer="http://cheng.guru"></webview>
```
设置guest page的引荐来源页


### `useragent`

```html
<webview src="https://www.github.com/" useragent="Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko"></webview>
```
预先设置guest page 的 `useragent`。
页面被加载后,可使用 `setUserAgent` 方法改变 `useragent`。

### `disablewebsecurity`

```html
<webview src="https://www.github.com/" disablewebsecurity></webview>
```

 `on` 则允许guest page忽略web安全设置。
 
### `partition`

```html
<webview src="https://github.com" partition="persist:github"></webview>
<webview src="http://electron.atom.io" partition="electron"></webview>
```

设置页面的session;
如果 `partition`以 `persist：`开头，页面将使用一个持久 session且可用于应用程序中具有相同 `partition`的所有页面。
如果没有 `persist：`前缀，那么页面将使用临时session 。
如果 `partition` 为空，那么将被返回应用程序的默认 session。

这个值只能在在第一个渲染进程之前设置修改，之后修改的话会无效并且抛出一个DOM异常.

### `allowpopups`

```html
<webview src="https://www.github.com/" allowpopups></webview>
```
 `on` 则允许咋新窗口打开 guest page。

### `webpreferences`

```html
<webview src="https://github.com" webpreferences="allowDisplayingInsecureContent, javascript=no"></webview>
```
在webview上 进行设置的 `web首选项` 的字符串列表，用`,`分隔。
支持的首选项字符串的完整列表,详见[BrowserWindow](browser-window.md#new-browserwindowoptions).
字符串格式和`window.open`中的features字符串一样,每个名称都默认为`true',允许使用 `=` 设置为另一个值。
其中,特殊值 `yes'和 `1`被解释为 `true`，而 `no`和 `0`被解释为`false`。

### `blinkfeatures`

```html
<webview src="https://www.github.com/" blinkfeatures="PreciseMemoryInfo, CSSVariables"></webview>
```
要启用的 `特性` 的字符串列表，用 `,`分隔。
完整列表,请详[RuntimeEnabledFeatures.in][blink-feature-string].

### `disableblinkfeatures`

```html
<webview src="https://www.github.com/" disableblinkfeatures="PreciseMemoryInfo, CSSVariables"></webview>
```
要禁用的 `特性` 的字符串列表，用 `,`分隔。
完整列表,请详[RuntimeEnabledFeatures.in][blink-feature-string].

### `guestinstance`

```html
<webview src="https://www.github.com/" guestinstance="3"></webview>
```

将webview连接到实例标识符为指定值(如上文的 `3`)的webContents。
webview首次加载的时候，将创建一个guestinstance等于其实例标识符的webContents。
现有的webview会触发`destroy`，然后在加载新的URL时创建一个新的webContents。


### `disableguestresize`

```html
<webview src="https://www.github.com/" disableguestresize></webview>
```

调整webview元素尺寸时禁止其内容大小也跟随调整.通常和[`webContents.setSize`](web-contents.md#contentssetsizeoptions)结合运用,进行调整webview尺寸.

```javascript
const {webContents} = require('electron')

//假设`win`是 指向一个包含`disableguestresize`属性的`webview>`的`BrowserWindow`实例。

win.on('resize', () => {
  const [width, height] = win.getContentSize()
  for (let wc of webContents.getAllWebContents()) {
//检查`wc'是否属于win 中的webview。
    if (wc.hostWebContents &&
        wc.hostWebContents.id === win.webContents.id) {
      wc.setSize({
        normal: {
          width: width,
          height: height
        }
      })
    }
  }
})
```

## 方法
 `webview` 标签有以下方法：
 **注意：** webview元素加载后才可以使用下列使用方法
```javascript
const webview = document.getElementById('foo')
webview.addEventListener('dom-ready', () => {
  webview.openDevTools()
})
```

### `<webview>.loadURL(url[, options])`

* `url` URL
* `options` Object (可选)
  * `httpReferrer` String (可选) - 来源网址
  * `userAgent` String (可选) - 发起请求的userAgent.
  * `extraHeaders` String (可选) - 用`\ n`分隔的额外头
  * `postData` ([UploadRawData](structures/upload-raw-data.md) | [UploadFile](structures/upload-file.md) | [UploadFileSystem](structures/upload-file-system.md) | [UploadBlob](structures/upload-blob.md))[] - (可选)
加载 `url`到这个webview中,  `url` 必须指定协议如 `http://` 或`file://`.

### `<webview>.getURL()`

返回 `String` - 当前 guest page 的链接地址

### `<webview>.getTitle()`

返回 `String` - 当前 guest page 的标题

### `<webview>.isLoading()`

返回 `Boolean` - 当前 guest page 是否仍在加载资源

### `<webview>.isWaitingForResponse()`

返回 `Boolean` - 当前 guest page 是否等待来自页面的主资源的第一响应。

### `<webview>.stop()`
停止任何待处理的导航。

### `<webview>.reload()`
重新加载当前 guest page.

### `<webview>.reloadIgnoringCache()`
忽略缓存的重新加载当前 guest page。

### `<webview>.canGoBack()`

返回 `Boolean` - 当前 guest page 是否可以可后退至上个网页。

### `<webview>.canGoForward()`

返回 `Boolean` - 当前 guest page 是否可以前进到下一个网页。

### `<webview>.canGoToOffset(offset)`

* `offset` Integer

返回 `Boolean` - 当前 guest page 是否可以转到 `offset`这个页面。

### `<webview>.clearHistory()`
清除历史记录。

### `<webview>.goBack()`
使 guest page 后退往上一页

### `<webview>.goForward()`
使 guest page 前进往下一页

### `<webview>.goToIndex(index)`
* `index` Integer
将浏览器导航到指定索引 `index` 的网页。

### `<webview>.goToOffset(offset)`
* `offset` Integer
从 当前页 到指定 `offset`的偏移量。

### `<webview>.isCrashed()`

返回 `Boolean` -  渲染器进程是否崩溃。

### `<webview>.setUserAgent(userAgent)`
* `userAgent` String

重定义当前 guest page 的 UserAgent.

### `<webview>.getUserAgent()`

返回 `String` - 当前 guest page 的 UserAgent.

### `<webview>.insertCSS(css)`

* `css` String

将CSS插入到当前 guest page.

### `<webview>.executeJavaScript(code, userGesture, callback)`

* `code` String
* `userGesture` Boolean - 是否支持用户手势 默认为 `false`.
* `callback` Function (可选) - script脚本执行后调用
  * `result` Any
返回 `Promise` - 是否可解析执行代码 `code`
作用:在页面中评估(eval) `code` .
将 `userGesture` 设置为 `true`,可对去除 某些HTML API 只能通过 手势 进行调用 的限制,比如 `requestFullScreen`。

### `<webview>.openDevTools()`

打开guest page的开发者工具栏

### `<webview>.closeDevTools()`

关闭guest page的开发者工具栏

### `<webview>.isDevToolsOpened()`

返回 `Boolean` - guest page的开发者工具栏是否已打开

### `<webview>.isDevToolsFocused()`

返回 `Boolean` - guest page的开发者工具栏是否已聚焦

### `<webview>.inspectElement(x, y)`
* `x` Integer
* `y` Integer
在 guest page的( `x`,  `y`) 这个位置开始检查元素


### `<webview>.inspectServiceWorker()`
在这个guest page中为 service worker 上下文打开开发者工具栏.

### `<webview>.setAudioMuted(muted)`
* `muted` Boolean
使当前 guest page 的音频静音

### `<webview>.isAudioMuted()`
返回 `Boolean` - 此 guest page 是否已静音。

### `<webview>.undo()`
在网页中执行撤销( `undo`)。

### `<webview>.redo()`
在网页中执行重做( `redo`)。

### `<webview>.cut()`
在网页中执行剪切( `cut`)。

### `<webview>.copy()`
在网页中执行复制( `copy`)。

### `<webview>.paste()`
在网页中执行粘贴( `paste`)。

### `<webview>.pasteAndMatchStyle()`
在网页中执行粘贴并清除样式( `pasteAndMatchStyle`)。

### `<webview>.delete()`
在网页中执行删除( `delete`)。

### `<webview>.selectAll()`
在网页中执行全选( `selectAll`)。

### `<webview>.unselect()`
在网页中执行取消选择( `unselect`)。

### `<webview>.replace(text)`
* `text` String
在网页中执行替换( `replace`)文本( `text`)。

### `<webview>.replaceMisspelling(text)`
* `text` String
在网页中执行校正( `replaceMisspelling`)错别字( `text`)。

### `<webview>.insertText(text)`
* `text` String
插入`text`到焦点元素。

### `<webview>.findInPage(text[, options])`
更多细节,详见 `webContents.findInPage(text[, options])`

### `<webview>.stopFindInPage(action)`
* `action` String - 指派[`<webview>.findInPage`](webview-tag.md#webviewtagfindinpage)请求结束时要执行的操作。
  * `clearSelection` - 清除选择。
  * `keepSelection` - 转为没有任何action等等的普通选择
  * `activateSelection` - 聚焦并单击该选择
使用给定的 `action` 来为 `webview` 停止任何 `findInPage` 请求.

### `<webview>.print([options])`

打印 `webview`'页面. 
更多细节,详见 `webContents.print([options])`.

### `<webview>.printToPDF(options, callback)`

打印F `webview`'页面为PDF
更多细节,详见 `webContents.printToPDF(options, callback)`.

### `<webview>.capturePage([rect, ]callback)`
截图 `rect`区域
更多细节,详见 `webContents.capturePage([rect, ]callback)`.

### `<webview>.send(channel[, arg1][, arg2][, ...])`

* `channel` String
* `arg` (可选)

通过 `channel` 向主进程发送异步消息，也可以发送任意参数.参数会被JSON序列化，之后就不会包含函数或原型链.
渲染器进程通过使用 `ipcRenderer` 模块来监听 `channel`，从而处理消息,
更多细节,详见[webContents.send](web-contents.md#webcontentssendchannel-args)

### `<webview>.sendInputEvent(event)`
* `event` Object

向页面发送输入 `event` .
更多细节,详见[webContents.sendInputEvent](web-contents.md#webcontentssendinputeventevent)

### `<webview>.setZoomFactor(factor)`
* `factor` Number - 缩放系数

设置页面的缩放系数。
注意:缩放系数是百分制的，如3.0=300％。


### `<webview>.setZoomLevel(level)`
* `level` Number - 缩放级别
将缩放级别更改为指定级别。
原始大小为0，每个增量表示放大或缩小20％,默认限制为原始大小的300％至50％。

### `<webview>.showDefinitionForSelection()` _macOS_

Shows pop-up dictionary that searches the selected word on the page.

### `<webview>.getWebContents()`

返回 [`WebContents`](web-contents.md) - The web contents associated with this `webview`.

## DOM时间列表
以下DOM事件可用于 `webview`：
### Event: 'load-commit'
返回:
* `url` String
* `isMainFrame` Boolean

加载完成触发. 
包含当前文档导航和副框架的文档加载，但是不包含异步资源加载.

### Event: 'did-finish-load'
导航完成时(即选项卡中的微调框(spinner)已停止旋转，并且已分派 `onload`事件)触发。

### Event: 'did-fail-load'
返回:
* `errorCode` Integer
* `errorDescription` String
* `validatedURL` String
* `isMainFrame` Boolean

类似 `done-finish-load`，不过它是当加载失败或者是被取消(比如调用了 `window.stop()`)时触发,。
[这里](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)列出了所有错误代码及其含义.
由于重定向响应也会触发`errorCode` -3,也许您会希望忽略该错误。
 

### Event: 'did-frame-finish-load'
返回:
* `isMainFrame` Boolean
当一个 frame窗口 完成导航时触发

### Event: 'did-start-loading'
 选项卡中的微调框(spinner)开始旋转(loading)时触发

### Event: 'did-stop-loading'
 选项卡中的微调框(spinner)结束了旋转(loading)时触发

### Event: 'did-get-response-details'

返回:

* `status` Boolean
* `newURL` String
* `originalURL` String
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object
* `resourceType` String

 在所请求资源的相关详细信息可用时触发
 `status`标识了使用套接字连接( socket connection)来下载资源。

### Event: 'did-get-redirect-request'
返回:
* `oldURL` String
* `newURL` String
* `isMainFrame` Boolean

 在请求资源时接收到重定向时触发

### Event: 'dom-ready'

 当文档dom加载完成时触发

### Event: 'page-title-updated'
返回:

* `title` String
* `explicitSet` Boolean

 当文档改变标题时触发
当异步加载文档标题时,`explicitSet`为 `false`.

### Event: 'page-favicon-updated'
返回:
* `favicons` String[] - URL数组

 网页接收到(图标的url)favicon url时触发

### Event: 'enter-html-full-screen'
 通过HTML API进入全屏时触发

### Event: 'leave-html-full-screen'
 通过HTML API退出全屏时触发
### Event: 'console-message'
返回:
* `level` Integer
* `message` String
* `line` Integer
* `sourceId` String
 控制台打印消息时触发
以下是将监听控制台并输出日志消息的示例:
```javascript
const webview = document.getElementById('foo')
webview.addEventListener('console-message', (e) => {
  console.log('Guest page logged a message:', e.message)
})
```

### Event: 'found-in-page'

返回:

* `result` Object
  * `requestId` Integer
  * `activeMatchOrdinal` Integer - 活动匹配位置
  * `matches` Integer -匹配数
  * `selectionArea` Object -第一个匹配区域的坐标。
 当[`webview.findInPage`](webview-tag.md#webviewtagfindinpage) 进行页内查找并且找到可用值触发

```javascript
const webview = document.getElementById('foo')
webview.addEventListener('found-in-page', (e) => {
  webview.stopFindInPage('keepSelection')
})

const requestId = webview.findInPage('test')
console.log(requestId)
```

### Event: 'new-window'
返回:
* `url` String
* `frameName` String
* `disposition` String - 可为 `default`, `foreground-tab`, `background-tab`, `new-window`, `save-to-disk` 和 `other`.
* `options` Object - 创建新的 `BrowserWindow`时使用的参数.

当页面通过 `window.open`或外部链接(如 `<a target='_blank'>`)来 请求 `url`打开一个新窗口(默认创建一个新的 `BrowserWindow`)时触发。
使用系统默认浏览器打开链接的示例:
```javascript
const {shell} = require('electron')
const webview = document.getElementById('foo')

webview.addEventListener('new-window', (e) => {
  const protocol = require('url').parse(e.url).protocol
  if (protocol === 'http:' || protocol === 'https:') {
    shell.openExternal(e.url)
  }
})
```

### Event: 'will-navigate'
返回:
* `url` String
 用户或页面想要开始导航(比如 `window.location`对象更改或用户单击页面中的链接时)时触发。
 `<webview>.loadURL`和 `<webview>.back`之类的API以编程方式启动导航时，该事件不会触发。
页内跳转如 点击了锚链接 或 更新 `window.location.hash`, 该事件也不会触发,不您可以使用 `did-navigate-in-page`事件达到目的。

需要阻止导航,请调用 `event.preventDefault()`。

### Event: 'did-navigate'
返回:
* `url` String

 导航完成时触发.

类似'will-navigate',页内跳转如 点击了锚链接 或 更新 `window.location.hash`, 该事件也不会触发,不过您可以使用 `did-navigate-in-page`事件达到目的。

### Event: 'did-navigate-in-page'
返回:
* `isMainFrame` Boolean
* `url` String

 页内跳转时触发(虽然页面的url已改变但它不会跳出界面,如点击了锚链接 或 DOM的`hashchange`事件被触发时)

### Event: 'close'
 在窗口要关闭的时候触发

关闭时客户端将“webview”导航到“about：blank”的例子:
```javascript
const webview = document.getElementById('foo')
webview.addEventListener('close', () => {
  webview.src = 'about:blank'
})
```

### Event: 'ipc-message'
返回:
* `channel` String
* `args` Array
 当guest page发送异步消息至它的所在的嵌入页时触发
您可以通过 `sendToHost`方法和 `ipc-message`事件在guest page与它的所在的嵌入页之间轻松地进行通信：
```javascript
// guest page的所在嵌入页中
const webview = document.getElementById('foo')
webview.addEventListener('ipc-message', (event) => {
  console.log(event.channel)
  // Prints "pong"
})
webview.send('ping')
```
```javascript
// guest page 中
const {ipcRenderer} = require('electron')
ipcRenderer.on('ping', () => {
  ipcRenderer.sendToHost('pong')
})
```

### Event: 'crashed'
 渲染进程崩溃的时候触发

### Event: 'gpu-crashed'
 gpu进程崩溃时候触发

### Event: 'plugin-crashed'
返回:
* `name` String
* `version` String
 插件进程崩溃时候触发

### Event: 'destroyed'
 `webContents`被销毁后触发

### Event: 'media-started-playing'
 媒体开始播放触发

### Event: 'media-paused'
 媒体暂停或播放完毕时触发

### Event: 'did-change-theme-color'
返回:
* `themeColor` String
 页面的主题颜色被更改时触发
通常是因为使用了元标记如theme-color引起:
```html
<meta name='theme-color' content='#ff0000'>
```

### Event: 'update-target-url'
返回:
* `url` String
 鼠标移动到链接或键盘将焦点移动到链接时触发

#### 事件: 'devtools-opened'

 开发调试工具打开时触发

#### 事件: 'devtools-closed'

 开发调试工具关闭时触发

#### 事件: 'devtools-focused'

 开发调试工具获得焦点或当前已打开后触发

[blink-feature-string]: https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.in
