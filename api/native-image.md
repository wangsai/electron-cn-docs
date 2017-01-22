# 本文介绍:NativeImage图标创建与应用

>使用PNG or JPG 图片创建桌面小图标tray, dock图标或当前应用图片

进程: [主进程](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process)
在 Electron 中, 对所有创建 images 的 api 来说, 你可以使用文件路径或 `NativeImage` 实例. 如果使用 `null` ，将创建一个空的image 对象.
例如, 当创建一个 tray 或设置窗口的图标时候，你可以使用一个字符串的图片路径 :
```javascript
const {BrowserWindow, Tray} = require('electron')

const appIcon = new Tray('/Users/somebody/images/icon.png')
let win = new BrowserWindow({icon: '/Users/somebody/images/window.png'})
console.log(appIcon, win)
```

或者从剪切板中读取图片，它返回的是 `NativeImage`:

```javascript
const {clipboard, Tray} = require('electron')
const image = clipboard.readImage()
const appIcon = new Tray(image)
console.log(appIcon)
```

## 支持的格式

当前支持 `PNG` 和 `JPEG` 图片格式. 推荐 `PNG` ，因为它支持透明和无损压缩.
在 Windows, 你也可以使用 `ICO` 图标的格式.
* 常用小图标尺寸
 * 16x16 (100% DPI scale)
 * 20x20 (125% DPI scale)
 * 24x24 (150% DPI scale)
 * 32x32 (200% DPI scale)
* 常用大图标尺寸
 * 32x32 (100% DPI scale)
 * 40x40 (125% DPI scale)
 * 48x48 (150% DPI scale)
 * 64x64 (200% DPI scale)
* 256x256

[这篇文章][icons] 中的 *尺寸要求*有详细说明
[icons]:https://msdn.microsoft.com/en-us/library/windows/desktop/dn742485(v=vs.85).aspx

## 高分辨率图片
如果平台支持 high-DPI，你可以在图片基础路径后面添加 `@2x` ，可以标识它为高分辨率的图片.
例如，如果 `icon.png` 是一个普通图片并且拥有标准分辨率，然后 `icon@2x.png`将被当作高分辨率的图片处理，它将拥有双倍 DPI 密度.
如果想同时支持展示不同分辨率的图片，你可以将拥有不同size 的图片放在同一个文件夹下，不用 DPI 后缀.例如 :

```text
images/
├── icon.png
├── icon@2x.png
└── icon@3x.png
```


```javascript
const {Tray} = require('electron')
let appIcon = new Tray('/Users/somebody/images/icon.png')
console.log(appIcon)
```

也支持下面这些 DPI 后缀:

* `@1x`
* `@1.25x`
* `@1.33x`
* `@1.4x`
* `@1.5x`
* `@1.8x`
* `@2x`
* `@2.5x`
* `@3x`
* `@4x`
* `@5x`

## Template Image
模板图片由黑色和透明色(或alpha 通道)组成.
模板图片不旨在用作独立图片，它通常与其他内容混合以创建期望的最终效果。
最常见的情况是当做菜单栏图标，因此它可以适应亮和暗色菜单栏。
 **注意：** 仅在macOS上支持模板图片。
如果希望标记为模板图片，其文件名应以单词“Template”结尾。例如：
* `xxxTemplate.png`
* `xxxTemplate@2x.png`

## 方法

### `nativeImage.createEmpty()`
返回 `NativeImage`
创建一个空的 `NativeImage` 实例.

### `nativeImage.createFromPath(path)`

* `path` String

返回 `NativeImage`
从位于 `path`的文件创建一个新的 `NativeImage`实例。如果 `path`不存在，无法读取或不是有效图像，此方法将返回空图像。
```javascript
const nativeImage = require('electron').nativeImage
let image = nativeImage.createFromPath('/Users/somebody/images/icon.png')
console.log(image)
```

### `nativeImage.createFromBuffer(buffer[, options])`

* `buffer` [Buffer][buffer]
* `options` Object (可选)
  * `width` Integer (可选) - 对于位图缓冲区(bitmap buffers)是必需的。
  * `height` Integer (可选) - 对于位图缓冲区(bitmap buffers)是必需的。
  * `scaleFactor` Double (可选) - 默认1.0.

返回 `NativeImage`

从 `buffer`中创建一个 `NativeImage` 实例

### `nativeImage.createFromDataURL(dataURL)`

* `dataURL` String
从 `dataURL`中创建一个 `NativeImage` 实例

#### `image.toPNG()`

返回 `Buffer` - [Buffer][buffer]，其包含了图片的 `PNG` 编码数据.

#### `image.toJPEG(quality)`

* `quality` Integer (**必须**) - 质量可选 0 - 100.
返回 `Buffer` - [Buffer][buffer]，其包含了图片的 `JPEG` 编码数据.

#### `image.toBitmap()`

返回 `Buffer` - [Buffer][buffer]， 其包含了图片的原始位图像素数据的副本。

#### `image.toDataURL()`

返回 `String` - 返回图片数据的 URL.

#### `image.getBitmap()`

返回 `Buffer` -  [Buffer][buffer] 其包含了图片的原始位图像素数据。

 `getBitmap()`和 `toBitmap()`之间的区别是， `getBitmap()` 不会复制位图数据，
 所以你必须在当前事件中使用立即返回的Buffer，否则数据可能会被改变，销毁。

#### `image.getNativeHandle()` _macOS_

返回一个保存了 c 指针的 [Buffer][buffer] 来潜在处理原始图像.在macOS, 将会返回一个 `NSImage` 指针实例.
注意那返回的指针是潜在原始图像的弱指针，而不是一个复制，你_必须_ 确保与 `nativeImage` 的关联不间断 .

#### `image.isEmpty()`
返回 `Boolean` -  图片是否为空.

#### `image.getSize()`
返回 `Object`:
* `width` Integer
* `height` Integer
返回图片的 size.

#### `image.setTemplateImage(option)`
* `option` Boolean
将图片标识为模板图片.

#### `image.isTemplateImage()`

返回 `Boolean` - 是否是模板图片.

#### `image.crop(rect)`

* `rect` Object - 要裁剪的图像区域
  * `x` Integer
  * `y` Integer
  * `width` Integer
  * `height` Integer

返回 `NativeImage` - 裁剪后的图片

#### `image.resize(options)`

* `options` Object
  * `width` Integer (可选)
  * `height` Integer (可选)
  * `quality` String (可选) - 可能的值是 `good`, `better` 或者 `best`。默认值是 `best`

返回 `NativeImage` - 调整大小的图像。
如果只指定 `height`或 `width`，那么图片将保持原始宽高比。

#### `image.getAspectRatio()`
返回 `Float`  - 图像的宽高比。

[buffer]: https://nodejs.org/api/buffer.html#buffer_class_buffer
