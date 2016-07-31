# 0011-multiwindow-Support-normal-window-and-multi-window-s.patch

##　Activity.java

- 在getStackId函数中添加了判断，当不要添加边框时StackId设置为-1

## TaskStack.java

- 添加了变量 mIsFloating 和 isFloating方法（？ is Multiwindow）

## WindowManagerService.java:

- 因为使用了使用了TaskStack，但是这个类新添加了mIsFloating，所以相应的做了改变

## ActivityStack.java




