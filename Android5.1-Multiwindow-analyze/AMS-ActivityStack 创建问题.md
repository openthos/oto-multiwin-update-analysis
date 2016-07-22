### ActivityStack 数量的问题

- ActivityStack可以有多个也会有多个。但在正常的单窗口Android中，会有一个homeStack,还有一个普通的应用使用的Stack。
- 生成新的ActivityStack，条件是除了homeStack，没有其他的Stack可用用。在开机启动第一个应用会新建一个ActivityStack，还有一个情况是，一个ActivityStack内的应用发生***崩溃错误***等等，就会先建一个Stack.
- TaskStack 创建会是使用ActivityStack的mStackId,他们之间会有意义对应的关系。

### 5.1 multiwindow 与 6.0 aosp，ActivityStack创建

- 在5.1 multiwindow中启动Activity过程中新建或查找ActivityStack函数为ActivityStackSupervisor.adjustStackFocus

- 在6.0 aosp 中启动Activity过程中新建或查找ActivityStack函数为ActivityStackSupervisor.computeStackFocus

- 函数名发生改变
- 部分函数发生微小改动（ 创建Stack函数reateStackOnDisplay 将返回一个stack对象而不再是stackId），主要的步骤没有太多变化。

### 具体函数 adjustStackFocus & computeStackFocus

> Tieto，multiWindow的做法是，用一个flag（isMultiwindow）作为判断，若在多窗口状态下阻断了查找可用非HomeStack的stack的操作，强制新建一个stack。

> 个人认为 直接阻断方式有点过于粗暴，task显得有点没有意义，破坏了原来android多级ActivityRecord管理结构，可以为stack设置一个标志来标识是否被使用，保留查询可以stack的操作。

      if (!isMultiwindow) {
            ......
          /*查找可以用的非HomeStack*/
          final ArrayList<ActivityStack> homeDisplayStacks = mHomeStack.mStacks;
          for (int stackNdx = homeDisplayStacks.size() - 1; stackNdx >= 0; --stackNdx) {
              final ActivityStack stack = homeDisplayStacks.get(stackNdx);
              if (!stack.isHomeStack()) {
                  if (DEBUG_FOCUS || DEBUG_STACK) Slog.d(TAG,
                          "adjustStackFocus: Setting focused stack=" + stack);
                  setFocusedStack(stack.mStackId, "For single window in adjustStackFocus");
                  return mFocusedStack;
              }    
          }    
      }
      /* Tieto多窗口情况下上面不会被执行，下面新建stack就会必然被执行*/
      int stackId = createStackOnDisplay(getNextStackId(), Display.DEFAULT_DISPLAY, isMultiwindow);


      /* 在6.0 如下，没有太大差别*/
      stack = createStackOnDisplay(getNextStackId(), Display.DEFAULT_DISPLAY);
