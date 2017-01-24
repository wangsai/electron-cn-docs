# 本文介绍:Cookies类

> 会话(cookie)查询与修改操作类 

进程: [主进程](../glossary.md#main-process).      

```javascript
const {session} = require('electron')
//查询所有Cookie。
session.defaultSession.cookies.get({}, (error, cookies) => {
  console.log(error, cookies)
})
//查询与特定网址相关联的所有Cookie。
session.defaultSession.cookies.get({url: 'http://www.github.com'}, (error, cookies) => {
  console.log(error, cookies)
})
//设置具有给定Cookie数据的Cookie;
//如果存在相同的Cookie,则覆盖。
const cookie = {url: 'http://www.github.com', name: 'dummy_name', value: 'dummy'}
session.defaultSession.cookies.set(cookie, (error) => {
  if (error) console.error(error)
})
```
##实例事件

#### 事件: 'changed'

* `event` Event
* `cookie` [Cookie](structures/cookie.md) - 希望被改变的cookie
* `cause` String - 修改方式,可选以下:
  * `explicit` - 直接改变
  * `overwrite` - 自动擦除并覆盖
  * `expired` - 过期后自动删除
  * `evicted` - 跟随垃圾收集过程销毁
  * `expired-overwrite` - 用已过期的数据覆盖
* `removed` Boolean - `true` 已删除, `false` 其它.
当cookie因增加,编辑,删除,过期等改变而触发.

##实例方法

#### `cookies.get(filter, callback)`

* `filter` Object
  * `url` String (可选) -关联cookie的URL. 不存在则所有网址的Cookie。
  * `name` String (可选) - 按名称过滤Cookie。
  * `domain` String (可选) - 按 `domains`域或子域检索Cookie。
  * `path` String (可选) - 按 `path`路径检索Cookie。
  * `secure` Boolean (可选) -按照其Secure属性排序
  * `session` Boolean (可选) - 排除会话或持久cookie
* `callback` Function
  * `error` Error
  * `cookies` Cookies[]

发送请求以获取所有匹配 `details`的Cookie.
当 `callback(error, cookies)` 完成后, `callback`将被调用
 `cookies`是一个[`cookie`](structures/cookie.md) 对象的数组。

#### `cookies.set(details, callback)`

* `details`对象
  * `url` String - 关联cookie的URL。
  * `name` String(可选) - cookie的名称。如果省略，默认为空。
  * `value` String(可选) - cookie的值。如果省略，默认为空。
  * `domain` String(可选) - Cookie的域。如果省略，默认为空。
  * `path` String(可选) - Cookie的路径。如果省略，默认为空。
  * `secure` Boolean(可选) - Cookie是否应标记为安全。默认为false。
  * `httpOnly` Boolean(可选) - Cookie是否应标记为仅HTTP。默认为false。
  * `expirationDate` Double (可选) - 用秒级定义cookie的过期日期。如果省略，则cookie成为临时会话。
* `callback` Function
  * `error` Error
  
当 `callback(error)`完成后,用 `details`, `callback`设置一个cookie


#### `cookies.remove(url, name, callback)`

* `url` String - 关联cookie的URL。
* `name` String - 要删除的cookie的名称。
* `callback` Function

当 `callback()` 完成后,用 `url` 和 `name`, `callback`删除cookie