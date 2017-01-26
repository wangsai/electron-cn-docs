## 前言:

本文采用官方electron翻译并提供相关教程与资源,较之官译本更加精简和本土化.

## 初学者入门指导(Win为例):

* 1.设置环境变量:
```
方法:计算机右键-属性-高级系统设置-环境变量-新建
变量名:ELECTRON_MIRROR,变量值:http://npm.taobao.org/mirrors/electron/
```
* 2.[Node.js安装](https://nodejs.org/en/download/)
* 3.[git安装](https://git-for-windows.github.io/)
* 4.[Electron安装](tutorial/quick-start.md)

## 常见:

提交issue前,请详读以下常见问题:
* [Electron 常见问题](faq.md)        

## 特别注意:

* 与Jquery等第三方js插件的兼容相关
```
<script src=`jquery.min.js`></script>
<script>if (typeof module === 'object') {window.jQuery = window.$ = module.exports;};</script>
```

## 指南:
本文对应的是docs中的api文档全部翻译或精校,最后的版本时间为:2017-01-26