# AMS调用架构

这里主要介绍的是AMS调用的架构和流程，简述了一个小的获取AMS服务的流程

![ams -](pix/AMS调用结构.jpg)

从图中可以看出这里用的是代理者模式
需要注意的是ActivityManagerProxy 与 ActivityManagerNative都在同一个文件中，都在ActivityManagerNative.java中