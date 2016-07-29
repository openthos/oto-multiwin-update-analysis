# 基于Android6.0实现multiwindow的focus

## ActivityStackSupervisor.java
- 添加了isMultiwindow的变量，用来标识是否处于multiwindow状态下

### computeStackFocus
- computeStackFocus函数中如果处于多窗口的话跳过一段代码，这个代码的主要作用是返回第一个非homeStack的stack，只有把这段代码跳过才能在屏幕上显示多个窗口，要不然会代替掉前面的stack

## TaskStack.java

### mFullscreen
- mFullscreen标识这个窗口是否是全屏的

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## ActivityManagerService.java

### setFocuseStack
- 因为当把APP窗口缩小时，点击桌面的APP也会因为touch到桌面而focus桌面，所以一旦点击APP就会focus桌面，在focuse时过滤掉对于HomeActivity的显示到最前面，这样能解决在多窗口时打开一个新的APP不会切回到桌面，而是直接打开

- 这种做法在从launch直接返回桌面时，会直接回到桌面，但是在点击打开APP时还是会把所有的APP都同时显示出来，因为桌面放到了最后面，相当于桌面的Z轴变换，但是因为导航栏最后还是会隐藏掉，所以这个问题就可以不care了

- 目前看来，只是在多窗口情况下屏蔽了对于homeActivity显示到最前面的步骤，所以不会对整体造成影响