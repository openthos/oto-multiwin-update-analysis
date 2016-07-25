# MultiWindow中Activity关闭方法的移植记录

## 1.PhoneWindow.java

- 通过findViewById()方法获取mCloseBtn控件；
- 为mCloseBtn控件设置监听事件，即mCloseBtn.setOnClickListener();

## 2.ActivityManagerNative.java

- 添加closeActivity(int stackId)方法，需要注意的是，在内部类ActivityManagerProxy中添加closeActivity(int stackId)方法之后，需要在onTransact()方法中添加相应的case条件；

## 3.IActivityManager.java

- 添加相应的抽象类和常量；

## 4.ActivityManagerService.java

- 添加closeActivity(int stackId)方法的具体实现，在其内部又调用了closeActivity(int stackId, boolean individual, int activities)方法；
- 在closeActivity(int stackId, boolean individual, int activities)方法内部调用了removeStatusbarActivity(int stackId)方法；
- 在removeStatusbarActivity(int stackId)方法内部获取StatusBarManagerService服务，调用该服务的removeStatusbarActivity(stackId)方法；


## 5.StatusBarManagerInternal.java

- 添加removeStatusbarActivity(stackId)抽象方法；

## 6.StatusBarManagerService.java

- 添加removeStatusbarActivity(int stackId)方法；
- 在removeStatusbarActivity(int stackId)方法中，调用IStatusBar对象的removeStatusbarActivity(stackId)方法；

## 7.IStatusBar.aidl

- 添加removeStatusbarActivity(int stackId)方法的声明；

## 8.CommandQueue.java

- 添加removeStatusbarActivity(int stackId)的声明？需添加三处代码；
- 注意：因为PhoneStatusBar.java和TvStatusBar.java继承了CommmandQueue类，所以在CommandQueue.java中添加相应的方法之后，还需要在这两个文件中添加相应的方法；

## 9.PhoneStatusBar.java

- 添加removeStatusbarActivity(int stackId)方法的具体实现；
