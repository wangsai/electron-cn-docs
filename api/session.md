# 本文介绍:session(备注:本文与官译文档中有出入,那边的中文文档多了好多方法,暂时不知道是为什么)

> 管理浏览器 session ，Cookie，缓存，代理设置等。

进程: [主进程](../glossary.md#main-process)         

 `session`模块可以用来创建新的`Session`对象。
你也可以通过使用 [`webContents`](web-contents.md) 的 `session`属性或 `session`模块访问现有页面的 `session`， `webContents` 是[`BrowserWindow`](browser-window.md) 的属性.

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

const ses = win.webContents.session
console.log(ses.getUserAgent())
```

## 方法列表

 `session` 模块有如下方法:

### `session.fromPartition(partition[, options])`

* `partition` String
* `options` Object
  * `cache` Boolean - 是否允许缓存

返回 `Session` - 从字符串 `partition` 返回一个新的 `Session` 实例. 相同 `partition'的 `Session`存在时则返回它,否则将使用 `options`创建一个新的`Session`实例。

如果 `partition`以 `persist：`开头，页面将使用一个持久 session且可用于应用程序中具有相同 `partition`的所有页面。
如果没有 `persist：`前缀，那么页面将使用临时session 。
如果 `partition` 为空，那么将被返回应用程序的默认 session。

要用 `options`创建一个 `Session`， 您必须确保带有 `partition`的 `Session`从来没有被使用过. 
因为没有办法改变现有 `Session` 对象中的 `options`。

## 属性

 `session` 模块具有以下属性：

### `session.defaultSession`
返回应用程序的默认 `Session`对象。

## Session类的介绍与应用

> 获取和设置会话的属性

进程: [主进程](../glossary.md#main-process)         

可以在 `session` 模块中创建一个 `Session` 对象 :

```javascript
const {session} = require('electron')
const ses = session.fromPartition('persist:name')
console.log(ses.getUserAgent())
```

### 实例事件

实例 `Session` 有以下事件:

#### 事件: 'will-download'

* `event` Event
* `item` [DownloadItem](download-item.md)
* `webContents` [WebContents](web-contents.md)

当 Electron 将要从 `webContents` 下载 `item` 时触发.
调用 `event.preventDefault()` 可以取消下载，并且在进程的下个 tick中，这个 `item` 也不可用.
```javascript
const {session} = require('electron')
session.defaultSession.on('will-download', (event, item, webContents) => {
  event.preventDefault()
  require('request')(item.getURL(), (data) => {
    require('fs').writeFileSync('/somewhere', data)
  })
})
```

### 实例方法
实例 `Session` 有以下方法:

#### `ses.getCacheSize(callback)`
* `callback` Function
  * `size` Integer - 缓存大小(字节)

返回 session 的当前缓存大小

#### `ses.clearCache(callback)`
* `callback` Function - 操作完成时调用

清空 session 的 HTTP 缓存.

#### `ses.clearStorageData([options, callback])`

* `options` Object (可选)
  * `origin` String - 应该遵循 `window.location.origin`的格式 `scheme://host:port`。
  * `storages` String[] - 需要清理的 storages 类型, 可以包含 :
    `appcache`, `cookies`, `filesystem`, `indexdb`, `localstorage`,
    `shadercache`, `websql`, `serviceworkers`
  * `quotas` String[] - 需要清理的类型指标, 可以包含:
    `temporary`, `persistent`, `syncable`.
* `callback` Function (可选) - 操作完成时调用.

清除 web storages 的数据.

#### `ses.flushStorageData()`

将没有写入的 DOMStorage 写入磁盘.

#### `ses.setProxy(config, callback)`

* `config` Object
  * `pacScript` String - 与 PAC 文件相关的 URL.
  * `proxyRules` String -  代理使用规则.
  * `proxyBypassRules` String - 指定哪些网址应绕过代理设置的规则。
* `callback` Function - 操作完成时调用.

设置相关代理设置
当 `pacScript` 和 `proxyRules` 一同提供时，将忽略 `proxyRules`，并且使用 `pacScript` 配置 .
`proxyRules` 需要遵循下面的规则:

```
proxyRules = schemeProxies[`;`<schemeProxies>]
schemeProxies = [<urlScheme>`=`]<proxyURIList>
urlScheme = `http` | `https` | `ftp` | `socks`
proxyURIList = <proxyURL>[`,`<proxyURIList>]
proxyURL = [<proxyScheme>`://`]<proxyHost>[`:`<proxyPort>]
```

例子:

* `http=foopy:80;ftp=foopy2` - 为 `http://` URL 使用 HTTP 代理 `foopy:80` , 和为 `ftp://` URL
  HTTP 代理 `foopy2:80` .
* `foopy:80` - 为所有 URL 使用 HTTP 代理 `foopy:80` .
* `foopy:80,bar,direct://` - 为所有 URL 使用 HTTP 代理 `foopy:80` , 如果 `foopy:80` 不可用，则切换使用  `bar`, 再往后就不使用代理了.
* `socks4://foopy` - 为所有 URL 使用 SOCKS v4 代理 `foopy:1080`.
* `http=foopy,socks5://bar.com` - 为所有 URL 使用 HTTP 代理 `foopy`, 如果 `foopy`不可用，则切换到 SOCKS5 代理 `bar.com`.
* `http=foopy,direct://` - 为所有http url 使用 HTTP 代理，如果 `foopy`不可用，则不使用代理.
* `http=foopy;socks=foopy2` -  为所有http url 使用 `foopy` 代理，为所有其他 url 使用 `socks4://foopy2` 代理.

 `proxyBypassRules`是一个以逗号分隔的规则列表，如下所述：

* `[ URL_SCHEME `://` ] HOSTNAME_PATTERN [ `:` <port> ]`
匹配与模式HOSTNAME_PATTERN匹配的所有主机名。
如:`foobar.com`, `*foobar.com`, `*.foobar.com`, `*foobar.com:99`,`https://x.*.y.com:99`

 * ``.` HOSTNAME_SUFFIX_PATTERN [ `:` PORT ]`
匹配特定域后缀。
如:`.google.com`, `.com`, `http://.google.com`

* `[ SCHEME `://` ] IP_LITERAL [ `:` PORT ]`
   匹配IP地址。
   如:`127.0.1`, `[0:0::1]`, `[::1]`, `http://[::1]:99`

*  `IP_LITERAL `/` PREFIX_LENGHT_IN_BITS`
匹配到指定范围之间的IP字面值的任何URL。
IP范围使用CIDR表示法指定。
   如:`192.168.1.1/16`, `fefe:13::abc/33`.

*  `<local>`
匹配本地地址。
 `<local>`的含义是主机是否匹配`127.0.0.1`，`:: 1`，`localhost`之一。

#### `ses.resolveProxy(url, callback)`
* `url` URL
* `callback` Function
  * `proxy` Object
解析 `url` 的代理信息.当请求完成的时候使用 `callback(proxy)` 调用 `callback`.

#### `ses.setDownloadPath(path)`
* `path` String - 下载地址
设置下载保存地址，默认保存地址为各自 app 应用的 `Downloads`目录.

#### `ses.enableNetworkEmulation(options)`

* `options` Object
  * `offline` Boolean - 是否模拟网络故障.
  * `latency` Double (可选) -每毫秒的 RTT。默认为0，禁用延迟调节。
  * `downloadThroughput` Double (可选) -下载速率(以Bps为单位)。默认值为0，禁用下载限制。
  * `uploadThroughput` Double (可选) - 上传速率(以Bps为单位)。默认值为0，禁用上传限制。

通过给定配置的 `session` 来模拟网络.

```javascript
// 模拟 GPRS 连接，使用的 50kbps 流量，500 毫秒的 rtt.
window.webContents.session.enableNetworkEmulation({
  latency: 500,
  downloadThroughput: 6400,
  uploadThroughput: 6400
})

// 模拟网络故障.
window.webContents.session.enableNetworkEmulation({offline: true})
```

#### `ses.disableNetworkEmulation()`
重置为原始网络类型.并 停止所有已经使用 `session` 的活跃模拟网络.

#### `ses.setCertificateVerifyProc(proc)`

* `proc` Function
  * `hostname` String
  * `certificate` [Certificate](structures/certificate.md)
  * `callback` Function
   * `isTrusted` Boolean - 确定证书是否应受信任

为 `session` 设置证书验证过程，每当请求服务器证书验证时，proc将用 `proc(hostname，certificate，callback)`调用。
 `callback(true)` 来接收证书 , `callback(false)` 来拒绝验证证书., `setCertificateVerifyProc(null)` ，则恢复为默认证书验证过程。
 
```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

win.webContents.session.setCertificateVerifyProc((hostname, cert, callback) => {
  callback(hostname === 'github.com')
})
```

#### `ses.setPermissionRequestHandler(handler)`

* `handler` Function
  * `webContents` Object - [WebContents](web-contents.md) 请求许可.
  * `permission` String -枚举了 'media', 'geolocation', 'notifications', 'midiSysex', 'pointerLock', 'fullscreen', 'openExternal'.
  * `callback` Function
    * `permissionGranted` Boolean -允许或禁止许可.
    
为对应 `session` 许可请求设置响应句柄.调用 `callback(true)` 接收许可，调用 `callback(false)` 禁止许可.
```javascript
const {session} = require('electron')
session.fromPartition('some-partition').setPermissionRequestHandler((webContents, permission, callback) => {
  if (webContents.getURL() === 'some-host' && permission === 'notifications') {
    return callback(false) // denied.
  }

  callback(true)
})
```

#### `ses.clearHostResolverCache([callback])`

* `callback` Function (可选) - 操作结束调用.
清除主机解析缓存.

#### `ses.allowNTLMCredentialsForDomains(domains)`

* `domains` String - 按逗号分隔的一个启用了集成身份验证的服务器列表。

动态设置是否始终发送HTTP NTLM或协商身份验证的凭据。
```javascript
const {session} = require('electron')
// consider any url ending with `example.com`, `foobar.com`, `baz`
// for integrated authentication.
session.defaultSession.allowNTLMCredentialsForDomains('*example.com, *foobar.com, *baz')

// consider all urls for integrated authentication.
session.defaultSession.allowNTLMCredentialsForDomains('*')
```

#### `ses.setUserAgent(userAgent[, acceptLanguages])`

* `userAgent` String
* `acceptLanguages` String (可选)

覆盖此会话的 `userAgent`和 `acceptLanguages`。

 `acceptLanguages`是以逗号分隔的语言列表，例如 ``en-US，fr，de，ko，zh-CN，ja``。

这不会影响现有的 `WebContents`，每个WebContents可以使用 `webContents.setUserAgent`来覆盖会话级的userAgent。

#### `ses.getUserAgent()`

返回 `String` - 该会话的 user agent.

#### `ses.getBlobData(identifier, callback)`

* `identifier` String - Valid UUID.
* `callback` Function
  * `result` Buffer - Blob data.

返回 `Blob` - 与`identifier`相关联的blob数据。

#### `ses.createInterruptedDownload(options)`

* `options` Object
  * `path` String - 下载的绝对路径
  * `urlChain` String[] - 完整的下载链接
  * `mimeType` String (可选)
  * `offset` Integer - 下载的开始范围
  * `length` Integer - 下载大小
  * `lastModified` String - Last-Modified标头值
  * `eTag` String - ETag头值。
  * `startTime` Double (可选) - 秒级的下载时间

允许从上一个Session重新开始 `cancelled`或 `interrupted`下载.
API将生成一个可以用[will-download](＃event-will-download)进行访问的[DownloadItem](download-item.md)事件。
 [DownloadItem](download-item.md)不会有任何与它相关的 `WebContents`，初始状态将被'中断'。
 只有当在[DownloadItem](download-item.md)上调用 `resume` API时，下载将开始。

#### `ses.clearAuthCache(options[, callback])`

* `options` ([RemovePassword](structures/remove-password.md) | [RemoveClientCertificate](structures/remove-client-certificate.md))
* `callback` Function (可选) - 操作完成时调用

清除会话的HTTP身份验证缓存。

###实例属性
以下属性可用于`Session`的实例：

#### `ses.cookies`
此会话的Cookie对象

#### `ses.webRequest`
此会话的WebRequest对象。

#### `ses.protocol`
此会话的Protocol对象([protocol](protocol.md)模块的实例)。
```javascript
const {app, session} = require('electron')
const path = require('path')

app.on('ready', function () {
  const protocol = session.fromPartition('some-partition').protocol
  protocol.registerFileProtocol('atom', function (request, callback) {
    var url = request.url.substr(7)
    callback({path: path.normalize(`${__dirname}/${url}`)})
  }, function (error) {
    if (error) console.error('Failed to register protocol')
  })
})
```
