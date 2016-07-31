### stack创建

- computeStackFocus 同5.1 adjustStackFocus，每次生成新窗口，强制启动一个新的stack。

### stack创建大小调整

- computeStackFocus新建stack后进行判断，若为多窗口状态下并且为全屏模式下，
使用6.0自带的resizeStack调整的窗口大小为全屏大小。若为多窗口非全屏状态下，
调用getInitializingRect函数生成新的窗口大小与位置，再调用resizeStack。

- getInitializingRect，使窗口以层叠的形式出现。同时调用WMS的getDisplayMetrics 依据屏幕大小进行窗口大小位置的调整。

- 在WindowManagerService中添加getDisplayMetrics方法。
