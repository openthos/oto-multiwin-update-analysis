# 0008-multiwindow-Display-correct-window.patch

## mw_decor.xml
## symbols.xml


## Activity.java：
- 添加了getStackId()方法，获得Activity的StackId，主要是用于performCreate方法中添加setWindowAttributes()中window setStackId调用


## ActivityManagerNative.java:
- onTransact中当code 是 RELAYOUT_WINDOW_CORNERSTONE_TRANSACTION的情况下的操作

- ActivityManagerProxy中添加了relayoutWindow方法，供调用，主要是与ActivityManagerNative进行交互，具体的实施都是在ActivityManagerNative中

## IActivityManager.java：
- 添加relayoutWindow的抽象方法，ActivityManagerProxy继承使用

## PhoneWindow.java：
- 添加了变量mDecorMW（后面的代码删除了这个变量，所以这里不细说了），当前的窗口边框实现是在mContentParent中添加了边框和放大缩小等组件，然后在PhoneWindow.java获得各组件设置touchListener和hoverListener等，这个可以在后期细看，其中对于多窗口的判断中

## ActivityManagerService.java：
- 在方法getAllStackInfos函数中注释掉enforceCallingPermission（？）
- 添加relayoutWindow函数，这个函数主要是调用了WMS中的relayoutWindow函数，这个函数主要作用的改变window的position

## ActivityStack.java:
- behindFullscreen变量赋值更改，后期的版本中添加了一个isMultiwindowStack()方法，如果是Multiwinddow时候behindFullscreen赋值false

## ActivityStackSupervisor.java
- 注释掉了moveHomeStackTaskToTop方法，但是在版本的后期删除了这个方法（移植中只注释掉不删除）
- adjustStackFocus函数中添加一个判断当是multiwindow的时候跳过一段返回 focusStack的循环（具体？）添加relayoutWindow
在后期的版本中添加了setFocusedStack的重载，在moveTaskToStackLocked中调用了这个方法，在原始的版本中只是单纯的设置mFocusedStack = stack（添加的setFocusedStack具体流程？）

## TaskStack.java：
- 添加变量mCrappyRelayouted，用以标识是否用relayoutWindow更改过位置，并添加了set和get方法

## DimLayer.java：
- adjustSurface函数中判断添加条件

## DisplayContent.java：
- 添加mHomeHasFocus变量
- 添加relayoutStack函数，重新设置Task的位置
- 更改了stackIdFromPoint函数，这个函数是根据x，y的位置来获得task Stack id，添加了WMS中的一些操作（？）

## WindowManagerService.java：
- findFocusedWindowLocked函数的更改（）
- 添加了relayoutWindow用于重新更改窗口的位置

## WindowState.java:
- 更改了computeFrameLw函数（）
