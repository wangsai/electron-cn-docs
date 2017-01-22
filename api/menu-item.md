## 本文介绍: 菜单项的创建与修改

> 在菜单中插入新的菜单项

进程: [主进程](../glossary.md#main-process)    

您可以在 [`Menu`](menu.md)查看示例.  

### `new MenuItem(options)`

* `options` Object
  * `click` Function (可选) - 当菜单项被点击的时候，使用 `click(menuItem,browserWindow)` 调用
     * `menuItem` MenuItem
     * `browserWindow` BrowserWindow
     * `event` Event
  * `role` String (可选) - 如果定义菜单项的行为，在指定 `click` 属性时将会被忽略
  * `type` String (可选) - 可选`normal`, `separator`, `submenu`, `checkbox` 或 `radio`.
  * `label` String - (可选)
  * `sublabel` String - (可选)
  * `accelerator` [Accelerator](accelerator.md) (可选)
  * `icon` ([NativeImage](native-image.md) | String) (可选)
  * `enabled` Boolean (可选) -如果为false，菜单项将显示为灰色且不可点击。
  * `visible` Boolean (可选) -如果为false，菜单项将完全隐藏。
  * `checked` Boolean (可选) - 应该只为 `checkbox`或 `radio`类型菜单项指定。
  * `submenu` (MenuItemConstructorOptions[] | Menu) (可选) - 应当作为 `submenu` 菜单项的特定类型，当它作为 `type: 'submenu'` 菜单项的特定类型时可以忽略。如果它的值不是 `Menu`，将自动转为 `Menu.buildFromTemplate`。
  * `id` String - 标志一个菜单的唯一性。如果被定义使用，它将被用作这个菜单项的参考位置属性。
  * `position` String - 定义给定的菜单的具体指定位置信息。

在创建菜单项时，如果有匹配的方法，建议指定 `role` 属性，不需要人为操作它的行为，这样菜单使用可以给用户最好的体验。
当使用 `role`时， `label`和 `accelerator`是可选的，并且为每个平台使用适当的默认值。

 `role`属性值可以为：

* `undo`
* `redo`
* `cut`
* `copy`
* `paste`
* `pasteandmatchstyle`
* `selectall`
* `delete`
* `minimize` -  最小化当前窗口
* `close` - 关闭当前窗口
* `quit`- 退出应用
* `reload` -重新加载当前窗口
* `toggledevtools`  - 在当前窗口中切换开发人员工具
* `togglefullscreen`  - 在当前窗口切换全屏模式
* `resetzoom`  - 将聚焦页面的缩放级别重置为原始大小
* `zoomin`  - 放大聚焦的页面10％
*` zoomout`  - 缩小聚焦页面10％

在macOS上 `role`也可以有以下附加值：

* `about`  - 映射到 `orderFrontStandardAboutPanel`动作
* `hide`  - 映射到` hide`动作
* `hideothers`  - 映射到`hideOtherApplications`动作
* `unhide`  - 映射到`unhideAllApplications`动作
* `startspeaking`  - 映射到`startSpeaking`动作
* `stoppeaking`  - 映射到`stopSpeaking`动作
* `front`  - 映射到`arrangeInFront`动作
* `zoom`  - 映射到`performZoom`动作
* `window`  - 子菜单是一个“窗口”菜单
* `help`  - 子菜单是一个“帮助”菜单
* `services`  - 子菜单是一个“服务”菜单

当在macOS上指定 `role`时， `label` 和 `accelerator`是唯一会影响MenuItem的选项。所有其他选项将被忽略。

###实例属性

####`menuItem.enabled`
该项是否启用，此属性可以动态更改。

####`menuItem.visible`
该项是否可见，此属性可以动态更改。

####`menuItem.checked`
该项是否选中，此属性可以动态更改。
 `checkbox` 菜单项将在选中时打开或关闭 `checked`属性
 `radio`菜单项将在点击时打开其“checked”属性，并将关闭同一菜单中所有相邻项的该属性。
 `click` 允许添加一个点击行为。

####`menuItem.label`
菜单项内容字符串

####`menuItem.click`
当MenuItem点击时候触发