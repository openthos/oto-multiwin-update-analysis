# Activity窗口的四按键（最大化、最小化、返回、关闭）实现总结

## 一、基本原理
在Andorid系统中，当一个Activity启动时，系统会为这个正在启动的Activity组件创建一个Window对象，用来描述一个应用程序的窗口。而事实上，Activity类的成员变量mWindow指向的并不是一个Window对象，而是一个PhoneWindow对象。也就是说，一个Activity组件的UI是使用一个PhoneWindow对象来描述的。 PhoneWindow类继承了Window类，因此，它的对象可以保存Activity类的成员变量mWindow中。因此，应该在PhoneWindow类中来获取已经添加的窗口边框中的四个功能按键（关于窗口边框的添加请参考[给Activity添加多边框](https://github.com/openthos/oto-multiwin-update/blob/master/Android6.0-Multiwindow-transplant/%E5%A4%9A%E7%AA%97%E5%8F%A3%E7%A7%BB%E6%A4%8D-%E6%B7%BB%E5%8A%A0%E8%BE%B9%E6%A1%86.md)），即关闭按键、返回按键、最大化按键和最小化按键（顺序为从右到左），之后分别为其添加相应的监听事件即可。

## 二、关闭按键功能的实现


2.1 PhoneWindow.java

- 定义关闭控件

```
private ImageButton mCloseBtn;
```

- 获取控件

```
mCloseBtn = (ImageButton)root.findViewById(com.android.internal.R.id.mwCloseBtn);
```

- 设置监听事件

```
mCloseBtn.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        try {
            ActivityManagerNative.getDefault().closeActivity(getStackIdForMultiwindow());
        } catch (RemoteException e) {
            Log.e(TAG, "Close button failes", e);
        }    
    }    
});
```
在其监听事件中，虽然其调用了ActivityManagerNative类中的相关方法，但其最终还是调用了ActivityManagerService服务中的closeActivity(int stackId)方法；

2.2 ActivityManagerService.java

- closeActivity(int stackId)方法

```
/**   
  * Date: Aug 28, 2014
  * Copyright (C) 2014 Tieto Poland Sp. z o.o.
  *
  * Method for closing application by stackbox id and closing their tasks.
  */
@Override
public boolean closeActivity(int stackId) {
    return closeActivity(stackId, true, 0);
}
```
在本方法中调用了closeActivity(int stackId, boolean individual, int activities)方法；

- closeActivity(int stackId, boolean individual, int activities)方法

```
private boolean closeActivity(int stackId, boolean individual, int activities) {
    boolean succeed;

    synchronized (mSBAThread) {
        if(individual && activities == 0) {
            removeStatusbarActivity(stackId);
        }     
    }     

    if (!individual) {
        return true;
    }     

    /* FIXME: may memory leak which is missed by finishActivity()?? */
    long ident = Binder.clearCallingIdentity();
    StackInfo stack = getStackInfo(stackId);
    if (stack != null) {
        for (int next = stack.taskIds.length - 1; next >= 0; --next) {
            removeTask(stack.taskIds[next]);
        }     
        succeed = true;
    } else {
        succeed = false;
    }     
    Binder.restoreCallingIdentity(ident);
    return succeed;
}
```
在该方法中，总共实现了两个功能，一个是把下方StatusBar中的要关闭的应用程序的图标移除，调用的是removeStatusbarActivity(int stackId)方法；另一个是关闭该应用程序，调用的是removeTask()方法。

- removeStatusbarActivity(int stackId)方法


```
private void removeStatusbarActivity(int stackId) {
    StatusBarManagerInternal statusBarManager = LocalServices.getService(StatusBarManagerInternal.class);
    statusBarManager.removeStatusbarActivity(stackId);
    mStatusbarActivities.remove((Integer)stackId);
}
```

该方法的作用是关闭系统下方状态栏的对应的应用程序的图标，其最终调用的是StatusBarManagerService中的removeStatusbarActivity(int stackId)方法；

- removeTask(int taskId)方法


```
@Override
public boolean removeTask(int taskId) {
    synchronized (this) {
        enforceCallingPermission(android.Manifest.permission.REMOVE_TASKS,
                    "removeTask()");
        long ident = Binder.clearCallingIdentity();
        try {
            return removeTaskByIdLocked(taskId, true);
        } finally {
            Binder.restoreCallingIdentity(ident);
        }
    }
}
```

在这个方法中又调用了removeTaskByIdLocked(taskId, true)方法。

- removeTaskByIdLocked(int taskId, boolean killProcess)方法

```
/**
  * Removes the task with the specified task id.
  *
  * @param taskId Identifier of the task to be removed.
  * @param killProcess Kill any process associated with the task if possible.
  * @return Returns true if the given task was found and removed.
  */
private boolean removeTaskByIdLocked(int taskId, boolean killProcess) {
    TaskRecord tr = mStackSupervisor.anyTaskForIdLocked(taskId, false);
    if (tr != null) {
        tr.removeTaskActivitiesLocked();
        cleanUpRemovedTaskLocked(tr, killProcess);
        if (tr.isPersistable) {
            notifyTaskPersisterLocked(null, true);
        }
        return true;
    }
    Slog.w(TAG, "Request to remove task ignored for non-existent task " + taskId);
    return false;
}
```
在这个方法中，首先根据stackId来获取相应的TaskRecord对象，之后调用该对象的removeTaskActivitiesLocked()方法即可，这样，就关闭了相应的应用程序。

2.3 StatusBarManagerService.java

- removeStatusbarActivity(int stackId)方法

```
@Override
    public void removeStatusbarActivity(int stackId) {
        if (mBar != null) {
            try {
                mBar.removeStatusbarActivity(stackId);
            } catch (RemoteException e) {
        }
    }
}
```

在这个方法中，最终调用了PhoneStatusBar对象的removeStatusbarActivity(int stackId)方法；

2.4 PhoneStatusBar.java

- removeStatusbarActivity(stackId)方法

```
@Override // CommandQueue
public void removeStatusbarActivity(stackId) {
    int idx = findStatusbarActivityByStackId(stackId);
    if(idx >= 0) {
        if(!((ActivityKeyView) mStatusBarActivities.getChildAt(idx)).getStatusbarActivity().mIsDocked) {
            mStatusBarActivities.removeView((ActivityKeyView) mStatusBarActivities.getChildAt(idx));
        } else {
            ((ActivityKeyView) mStatusBarActivities.getChildAt(idx)).activityClosed();
        }
        Log.d(TAG,"pass removeStatusbarActivity");
    }
}
```

在这个方法中，首先根据所传入的stackId来获取所对应的ActivityKeyView对象，该对象指的是下方状态栏中的应用程序图标对象，之后调用该对象内的关闭方法即可关闭下方状态栏中的相应图标；

## 三、返回按键功能的实现

3.1 PhoneWindow.java

- 定义返回控件

```
private ImageButton mBackBtn;
```

- 获取控件

```
mBackBtn = (ImageButton)root.findViewById(com.android.internal.R.id.mwBackBtn);
```

- 设置监听事件

```
mBackBtn.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        try {
            Runtime.getRuntime().exec("input keyevent " + KeyEvent.KEYCODE_BACK);
        } catch (IOException e) {
            Log.e(TAG, "Back button failes", e);
        }
    }
});
```

在监听事件中，直接发送一个原生Android系统中的返回按键的信号即可；

## 四、最大化按键功能的实现

4.1 PhoneWindow.java

- 定义最大化控件

```
private ImageButton mMaximizeBtn;
```

- 获取最大化控件

```
mMaximizeBtn = (ImageButton)root.findViewById(com.android.internal.R.id.mwMaximizeBtn);
```

- 设置监听事件

```
mMaximizeBtn.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        Rect actualWindowSize = new Rect(mDecor.getViewRootImpl().mWinFrame);
        try {
            Rect customMaximizedWindowSize = ActivityManagerNative.getDefault().getMaximizedWindowSize();
            if (!customMaximizedWindowSize.equals(new Rect())) {
                mFullScreen = customMaximizedWindowSize;
            } else {
                mFullScreen = new Rect(0, 0, metrics.widthPixels, metrics.heightPixels);
            }
            if (!actualWindowSize.equals(mFullScreen)){
                mOldSize = actualWindowSize;

                ActivityManagerNative.getDefault().setOldWindowSizeByStackId(getStackIdForMultiwindow(), mOldSize);

                ActivityManagerNative.getDefault().resizeStack(getStackIdForMultiwindow(), mFullScreen);
                actualWindowSize = mFullScreen;
            } else {
                mOldSize = ActivityManagerNative.getDefault().getOldWindowSizeByStackId(
                getStackIdForMultiwindow());

                if (mOldSize == null) {
                    mOldSize = new Rect(mDecor.getViewRootImpl().mWinFrame);
                }
                actualWindowSize = mOldSize;
                ActivityManagerNative.getDefault().resizeStack(getStackIdForMultiwindow(), mOldSize);
            }
        } catch (RemoteException e) {
            Log.e(TAG, "Maximize failed", e);
        }
    }
});
```

在这个方法中，首先需要保存当前Activity窗口的大小，这是为了之后再次点击最大化按钮之后可以恢复到之前的窗口状态，关于最大化正常切换的问题可以具体参考文档[最大化与正常切换问题](https://github.com/openthos/oto-multiwin-update/blob/master/Android6.0-Multiwindow-transplant/%E5%A4%9A%E7%AA%97%E5%8F%A3%E7%A7%BB%E6%A4%8D-Android6.0-%E6%9C%80%E5%A4%A7%E5%8C%96%E8%BF%98%E5%8E%9F%E5%88%87%E6%8D%A2.md)；保存之后，调用resizeStack()方法来改变Activity窗口的大小；

改变窗口大小所调用的方法为resizeStack()方法，resizeStack()方法为Android 6.0新添加的方法，注意，在Android 6.0中，我们用这个方法替换了Android 5.1中的relayoutWindow()方法；

4.2 ActivityManagerService.java

- resizeStack(int stackId, Rect bounds)方法

```
@Override
public void resizeStack(int stackId, Rect bounds) {
    long ident = Binder.clearCallingIdentity();
    try {
        synchronized (this) {
            mStackSupervisor.resizeStackLocked(stackId, bounds);
        }
    } finally {
        Binder.restoreCallingIdentity(ident);
    }
}
```

该方法为Android 6.0中新添加的方法，直接调用即可；

## 五、最小化按键功能的实现

5.1 PhoneWindow.java

- 定义最小化控件

```
private ImageButton mMinimizeBtn;
```

- 获取控件

```
mMinimizeBtn = (ImageButton)root.findViewById(com.android.internal.R.id.mwMinimizeBtn);
```

- 设置监听事件

```
mMinimizeBtn.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        Rect actualWindowSize = new Rect(mDecor.getViewRootImpl().mWinFrame);
        Rect outOfScreen = new Rect(actualWindowSize.left + metrics.widthPixels,
                                    actualWindowSize.top + metrics.heightPixels,
                                    actualWindowSize.right + metrics.widthPixels,
                                    actualWindowSize.bottom + metrics.heightPixels);
        try {
            ActivityManagerNative.getDefault().saveInfoInStatusbarActivity(getStackIdForMultiwindow(), actualWindowSize);
            ActivityManagerNative.getDefault().resizeStack(getStackIdForMultiwindow(), outOfScreen);
        } catch (RemoteException e) {
            Log.e(TAG, "Minimize failed", e);
        }
    }
});
```


在这个监听事件中，首先设置新的窗口尺寸大小，使其可以移出屏幕，之后将该应用程序的信息保存在状态栏的图标对象中，最后调用resizeStack()方法改变窗口的大小即可；
saveInfoInStatusbarActivity()方法的具体实现在PhoneStatusBar.java文件中；

5.2 PhoneStatusBar.java

- saveInfoInStatusbarActivity(int stackId, Rect rect)方法

```
@Override // CommandQueue
public void saveInfoInStatusbarActivity(int stackId, Rect rect) {
    int idx = findStatusbarActivityByStackId(stackId);
    if(idx >= 0) {
        Log.d(TAG,"pass saveInfoInStatusbarActivity");
        ((ActivityKeyView) mStatusBarActivities.getChildAt(idx)).saveStackInfo(rect);
    }
}
```

在这个方法中，首先通过stackId来找到相应的ActivityKeyView对象，之后调用该对象的saveStackInfo方法即可保存该Activity的相关信息；
其中，通过stackId查找idx需要调用findStatusbarActivityByStackId(int stackId)方法；

- findStatusbarActivityByStackId(int stackId)方法

```
public int findStatusbarActivityByStackId(int stackId) {
    for (int i = 0; i< mStatusBarActivities.getChildCount(); i++) {
        if (mStatusBarActivities.getChildAt(i) instanceof ActivityKeyView) {
            ActivityKeyView kbv = (ActivityKeyView) mStatusBarActivities.getChildAt(i);
            if(kbv.getStatusbarActivity().mStackId == stackId) {
                return i;
            }
        }
    }
    return -1;
}
```

该方法的作用是通过stackId来找到在mStatusBarActivities对象中的该Activity所对应的下标；















