### PhoneWindow

- 功能：拖拽窗口

- mHeader添加监听事件

- 添加TouchListener类，用于拖拽和缩放使用。添加这个类所需的常量。
添加ResizeWindow 抽象类，拖拽与缩放实现不同的ResizeWindow.resize方法。

### 跳过窗口边界检测

- 6.0中会添加窗口边界判断保证窗口边界在窗口内。造成无法将窗口的拖出屏幕。
涉及代码为TaskStack.setBounds、WindowState.computeFrameLw

#### 修改代码

- services/core/java/com/android/server/wm/TaskStack.java
这个类的setBounds中添加判断，若非为多窗口的stack，则跳过这里的边界检测             

        if (!isMultiWindowStack && !bounds.intersect(mTmpRect)) {
                     return false;
        }
- WindowState.java的computeFrameLw添加判断若为多窗口跳过边界检测。

        //多窗口跳过边界检测
        if (!isMultiWindowStack && !mContainingFrame.intersect(cf)) {
                 mContainingFrame.set(cf);
        }

        //同时与5.1相同在窗口下，跳过一下步骤。
        if(!isMultiWindowStack){
            Gravity.applyDisplay(mAttrs.gravity, mDisplayFrame, mFrame);
        }
        ....
        if(!isMultiWindowStack){
                   mContentFrame.set(Math.max(mContentFrame.left, mFrame.left),
                           Math.max(mContentFrame.top, mFrame.top),
                           Math.min(mContentFrame.right, mFrame.right),
                           Math.min(mContentFrame.bottom, mFrame.bottom));
        ....
