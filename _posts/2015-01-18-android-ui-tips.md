---
title: Android UI Tips
date: 2015-01-18 23:30:00
layout: post
---

###Tips:

1. 使用SparseArray代替HashMap等容器类。
2. 能复用时一定要复用，不能复用时尽量复用。复用一切！
3. 使用静态工厂方法去获取对象（使用Message时不要自己去new Message，要使用Message.obtain()方法去获取）这样可以减少不必要的对象，复用已有的对象。
4. 使用一个全局变量代替多个临时变量。
5. 使用Handler、AsyncTask、Loader、IntentService处理异步任务，而非仅使用java.concurrent包
6. 给你的线程一个优先级，如果是处理后台任务的线程，设置他的优先级为Background。
7. 对于ListView的3种状态进行处理，从而优化代码，提升性能。(例如在滑动过程中停止图片的加载，等恢复正常后再继续加载图)

###总结:

1. 不要阻塞Main Thread（UI Thread）
2. 扁平化你的布局层次（能用一个Layout最好，可以使用merge代替FrameLayout）
3. 能复用就尽量复用一切可复用的东西。
4. 延迟加载一些不需要立即使用的内容。
5. 对你的Thread或者Task设置优先级，UI任务和Thread永远是最高级。

###参考:

https://speakerdeck.com/cyrilmottier/optimizing-android-ui-pro-tips-for-creating-smooth-and-responsive-apps