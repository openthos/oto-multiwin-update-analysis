### 任务栏功能

- 启动应用，任务栏出现对应用图标。关闭应用：对应应用任务图标消失。

- 调用：PhoneWindow 在创建使用添加：调用AMS中显示应用任务图标。关闭时候：调用AMS中的隐藏应用任务图标。

### 显示隐藏***应用任务图标***涉及代码

#### AMS添加代码

- AMS中添加createStatusbarActivity、removeStatusbarActivity方法
用于显示任务栏任务图标与隐藏应用任务图标。（涉及文件包括ActivityManagerService.java、
ActivityManagerNative.java、IWindow.java, 具体详细见log）

- IWindow.java 添加createStatusbarActivity、removeStatusbarActivity接口方法与IBinder所需的常量

- ActivityManagerNative.java 代理类中添加createStatusbarActivity、removeStatusbarActivity方法、在 onTransact添加方法调用AMS同名方法。

- ActivityManagerService.java 添加具体实现createStatusbarActivity、removeStatusbarActivity方法。
添加对显示应用任务图标的统一管理的集合ArrayList<Integer> mStatusbarActivities
添加进程mSBAThread，用于周期性清理已关闭应用的应用任务图标、窗口关闭的焦点切换。
需要在AMS中添加killUselessStatusbarActivity，
在ActivityStackSupervisor中添加getUpdateStack、needResetHomeStack方法。

#### StatuBarManagerService添加代码

- AMS方法中调用StatusBarManagerService.mInternalService的showStatusbarActivity、
removeStatusbarActivity分别对应显示、隐藏应用任务图标。
（涉及文件为StatusBarManagerInternal,IStatusBar.aidl,CommandQueue.java,PhoneStatusBar）
      StatusBarManagerService.mInternalService（接口为StatusBarManagerInternal）
        ->CommandQueue(接口为IStatusBar.aidl)
        ->PhoneStatusBar(接口为CommandQueue.Callbacks)
- StatusBarManagerInternal 添加showStatusbarActivity、removeStatusbarActivity接口

- StatusBarManagerService.mInternalService 实现中添加showStatusbarActivity、removeStatusbarActivity实现，
这个实现会调用IStatusBar.aidl接口的实现的同名方法。

- IStatusBar.aidl接口中添加showStatusbarActivity、removeStatusbarActivity接口

- CommandQueue.java 是IStatusBar.aidl实现，添加showStatusbarActivity、removeStatusbarActivity方法。
这些方法会调用CommandQueue.Callbacks接口的同名方法。

- 抽象类BaseStatusBar实现了CommandQueue.Callbacks,PhoneStatusBar 继承BaseStatusBar，
具体的CommandQueue.Callbacks实现在PhoneStatusBar中。
PhoneStatusBar中添加showStatusbarActivity、removeStatusbarActivity方法。

#### 应用任务图标具体实现

- 在状态栏布局文件status_bar.xml中添加LinearLayout用于显示应用任务栏。

- 在PhoneStatusBar中用mStatusBarActivities（LinearLayout）对任务图标视图显示进行管理。

- 添加ActivityKeyView类表示应用任务图标视图（显示的view）。

- 添加StatusbarActivity类用于存储应用基本信息（包名、图标等）。
