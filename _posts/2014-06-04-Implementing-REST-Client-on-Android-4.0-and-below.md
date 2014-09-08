---
title: Implementing REST Client on Android 4.0 and below
layout: post
date: 2014-06-03 22:19:00
tags: 
---

#General Overview

REST Client 是一个非常好的处理客户端与服务端之间交互的框架模型，他的机构如下：

![framework](http://tedyin.me/images/framework.png)

他的最佳实践有以下几点：

+ 使用`Loaders`相关的类来进行异步加载
+ 使用`Service`
+ 使用`ContentProvider`通过他可以使得我们更方便的去访问数据库，`ContentProvider`是Android提供的对外暴露数据的接口，使用它可以很好的将我们的数据层和应用层进行隔离。`ContentProvider`不是线程安全的，因此我们需要使用`Loader`框架来配合使用`ContentProvider`。

# REST With Loaders
Loaders的特性：
+ 可以被Activity和Fragment来运行，并且所有Loaders的状态都是由LoaderManager来管理，因此我们不用担心Loaders的使用，他的生命周期是受系统管理的。
+ 一般的REST调用都是一次性的，一旦你的Activity被放到background，你的REST调用可以被结束。

REST 调用一般都是网络请求，也就是说一般都会是很耗时的操作，所以不应该放到主线程中进行，而是使用异步线程来执行网络操作，我们可以使用`AsyncTaskLoader`来实现这一目标，因此我们需要一个` RESTLoader`继承自A`AsyncTaskLoader`， 我们可以通过`LoaderManager`在Activity和Fragment中来调用`RESTLoader`。

#Start Our Project


