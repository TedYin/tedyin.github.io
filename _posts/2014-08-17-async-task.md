---
title: AsyncTask的执行过程
layout: post
date: 2014-08-17 18:50:00
---
##AsyncTask是什么？

AsyncTask是Android系统提供的用来执行异步任务的工具类，它提供了一系列可覆盖的方法，可以对任务执行的各个时期进行操作。

##AsyncTask是如何实现UI线程与非UI线程切换的？

AsyncTask内部封装了一个Handler，和一个ThreadPool，具体的任务交给ThreadPool中的线程来处理，并且使用Handler来通知UI线程将异步处理的结果更新到UI上去。

##AsyncTask的执行过程是怎样的？

+ 在执行一个AsyncTask之前我们需要创建一个AsyncTask的对象，创建AsyncTask对象asyncTask。
+ 创建好对象之后我们调用AsyncTask对象的execute方法来启动AsyncTask这个异步任务。
+ 这里需要注意的是AsyncTask必须在UI线程中启动。

AsyncTask启动后它首先会在UI线程中调用`onPreExecute()`方法，来做一些启动异步任务前的准备工作，比如说显示Loading动画等。接下来会调用sDefaultExecutor会调用它自己的execute方法并把FutureTask对象传入。在sDefaultExecutor的execute()方法中，会先将FutureTask对象封装成一个Runnable对象加入到一个ArrayDeque<Runnable>队列中等待调度（防止多线程并发一起的重复调用），接下来从ArrayDeque取出FutureTask然后交给其内部自定义的一个线程池（TPE）来处理这个Runnable任务。TPE拿到这个Runnable任务后，交给其WorkerThread然后异步的运行这个Runnable。其中sDefaultExecutor的实现代码如下：
![sDefaultExecutor](http://tedyin.me/images/sDefaultExecutor.jpg)

AsyncTask在new一个Task的时候，会创建一个FutureTask，在这个FutureTask中会传入一个实现了Callable接口的对象，在这个Callable对象的call()方法中才会真正调用doInBackground()这个方法，在doInBackground()方法执行结束后，会返回处理的结果Result（泛型），然后调用AsyncTask的私有方法postResult()来传递Result到UI线程中。该方法内部先将Result和AsyncTask对象包装成AsyncTaskResult对象，然后通过Handler将该对象传递到UI线程，然后在UI线程中调用onPostExecute(Result result)这个被重载的方法进行UI处理。

postResult()的方法如下：
![postResult](http://tedyin.me/images/postResult.jpg)

其中AsyncTaskResult的实现如下：
![asyncTaskResult](http://tedyin.me/images/asyncTaskResult.jpg)

其中封装了AsyncTask和Result对象，另外一个就是sHandler，它是在AsyncTask初始化的时候就直接生成的静态对象，sHandler的实现如下：
![sHandler](http://tedyin.me/images/sHandler.jpg)

在Handler中(UI线程中)调用mTask.finish()方法结束AsyncTask,该方法的实现如下：
![finish](http://tedyin.me/images/finish.jpg)

可以看出在该方法中调用了方法onPostExecute(Result result)，这样AsyncTask就执行结束了，这就是整个AsyncTask的整个执行过程！

注：在FutureTask在被创建时会重写父类的done()方法