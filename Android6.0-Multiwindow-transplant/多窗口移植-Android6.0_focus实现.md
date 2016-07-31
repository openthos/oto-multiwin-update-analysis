# 基于Android6.0实现multiwindow的focus

### setFocuseStack

因为当把APP窗口缩小时，点击桌面会focus桌面，所以一旦focus桌面，就会造成隐藏掉打开的多个窗口，这样不符合多窗口的特性，所以在focuse时过滤掉对于HomeActivity的显示到最前面

## ActivityManagerService.java
### setFocuseStack
- 这个方法就是通过StackId来focus不同的ActivityStack，Android5.1与Android6.0在这方面的机制又有所不同了，虽然在AMS中都有同样的方法，但是5.1中最终只调用了一个方法来focused 不同的ActivityStack，而6.0中把focus与真实的显示都最前面的两个函数分开了，所以在6.0中只是屏蔽了后面的真实显示到最前面的函数还是依旧focus，避免了5.1的很多做法（5.1屏蔽了桌面的focus，所以在TaskStack中有标识是否处于桌面的标志，而桌面其实并没有得到focus）在多出有代码的修改，这样难免会造成漏洞，6.0的避免了这个问题。
- AMS中其实还是调用到了ActvityStackSupervisor.java的方法来进行focus，但是5.1与6.0在这个方法上面有修改

## ActivityStackSupervisor.java
### setFocusedStack
- Android5.1中对于这个方法最终就是调用moveHomeStack来进行focus的显示切换，因为这种特性所以无法做到focus不同的ActivityStack，所以新加了多个方法进行单个ActivityStack的focus，其实就是最终调用了moveToFront方法，不过做了桌面与其它的不同判断

- 6.0中，这个方法最终就只直接调用了moveToFront，所以与5·1的机制不同，就没有做了修改

目前看来，只是在多窗口情况下屏蔽了对于homeActivity显示到最前面的步骤，所以不会对整体造成影响

## 补充
### PhoneWindow.java
- 点击多窗口模式下窗口的header可以拖拉，但是这时没有focus，所以在PhoneWindow.Java中添加了setFocus的方法，当点击窗口Header时就会focus这个窗口
