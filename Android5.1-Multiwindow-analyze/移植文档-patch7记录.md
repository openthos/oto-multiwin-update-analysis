# 0007-multiwindows-Merge-the-trivial-java-code.patch:

## Activity.java:
- performCreate方法中添加setWindowAttributes()主要是对window的stackId与stackId进行设置，主要用途是多窗口中resize窗口时需要获得stackId，而单窗口不需要进行resize（但是在这个patch中是直接设置的没有写这个方法）

### IActivityManager.java:
添加了用于Binder通信的code：
- RELAYOUT_WINDOW_CORNERSTONE_TRANSACTION（Transaction for changing window position）

- CLOSE_ACTIVITY_WITH_WINDOW_TRANSACTION（Transaction for closing application in window）

- SET_MAXIMIZED_WINDOW_SIZE_TRANSACTION（Transaction for setting custom maximized window size）

- GET_MAXIMIZED_WINDOW_SIZE_TRANSACTION（Transaction for getting maximized window size）

- 用于ActivityManagerNative 与 ActivityManagerProxy 继承时使用，binder通信，Android底层这个使用的是proxy模式

### Intent.java:
添加了两个flag：
- FLAG_ACTIVITY_RUN_ON_EXTERNAL_DISPLAY = 0x00000080
- FLAG_ACTIVITY_RUN_IN_WINDOW = 0x00000100
- 通过查看代码发现FLAG_ACTIVITY_RUN_ON_EXTERNAL_DISPLAY基本没有使用到而FLAG_ACTIVITY_RUN_IN_WINDOW标识是否正处于multiwindow

### Window.java:
- 添加了变量mStackId、mTaskId,并提供了get与set方法，供Activity调用应用于多窗口的resize操作，isMWPanel()只是判断了是否是SystemUI或者是Launcher，是否要添加 多窗口

### WindowManager.java:
- 添加TYPE_MULTIWINDOW_APPLICATION = 100，用于标识多窗口，目前主要用于桌面不同的显示层级，在PhoneWindowManager中返回新的多窗口层级

### ActivityManagerService.java:
- 添加了mMaximizedWindowSize

### ActivityRecord.java:
- 对变量fullScreen的修改，变成了私有变量，并添加了isFullscreen()和setFullscreen(boolean fullscreen)，在isFullscreen中如果是多窗口的话就不能全屏了，添加了判断，并更改了fullScreen的使用代码都替换成了isFullscreen()，这些文件都包括ActivityRecord.java、ActivityStack.java、
ActivityStackSupervisor.java、TaskRecord.java

### ActivityStack.java:
- 添加了mIsMultiwindowStack（用于判断是否创建了multiwindow的stack）

### InputManagerService.java:
- monitorInput方法的改进，使其能够支持更多的变种，没有发现在代码其它地方使用到扩展的方法

### DisplayContent.java:
- 添加了一个sCurrentTouchedDisplay变量，在多窗口系统下标识了当前display是否处于focus状态

### DragState.java：
- 简化了getTouchedWinAtPointLw方法中对于拖拽的一个判断（具体作用还有待具体分析）

### StackTapPointerEventListener.java：
- 在ActionUp中添加了一个过滤条件，猜测是多窗口中是否focus窗口相关


### 总结：这个patch的添加主要是一些代码的合并，有的对于后期的多窗口至关重要，但是有的完全没有用
