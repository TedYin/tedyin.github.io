---
title: Android 事件传递机制
layout: post
date: 2014-08-03 21:51:00
---

1. Android中所有的事件都是基于“按下（ActionDown）”这个动作之后的。
2. Android中所有的视图都继承自View，通过各种布局组件(ViewGroup)来对View进行布局。所有的“UI控件(Button，TextView等)”都是继承自View，所有的“布局控件(RelativeLayout等)”，“容器控件(ListView等)”都是继承自ViewGroup，ViewGroup是继承自View的。

事件主要在ViewGroup和View之间进行传递，他们处理对事件的方法如下：

View：
          public boolean dispatchTouchEvent(MotionEvent event)
          public boolean onTouchEvent(MotionEvent event)
ViewGroup:
          public boolean dispatchTouchEvent(MotionEvent event)
          public boolean onTouchEvent(MotionEvent event)
          public boolean onInterceptTouchEvent(MotionEvent event)

这些方法中都会接收一个MotionEvent对象，该类继承自InputEvent，MotionEvent是用户的动作行为的封装类，我们可以通过MotionEvent对象内部的信息就可以获取用户的行为。

以上三个方法的作用：

+ dispatchTouchEvent：用于分发事件，Android中所有的事件都是由这个方法进行分发的，返回true表示事件被消费，不继续分发，返回false则继续往下分发。如果事件分发给ViewGroup，则是交给onInterceptTouchEvent方法判断是否需要拦截该事件。

+ onTouchEvent：用来处理事假，返回true表示消费了事件，事件将不再向下传递。


+ onInterceptTouchEvent：它是ViewGroup中才有的方法，它的作用就是拦截事件，返回true表示事件被拦截，将不再向下传递。而是交给自身的onTouchEvent处理。该方法默认返回false，即不进行拦截。

Touch事件的传递过程：
     先由Activity的dispatchTouchEvent分发给具体的UI组件(比如被点击的Button)，然后由TargetView的dispatchTouchEvent再进行分发，然后由自己的OnTouchEvent处理，在处理过程中，先调用注册在自己身上的onTouchListener对象中的onTouch方法。然后在执行OnTouchEvent方法，当OnTouchEvent执行完毕后，会调用View的onClick方法，该方法是在OnTouchEvent方法中处理ACTION_UP的这个分支中处理的performClick方法中调用的。
     因此onTouch会在onClick之前调用，onClick方法会在ACTION_UP的时候调用，而不是在ACTION_DOWN的时候调用。执行了onTouch方法的时候如果返回true时dispatchTouchEvent也会返回true，事件就不会再往下执行了，也就不会再去执行onTouchEvent方法了，也就不会再去执行onClick方法了。

事件传递的流程图：
![event](http://tedyin.me/images/charflow.jpg)


对于嵌套布局的事件传递是：
1. Activity —>  ViewGroup   —>  View

2. onTouch事件是在dispatchTouchEvent中调用，onClick方法是在onTouchEvent中调用，onTouchEvent是在目标View的onTouchListener不存在或者返回了false的前提下才会被dispatchTouchEvent方法调用。

3. onInterceptTouchEvent方法的作用是拦截MotionEvent，它是ViewGroup特有的方法，如果在ViewGroup中它返回true表示事件被拦截，那么事件就不会再传递到View中去了。表现的现象就是你点击Layout中的一个Button但是调用的却是Layout中的onClick方法。

常用控件之间的继承关系：
![overried](http://tedyin.me/images/over.jpg)
