# TaskStack.java-mIsFloating 问题

## Android5.1-multiwindow中

### 在Android5.1-multiwindow中TaskStack.java中mIsFloating代表是否处于多窗口中

### 在WindowState.java中使用
- computeFrameLw函数中使用(? 不知道为什么要添加这个判断，觉得没必要)
- refreshContainingFrame函数中使用，当处于多窗口的情况下会执行refreshContainingFrame这个函数，但是在Android6.0中，新添加来了一个fullScreen的判断，做的是一样的事，所以要细看着了要不要添加这个函数

## ## Android6.0-multiwindow中

- 对于这个变量可以重新命名为mIsAppendMWBorder