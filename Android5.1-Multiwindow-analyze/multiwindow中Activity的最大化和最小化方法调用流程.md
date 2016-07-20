# MultiWindow中Activity的最大化和最小化方法调用流程

## 1. PhoneWindow.java
- 通过findViewById()获取最小化按钮控件mMinimizeBtn和最大化控件mMaximizeBtn；
- 为最大化按钮控件mMaximizeBtn和最小化控件按钮mMinimizeBtn设置监听事件，即mMaximizeBtn.setOnClickListener()和mMinimizeBtn.setOnClickListener()，在监听事件中调用ActivityManagerService类中的relayoutWindow(int stackId, Rect r)方法；

## 2. ActivityManagerService.java
- 设置新的窗口的大小，调用方法为relayoutWindow(int stackId, Rect r),在这个方法中调用了WindowManagerService对象中的relayoutWindow(int stackId, Rect pos)方法；

## 3. WindowManagerService.java
- 执行relayoutWindow(int stackId, Rect pos)方法，在该方法中调用了DisplayContent对象中的relayoutStack(int stackId, Rect pos)方法；

## 4. DisplayConent.java
- 执行relayoutStack(int stackId, Rect pos)方法，在该方法中调用了TaskStack对象中的setBoundsByForce(Rect bounds)方法；

## 5. TaskStack.java
- 执行setBoundsByForce(Rect bounds)方法，在这个方法中调用了DimLayer对象的setBounds(Rect bounds)方法；

## 6. DimLayer.java
- 执行setBounds(Rect bounds)方法，在其中又调用了adjustSurface(int layer, boolean inTransaction)方法；
- 在adjustSurface(int layer, boolean inTransaction)方法中又调用了SurfaceControl对象的setSize(int w, int h)方法；

## 7. SurfaceControl.java
- 执行setSize(int w, int h)方法，在这个方法中通过JNI最终调用本地方法nativeSetSize(mNativeObject, w, h)来进行处理。
