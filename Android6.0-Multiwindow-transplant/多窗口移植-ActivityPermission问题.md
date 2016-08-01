# 多窗口下屏蔽permission
- 多窗口下的一些操作需要特殊的permission，但是系统默认没有给，在5.1中的做法是在WMS中直接注释掉check的那段代码，但是这么做需要在原始代码上面做大量更改，也不利于后期的更改和维护
- 6.0的做法与5.1的做法不同，是在WMS中定义了一个私有变量mFilterPermissions的字符串数组，在checkCallingPermission中进行过滤，只要包含了mFilterPermissions的话就直接返回true，这样的话能尽量减少对于原始的代码的更改，能动态改变数组中的内容来调整屏蔽内容
这样有利于后期的添加和更改
- 改进：wms下checkCallingPermission会调用过程为 

    wms.checkCallingPermission
    ->context.checkCallingPermission->checkPermission
    ->ams.checkPermission
    ->ActivityManager.checkComponentPermission

将WMS中定义了一个私有变量mFilterPermission移动到ActivityManager中，在checkComponentPermission添加权限的过滤（开放了权限）。
