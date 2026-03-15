---
title: "Android Module"
description: 
date: 2026-03-14T10:04:26Z
image: 
math: 
license: 
comments: true
build:
    list: always    # Change to "never" to hide the page from the list
---

# Android四大组件学习

Android的四大组件包括：Activity（活动）、Server（服务）、BroadcaseReceiver（广播接收器）、ContentProvider（内容提供者）。

![module](module1.png)

1. Activity：通俗来讲其实就是App上面用户看到的每个界面，Activity组件负责**界面展示、处理用户交互、进行数据传递**等
1. Server：无界面的后台组件，用于执行**长期执行**的操作。就比如说应用商城后台下载的东西、后台播放网易云音乐等
1. BroadcaseReceiver：手机里的“消息喇叭”，用于**监听系统或者应用发出的全局事件**的组件，比如说网络状态的变化，充电状态的变化等
1. ContentProvider：应用间的“数据共享存储桥”，是**管理跨应用访问**的组件，通过URL来标识数据，比如说我们的手机通讯录是不是可以被多个应用访问读取

## Activity(活动)

### 1.什么是Activity
    Activity通俗来讲就是用户所看到每一个页面，是Android应用中用来展示用户界面的组件，我们可以通过它来进行应用程序的交互。

Activity可以想象成手机的每一个“页面”。比如当你打开一个App时，你看到的第一个界面就是一个Activity，这时你点击某一个按钮跳转到另一个界面就是另一个Activity。每一个Activity都是一个独立的界面，负责用户的交互和展示内容。

