# 本文介绍:支持的 Chrome 命令行开关

>下文列出了Chrome浏览器和Electron支持的命令行开关. 你也可以在[app][app]模块的[ready][ready]事件发出之前使用
[app.commandLine.appendSwitch][append-switch] 来添加它们到你应用的main脚本里面:

```javascript
const {app} = require（'electron'）
app.commandLine.appendSwitch（'remote-debugging-port'，'8315'）
app.commandLine.appendSwitch（'host-rules'，'MAP * 127.0.0.1'）

app.on（'ready'，（）=> {
  //您的代码在这里
}）
```

## --ignore-connections-limit =`domains`

忽略由`，`分隔的`domains`列表的连接数限制。

## --disable-http-cache

为HTTP请求禁用磁盘缓存。

## --disable-http2

禁用HTTP / 2和SPDY / 3.1协议。

## --debug =`port`和--debug-brk =`port`

调试相关标志，请参见[调试主进程] [debugging-main-process]指南了解详细信息。

## --remote-debugging-port =`port`

通过HTTP在指定的端口上启用远程调试。

## --js-flags =`flags'
指定引擎过渡到 JS 引擎. 
在启动Electron时，如果你想在主进程中激活 `flags` ，它将被转换.
```bash
$ electron --js-flags =“ - harmony_proxies --harmony_collections”your-app
```

请参阅[Node文档] [node-cli]或在终端中运行`node --help`以获取可用标志列表。
另外，运行`node --v8-options`可以看到特定引用Node的V8 JavaScript引擎的标志列表。

## --proxy-server =`address：port`

使用指定的代理服务器，该服务器覆盖系统设置。这个开关
仅影响具有HTTP协议的请求，包括HTTPS和WebSocket
要求。还值得注意的是，并非所有代理服务器都支持HTTPS和
WebSocket请求。

## --proxy-bypass-list =`hosts`

指示Electron绕过给定分号分隔的代理服务器
主机列表。此标志只有在串联使用时才有效果 `--proxy-server`。

例如：

```javascript
const {app} = require（'electron'）
app.commandLine.appendSwitch（'proxy-bypass-list'，'<local>; *。google.com; * foo.com; 1.2.3.4：5678'）
```

将使用代理服务器为所有主机除本地地址（`localhost`，
`127.0.0.1`等），`google.com`子域，包含后缀的主机
`foo.com`和任何在`1.2.3.4：5678`。

## --proxy-pac-url =`url`

在指定的`url`使用PAC脚本。

## --no-proxy-server

不要使用代理服务器并始终进行直接连接。覆盖任何其他
代理服务器标志。

## --host-rules =`rules`

以逗号分隔的“rules”列表，用于控制主机名的映射方式。

例如：

* `MAP * 127.0.0.1`强制所有主机名映射到127.0.0.1
* `MAP * .google.com proxy`强制将所有google.com子域解析为“代理”。
* `MAP test.com [:: 1]：77`强制“test.com”解决IPv6环回。将
  也强制端口的所得套接字地址为77。
* `MAP * baz，EXCLUDE www.google.com`将所有内容重新映射到“baz”，除了“www.google.com”。

这些映射适用于网络请求（TCP连接）中的端点主机
和主机解析器在一个直接连接，和`CONNECT`在HTTP代理
连接，以及SOCKS代理连接中的端点主机）。

## --host-resolver-rules =`rules`

像`--host-rules`，但这些规则只适用于主机解析器。

## --auth-server-whitelist =`url`

启用集成身份验证的服务器的逗号分隔列表。

例如：

```
--auth-server-whitelist ='* example.com，* foobar.com，* baz'
```

那么以“example.com”，“foobar.com”，“baz”结尾的任何`url'将被考虑
用于集成认证。如果没有`*`前缀，url必须完全匹配。

## --auth-negotiate-delegate-whitelist =`url`

需要委派用户凭据的服务器的逗号分隔列表。
如果没有`*`前缀，url必须完全匹配。

## --ignore-certificate-errors

忽略与证书相关的错误。

## --ppapi-flash-path =`path`

设置ppapi flash插件的`path'。

## --ppapi-flash-version =`version`

设置ppapi flash插件的版本。

## --log-net-log =`path`

允许保存网络日志事件，并将其写入“path”。

## --ssl-version-fallback-min =`version`

设置TLS的最低SSL / TLS版本（`tls1`，`tls1.1`或`tls1.2`）
回退会接受。

## --cipher-suite-blacklist =`cipher_suites`

指定要禁用的SSL密码套件的逗号分隔列表。

## --disable-renderer-backgrounding

防止 Chromium 降低隐藏的渲染进程优先级.

这个标志对所有渲染进程全局有效，如果你只想在一个窗口中禁止使用，你可以采用 hack 方法[playing silent audio][play-silent-audio].

## --enable-logging

打印 Chromium 信息输出到控制台.

如果在用户应用加载完成之前解析`app.commandLine.appendSwitch` ，这个开关将实效，但是你可以设置 `ELECTRON_ENABLE_LOGGING` 环境变量来达到相同的效果.