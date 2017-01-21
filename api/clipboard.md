# 本文介绍:clipboard类
> 复制与粘贴操作类
进程: [主进程](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process)

复制字符串例子:
```javascript
const {clipboard} = require('electron')
clipboard.writeText('Example String')
```

在 X Window 系统上, 有一个可选的 clipboard. 你可以为每个方法使用 `selection` 来控制它:

```javascript
const {clipboard} = require('electron')
clipboard.writeText('Example String', 'selection')
console.log(clipboard.readText('selection'))
```

## 方法
 `clipboard` 模块有以下方法:
 **注意:** 测试 APIs 已经标明，并且在将来会被删除 .

### `clipboard.readText([type])`
* `type` String (可选)
 **以纯文本形式从 clipboard 返回内容**
 
### `clipboard.writeText(text[, type])`
* `text` String
* `type` String (可选)
 **以纯文本形式向 clipboard 添加内容**

### `clipboard.readHTML([type])`
* `type` String (可选)
 **返回 clipboard 中的标记内容**

### `clipboard.writeHTML(markup[, type])`
* `markup` String
* `type` String (可选)
 **向 clipboard 添加 `markup` 内容**

### `clipboard.readImage([type])`
* `type` String (可选)
 **从 clipboard 中返回 [NativeImage](native-image.md) 内容**

### `clipboard.writeImage(image[, type])`
* `image` [NativeImage](native-image.md)
* `type` String (可选)
 **向 clipboard 中写入 `image` **

### `clipboard.readRTF([type])`
* `type` String (可选)
 **从 clipboard 中返回 RTF 内容**

### `clipboard.writeRTF(text[, type])`
* `text` String
* `type` String (可选)
 **向 clipboard 中写入 RTF 格式的 `text`**

### `clipboard.readBookmark()` _macOS_ _Windows_
返回 `Object`:
* `title` String
* `url` String
 **返回一个包含表示书签的 `title` 和 `url` 键的对象**
 如果书签不可用, `title'和 `url`值将是空字符串


### `clipboard.writeBookmark(title, url[, type])` _macOS_ _Windows_
* `title` String
* `url` String
* `type` String (可选)
 **把 `title` 和 `url` 当做书签写入剪贴板**
 **注意:** 大部分WIN程序都不支持直接复制或粘贴书签,你可以使用 `clipboard.write`来写入.
```js
clipboard.write({
  text: 'http://electron.atom.io',
  bookmark: 'Electron Homepage'
})
```

### `clipboard.readFindText()` _macOS_

返回 `String` -  **从粘贴板中查找文本**
在渲染进程中使用IPC调用该方法进行查找时,每当程序被激活,缓存文本都将被重读.

### `clipboard.writeFindText(text)` _macOS_
* `text` String
在渲染进程中使用IPC调用该方法且以文本形式查找粘贴板.

### `clipboard.clear([type])`
* `type` String (可选)
 **清空 clipboard 内容**

### `clipboard.availableFormats([type])`
* `type` String (可选)
返回  `String[]` 
 **clipboard 支持的格式数组**

### `clipboard.has(data[, type])` _Experimental_
* `data` String
* `type` String (可选)
返回  `Boolean` 
**clipboard 是否支持指定 `data` 的格式**

```javascript
const {clipboard} = require('electron')
console.log(clipboard.has('<p>selection</p>'))
```

### `clipboard.read(data[, type])` _Experimental_
* `data` String
* `type` String (可选)
 **字符串形式读取 clipboard 的 `data`**


### `clipboard.write(data[, type])`
* `data` Object
  * `text` String (可选)
  * `html` String (可选)
  * `image` [NativeImage](native-image.md) (可选)
  * `rtf` String (可选)
  * `bookmark` String (可选) -  `text` 里url链接的标题.
* `type` String (可选)
 **向 clipboard 写入 `data`**
```javascript
const {clipboard} = require('electron')
clipboard.write({text: 'test', html: '<b>test</b>'})
```

