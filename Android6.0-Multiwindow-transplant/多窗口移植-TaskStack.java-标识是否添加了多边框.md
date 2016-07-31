# TaskStack.java-标识多窗口问题问题
在Android中，一个ActivityStack对应着一个TaskStack，一个管理逻辑，一个管理界面绘制，所以在TaskStack中也有标识多窗口的标志

## Android5.1-multiwindow中

### 在Android5.1-multiwindow中TaskStack.java中mIsFloating代表是否处于多窗口中

### 在WindowState.java中使用
- computeFrameLw函数中使用
- refreshContainingFrame函数中使用，当处于多窗口的情况下会执行refreshContainingFrame这个函数，但是在Android6.0中，新添加来了一个fullScreen的判断，做的是一样的事，所以要细看着了要不要添加这个函数

## ## Android6.0-multiwindow中

- 对于这个变量可以重新命名为mIsAppendMWBorder
- 在computeFrameLw函数中使用
