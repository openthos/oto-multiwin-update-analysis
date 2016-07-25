# Android6.0-multiwindow 应用边框缩放实现

## 添加资源
- 在frameworks/base/core/res/res/drawable下面拷入资源文件
point_arrow_lean14.png ；
point_arrow_lean23.png ；
point_arrow_left_right.png ；
point_arrow_up_down.png ；
pointer_arrow_lean14.xml ；
pointer_arrow_lean23.xml ；
pointer_arrow_leftright.xml ；
pointer_arrow_updown.xml

- 在frameworks/base/core/res/res/values 目录下面的attrs.xml；styles.xml中根据资源文件作出相应的更改

## 添加 native method 来更改鼠标的icon

### android_view_PointerIcon.h
- 定义了当鼠标点击或者hover边框时鼠标样式的更改，定义了四种类型，POINTER_ICON_STYLE_ARROW_UPDOWN（上下）、POINTER_ICON_STYLE_ARROW_LEFTRIGHT（左右）、POINTER_ICON_STYLE_ARROW_ONEFOUR（左上右下）、POINTER_ICON_STYLE_ARROW_TWOTHREE（右上左下）
在com_android_server_input_InputManagerService.cpp会使用到

### libs/input/PointerController.h
- 新添加五种spriteIcon的类型arrowNormal、arrowUpdown、arrowLeftright、arrowOnefour、arrowTwothree

- 定义了pointerIconChange(int type)函数

### libs/input/PointerController.cpp
- 定义了ICON_CHANGE_TYPE标识鼠标icon的类型

- 实现了pointerIconChange函数来对鼠标的Icon类型进行切换，主要是对ICON_CHANGE_TYPE的赋值，然后调用updatePointerLocked进行真实的操作

- 更改了updatePointerLocked函数，添加了新的type的判断，设置icon

### services/core/jni/com_android_server_input_InputManagerService.cpp
- 对于loadPointerResources函数的更改，添加了添加了对于添加的鼠标资源的load添加

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### core/java/android/hardware/input/IInputManager.aidl
- 定义一个setPointerIcon(int type)方法，根据type的不同来设置pointer的icon

### core/java/android/hardware/input/InputManager.java
- 对于setPointerIcon的实现

### core/java/android/view/PointerIcon.java
- 定义了当鼠标点击或者hover边框时鼠标样式的更改，定义了四种类型，上下、左右、左上右下、右上左下，并根据这几种类型返回指定的资源文件

### services/core/java/com/android/server/input/InputManagerService.java
- 添加了nativeSetPointerIcon用于调用C++层的代码，设置PointerIcon

- 添加了setPointerIcon方法，方法主要是调用nativeSetPointerIcon函数

- 添加nativeSetPointerIcon函数

- 对gInputManagerMethods[]的更改