# 给Activity添加多边框

## 资源的移植
1. 把资源放入到core/res/res中，包括drawbel、layout中，并在values中添加id和dimen、color等，
   这个可以根据最早的git来添加

2. 在layout目录中，根据多窗口要新创建一些用于初始化mContentRoot的xml文件

## Activity View层次变化
原生Android5.1-x86

![android5 1-x86activity](原生Android5.1-x86Activity层次.png)

Android6.0-x86改进

![android6 0 -x86activity](原生Android6.0改进-x86Activity层次.png)

更改原理及其办法
- 基本原理就是在mContentRoot下添加了多窗口的header，并在mContentRoot的linearlayout中添加多边框等

- 在PhoneWindow.java中对于mContentRoot的inflate是根据不同的情况选择不同的xml，那么在选择不同的resourceId的时候根据判断选择不同的xml，在原始的xml中添加多边框的组件形成一个新的xml，那么在多窗口模式下只需要添加新文件并在原始文件中更改少量的代码即可
