---
title : Android Tools Attribute
date: 2015-03-28 22:29:00
layout: post
---
Android tools提供了一组非常有用的属性方法来方便我们开发，tools所指定的所有属性在打包的时候都不会打包到APK里，它只是辅助开发的一组工具属性，本文基于Android Studio，快捷建使用Mac OSX 10.5+ KeyMap，下面以IDE来表示Android Studio。

##tools : context
使用context属性来告诉IDE，这个布局文件是哪个Activity的布局文件，所以在预览的时候需要根据这个Activity的主题属性来显示，而不是使用默认的主题来进行预览。除了这个作用之外还有另外一点，在IDE中使用Go to Related files（快捷键CMD+Ctrl+Up）功能来帮助找到相关的布局文件。context属性在填写的时候，需要填上Activity的完整包名。
![p1](http://blog.tedyin.me/images/tools_1.png)

##tools:menu
如果你已经使用了context属性指定过了对应的Activity，那么IDE默认会自己去检查OnCreateOptionsMenu里面使用了哪个menu布局，然后在预览的时候加载该布局。使用menu属性可以覆盖上述默认操作。如果不想显示menu则可以设置tools:menu=“”即可。另外还可以使用menu属性定义多个菜单资源，如果不想显示menu则可以给menu属性赋值为空，例如：tools:menu=“”即可。另外，还可以在预览的时预览任意个你想看到的menu菜单项，而不是直接预览menu布局里面的内容。实现这样的效果十分简单，只需要在menu后面写上对应的菜单项的Id并用“ , ”隔开即可。举个例子：
`tools:menu=“action_search,action_add"`
还有一点需要注意的就是，在使用Theme.AppCompat时，menu这个属性不起作用。

##tools:actionBarNavMod
使用该属性告诉IDE我要显示的ActionBar的模式，包含了三个选项standard、tabs、list，该属性只有在Holo主题下有用，在其他主题下无法使用。

##tools:listitem，tools:listheader，tools:listfooter
从名字就可以看出他们的意思，这是一组属性，我们可以使用这些属性分别告诉IDE一下信息:

1. listitem是告诉IDE这个列表里面的Item内容是什么，在预览的时候可以和Item一起预览，而不是傻傻的默认显示一行文字。
2. listheader是告诉IDE这个列表的头部是什么，并一起预览
3. listfooter是告诉IDE这个列表的底部是什么，并一起预览需要注意的一点是，listheader和listfooter对GridView是没有效果的。

##tools:layout
用这个属性告诉IDE，这个Layout在预览的时候应该显示什么东西，而不是显示一片空白。
![p2](http://blog.tedyin.me/images/tools_2.png)

##补充：
1. tools:text属性用来显示要预览的文字，预览时会覆盖text属性的内容。
2. tools:src属性用来显示要预览的图片资源。

#tools 中对Lint处理的属性
##tools:ignore
该属性对那些有“强迫症”的患者非常有用，经常我们在布局文件中写完ImageView后，都会提示少了Description，每次看见那个小黄线的提示就会觉得非常别扭，但是有了这个属性，就不用担心了，只要在ImageView的属性设置后面加上tools:ignore=“contentDescription”即可立马解决问题。除了上述用法外，还可以用在别的想忽略警告的地方。

##tools:targetApi
这个属性从名字就可以看出是用来指定API的。如果我们的minSdk是11，但是我们用了一个在API 14后才有的控件，在布局编辑器里就会报错，那么在报错的控件下面使用targetApi并指定对应的API即可解决问题。
{% highlight xml %}
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
android:color="@color/accent_color"
tools:targetApi="LOLLIPOP" />
{% endhighlight %}
好了，tools的用法基本就介绍完了，详细的列表可以[参考这里](http://tools.android.com/tech-docs/tools-attributes)
##参考
[英文](https://medium.com/sebs-top-tips/tools-of-the-trade-part-2-b91271892d10)
[中文版](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0309/2567.html)

