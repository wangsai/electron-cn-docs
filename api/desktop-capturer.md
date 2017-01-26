# 本文介绍:捕获桌面资源

> 通过[`navigator.webkitGetUserMedia`] API 捕获audio 和 video等媒体资源

进程: [Renderer](../glossary.md#renderer-process)

从 `Electron`桌面应用中捕获video:
```javascript
// In the renderer process.
const {desktopCapturer} = require('electron')

desktopCapturer.getSources({types: ['window', 'screen']}, (error, sources) => {
  if (error) throw error
  for (let i = 0; i < sources.length; ++i) {
    if (sources[i].name === 'Electron') {
      navigator.webkitGetUserMedia({
        audio: false,
        video: {
          mandatory: {
            chromeMediaSource: 'desktop',
            chromeMediaSourceId: sources[i].id,
            minWidth: 1280,
            maxWidth: 1280,
            minHeight: 720,
            maxHeight: 720
          }
        }
      }, handleStream, handleError)
      return
    }
  }
})

function handleStream (stream) {
  document.querySelector('video').src = URL.createObjectURL(stream)
}

function handleError (e) {
  console.log(e)
}
```
当使用这个方法的时候，不用指定 `chromeMediaSourceId`.

当调用 `navigator.webkitGetUserMedia` 时创建一个约束对象后.

如果你想使用 `desktopCapturer` 的资源，您必须设置 `chromeMediaSource` 为 ``desktop`` ， `audio` 为 ``false``.

如果你想捕获整个桌面的 audio 和 video，您必须设置 `chromeMediaSource` 为 ``screen`` ， `audio` 为 ``true``.


## 方法

### `desktopCapturer.getSources(options, callback)`

* `options` Object
  * `types` String[] - 列出可捕获的桌面资源类型的String 数组, 可用类型为 `screen` 和 `window`.
  * `thumbnailSize` Object (optional) - 等比缩放的缩图尺寸, 默认为 `{width: 150, height: 150}`.
* `callback` Function
  * `error` Error
  * `sources` [DesktopCapturerSource[]](structures/desktop-capturer-source.md)

发起 获取所有桌面资源 的请求，请求完成后使用 `callback(error, sources)` 调用  `callback` .

 `sources` 是一个[`DesktopCapturerSource`](structures/desktop-capturer-source.md) 对象数组, 每个 `DesktopCapturerSource` 表示了一个捕获的屏幕或窗口并且有如下属性 :
* `id` String - 在 `navigator.webkitGetUserMedia` 中使用的捕获窗口或屏幕的 id . 格式为 `window:XX` 或者 `screen:XX`， `XX` 是一个随机数.
* `name` String - 捕获窗口或屏幕的描述名 . 如果资源为屏幕，名字为 `Entire Screen` 或 `Screen <index>`; 如果资源为窗口, 名字为窗口的标题.
* `thumbnail` [NativeImage](NativeImage.md) - 缩略图.

**注意:** 不能保证 `source.thumbnail` 的 尺寸 和 `options` 中的 `thumnbailSize` 一直一致. 因为它也取决于屏幕或窗口的缩放比例.

[`navigator.webkitGetUserMedia`]: https://developer.mozilla.org/en/docs/Web/API/Navigator/getUserMedia