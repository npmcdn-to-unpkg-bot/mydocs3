# 移动webapp介绍

% hdm258i@gmail.com | 2013-03-29 | 简介, webapp框架

## 前言

近年来，HTML5技术大行其道，特别是在`移动平台`（iOS和Android）上，基于`webkit内核`的浏览器
对HTML5支持力度越来越大，越来越多的web站点开始使用HTML5技术。使用HTML5技术，使web技术的发挥空间得到了扩张，
也使web产品发生变革。`移动webapp`就是这一阶段催生的典型的web产品类型。

本篇试图通过一些阐述，介绍移动webapp概念，以期大家能够获得对移动webapp的统一认知。
另，本系列文章总是focus在移动端，简便起见，后文用`webapp`代替。

## 1. 大家是怎么理解的

在日常的开发工作中，接触过一些产品人员、开发人员，在进行沟通时，经常存在对webapp理解的不同步，主要表现在：
* webapp和`HTML5`的关系
* webapp与`普通HTML5站点`又有什么区别

以上两个方面的理解没有达成一致，就会存在各种模棱两可的理解：
* 通过web技术实现的都是webapp: 那么精简版（WML）算不算？标准版算不算？触屏版算不算？高端版呢？
* 只要使用了HTML5技术开发的网页都是webapp：我的站点使用了`CSS3圆角`，所以是webapp；
    我的站点使用了客户端缓存`localStorage`，所以是webapp；等等

在没有给出统一化概念的时候，以上问题总是无法辨真，因为`公说公有理，婆说婆有理`。

## 2. 什么是webapp

webapp不同与普通网页，它具有以下特征
（摘自: http://www.html5rocks.com/webappfieldguide/know-your-apps/intro/）：

1. `用户体验`。让用户能很容易完成任务，并利用了设备本地的一些功能
2. `界面设计`。丰富的视觉体验，又不会分散人的注意力。注重美学，使用和本地应用一样的设计模式，又不失易用性
3. `交互方式`。注重用户的交互、参与和完成任务，而不是让他们仅仅浏览网页。
    应用程序是`自包含`（self-contained）的，也即用户不用导航到其他站点或者应用来完成任务。

以上数点还是不好界定，可以用以下方法来判定一个站点是否webapp：

`一个站点如果具有绝大部分以下特性，就可以认为其是一个webapp`：

* `自包含`：不用切换至第三方站点，一站式满足用户需求
* `交互体验`：局部刷新，平滑切换等
* `全屏模式`：桌面图标点击，打开以后可以占满屏幕（iOS）
* `类本地UI界面设计`：UI类似native app
* `离线工作`：即使没有网络，仍然能够正常打开
* `设备功能调用`：GPS、传感器等
* `不使用传统导航元素和链接`
* 应用设计使用`富客户端架构`模型（JS密集型）

综上，可以这么来定义webapp：webapp是一组特性的集合体，当一个站点具有特性
达到一定数目（`不小于6`），才可以认为该站点是一个webapp

## 3. 有哪些webapp

使用6作为`特性阈值`，以下站点是webapp（分析时间：`2013-03`）：

1. `mail.google.com` 6
2. `rocket.ft.com` 6
3. `m.baidu.com/ios` 6
4. `pd.cmread.com` 7
5. `m.baidu.com/video` 6
6. `m.baidu.com/news` 6
7. `map.baidu.com/mobile` 6

