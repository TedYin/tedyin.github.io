---
title: Android View Lifecycle
date: 2015-03-11 21:00
layout: post
---

在Android开发中了解各个组件的生命周期非常重要，网上已经有许多关于Activity、Fragment等的生命周期的介绍了，今天来介绍一下View的生命周期。

##什么是View
先来看看官方文档的解释：
> This class represents the basic building block for user interface components. A View occupies a rectangular area on the screen and is responsible for drawing and event handling. View is the base class for widgets, which are used to create interactive UI components (buttons, text fields, etc.). The ViewGroup subclass is the base class for layouts, which are invisible containers that hold other Views (or other ViewGroups) and define their layout properties.

也就是说View是最基础的UI组件，所有的其他的UI组件都是从View类中衍生出来的，可见View的地位是多么的高，所以需要掌握View的各个细节。

##View生命周期中用到的方法

###1. View的创建期：
使用View的构造函数进行创建，在这个过程中，如果有自定义的属性的话，也需要在这个期间进行处理。当View和他的子View全部从XML中inflate结束后，会调用`View#onFinishInflate()`方法。

###2. View的布局过程
整个布局过程做的就是处理View的大小和位置相关的信息。
`View#onMesure()`方法的作用就是计算每个控件在屏幕上的尺寸大小。`View#onLayout`方法的作用就是设置所有控件的大小和位置。
`View#onSizeChanged`方法的作用是当View的大小改变会调用此方法。

###3. View的渲染
View的渲染过程会调用onDraw方法。

###4. View对事件的处理
处理View事件方法有`View#onKeyDown()`当设备的物理按键按下后会触发该方法；`View#onKeyUp()`当设备的物理按键弹起的时候就会触发该方法；`View#onTrackballEvent()`当轨迹球被触动的时候会触发该方法；
`View#onTouchEvent()`当设备的屏幕有来自用户的触摸操作时会回调该法，比如某些滑动操作我们就可以在该方法中拦截处理，可以根据用户的不同滑动距离和滑动速度等用户操作，给出许多不同的反馈，提供更好的用户体验。

###5. View对焦点的处理
处理焦点相关事件的回调方法为`View#onFocusChanged()`当控件的焦点发生改变，会触发该方法。

###6. View在根节点上的挂载和删除
当View通过构造函数创建出来后，如果不挂载到Window上时，是无法显示出来的。当View要挂载到Window上时会调用`Veiw#onAttachedToWindow()`方法；当View被销毁后要从Window上去除时会调用`View#onDetachedFromWindow()`方法；当Window隐藏或者可见时会调用`View#onWindowVisibilityChanged()`方法。通过这几个方法我们可以处理一些初始化的操作，和一些在View被销毁后进行的内存回收或者善后的工作。

##View的生命周期示意图
![view_lifecycle](http://blog.tedyin.me/images/view_lifecycle.png)