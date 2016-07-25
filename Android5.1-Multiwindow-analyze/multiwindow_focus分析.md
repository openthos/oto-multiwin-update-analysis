# Multiwindow 中focus的实现

## ActivityStackSupervisor.java

### setFocusedStack函数
- 添加了一个setFocusedStack方法，Android5.1中在这个文件中也有同样的一个setFocusedStack方法，更改了原生系统的setFocusStack函数，新添加了一个setFocusStack函数但是参数不同，系统原生的这个方法最后只是调用了moveHomeStack，只是对home的focuse或者是失去focuse,没有focuse其它的activity的操作，
multiwindow中新加的这个方法就是对一般的Activity的focuse

- 最开始的方法是取得每一个显示屏的所有ActivityStack，然后一一比较其stackId是否与要focuse的id一致，如果一样就把对应的ActivityStack放到ActivityStack list的末尾

- 目前multiwindow中在这其中还做了对于startupMenu的判断，当focus其它的Activity的时候要关掉startupMenu，这么做使整个方法变得更加复杂（个人觉得可以更改startupmenu的写法）不需要在这里做更改

- 多处调用了setFocusedStack

### getFocusedStack函数
- 添加了判断

### getLastStack函数
- 添加了判断

## DisplayContent.java

### stackIdFromPoint
- 更改了这个函数中的代码，添加了判断，主要是设置了mHomeHasFocus的参数jjjjjjjkk

- 如果是普通的Activity的话多执行了三个函数分别是WindowManagerService中的rebuildAppWindowListLocked、prepareAppTransition、executeAppTransition，做了Activity的切换动作（个人觉得在这里就执行切换动作很奇怪？）

### isHomeStack
- 添加了一个isHomeStack方法，判断displayContent处于homeFocused，在WMS中的findFocusedWindowLocked使用到，做了特殊的处理（这个方法和处理需要着重分析）

## ActivityManagerService.java

### mFocusJustChanged
- multiwindow中添加了一个新的变量，用来标识是否focus刚刚改变了，并有一个setFocusJustChanged，这个方法在ActivityStackSupervisor中的setFocusedStack(int stackId, String reason)方法中使用到了（具体作用?）

### setFocusedStack函数
- 对函数进行的更改，添加了一个mFocusJustChanged的判断，并添加了是否是isHomeActivity的判断，多调用了moveTaskToFront函数

## PhoneWindow.java

- 定义一个 setFocusedStack函数，其函数体的本质是调用了

## ActivityStack.java

- 在moveTaskToFrontLocked中调用了ActivityStackSupervisor 的setFocusedStack

## WindowManagerService.java
- 对于TAP_OUTSIDE_STACK情况下，源码中注释掉了里面的方法，这里反注释了，通过x，y坐标获得StackId，然后调用AMS的setFocusedStack方法

