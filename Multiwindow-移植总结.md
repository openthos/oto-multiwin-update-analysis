# 基于Android6.0-x86实现多窗口操作系统
内容：
- 项目简介
- 功能需求
- 软件支持
- 存在问题
- multiwindow5.1分析
- 设计实现

## 项目简介
对于原生Android6.0-x86与原生Android5.1-x86的对比分析，并参考分析基于Android5.1-x86实现的multiwindow，基于Android6.0-x86实现简易的多窗口
操作系统

## 功能需求
|完成|描述|
|---|---|
|√| 在窗口外层加上类似于Windows的边框与按钮（最大化、最小化、返回、关闭）
|√| 实现打开一个APP打开一个单独的窗口，实现多个窗口在同一桌面共存
|√| 点击不同的窗口能实现焦点的切换，但是点击桌面不能隐藏掉其它的窗口
|√| 实现窗口的拖拉，并能够拖拉到桌面外，hover到边框时能实现鼠标样式的改变，拖拉边框改变窗口大小
|√| 实现简单的状态栏，打开新的APP能在状态栏上面显示，并能与桌面窗口进行交互
|√| 实现窗口的最小化、关闭、返回功能，并实现最大化与正常状态之间的切换

## 软件支持
原生Android6.0-x86支持的软件在目前的multiwindow6.0上面都能运行，下面是在HP机器上面做的测试报告
- [HP:原生Android6.0 VS multiwindow6.0运行软件对比.md](https://github.com/openthos/oto-multiwin-update/blob/master/Android6.0-Multiwindow-transplant/HP:%E5%8E%9F%E7%94%9FAndroid6.0%20VS%20multiwindow6.0%E8%BF%90%E8%A1%8C%E8%BD%AF%E4%BB%B6%E5%AF%B9%E6%AF%94.md)

## 存在问题
目前的多窗口操作系统还是有一些问题需要解决，对于Android5.1移植的过程中出现的问题在以下链接文档中：
- [多窗口边框拖拉程序LineRect在Android6.0上面的问题解决.md](https://github.com/openthos/oto-multiwin-update/blob/master/Android6.0-Multiwindow-transplant/%E5%A4%9A%E7%AA%97%E5%8F%A3%E7%A7%BB%E6%A4%8D-%E9%81%97%E7%95%99%E9%97%AE%E9%A2%98.md)

- [多窗口移植-遗留问题.md](https://github.com/openthos/oto-multiwin-update/blob/master/Android6.0-Multiwindow-transplant/%E5%A4%9A%E7%AA%97%E5%8F%A3%E8%BE%B9%E6%A1%86%E6%8B%96%E6%8B%89%E7%A8%8B%E5%BA%8FLineRect%E5%9C%A8Android6.0%E4%B8%8A%E9%9D%A2%E7%9A%84%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3.md)

## multiwindow5.1分析
- 对于基于Android5.1实现的multiwindow的分析并没有很全面，只是选取了有用的进行了分析，分析文档在
[multiwindow5.1分析文档]https://github.com/openthos/oto-multiwin-update/tree/master/Android5.1-Multiwindow-analyze

## 设计实现
- 包括了对于原生Android6.0的部分机制的分析，和基于Android6.0开发的multiwindow的设计实现文档
[multiwindow6.0设计文档]https://github.com/openthos/oto-multiwin-update/tree/master/Android6.0-Multiwindow-transplant



