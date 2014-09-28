---
layout: _post
date: 2014-09-28 19:08:00
title: Android 文件资源的适配
---


现在的Android系统运行在各种尺寸的机器上，对于开发者，我们需要让我们的应用适应这些大大小小的屏幕确实比较困难，但是Android给我们提供了一套简单而且方便的机制来帮助我们完成适配，首先我们来看看资源文件的组织方式。

##组织资源文件
众所周知资源文件都存放在`res/`目录下的各个文件夹中，这些文件夹都有着各自不同的意义。

+ `animator/` 存放属性动画(property animation)
+ `anim/` 存放Tween动画
+ `color/` 存放颜色状态的xml文件（即：按钮按下什么颜色，正常什么颜色之类的selector文件）
+ `drawable/` 存放图片文件(.png/.9.png/.gif/.jpg/.xml)xml可以为帧动画的animation-list文件，也可以为shape类型文件
+ `layout/` 存放布局文件
+ `menu/` 存放Options Menu 或者 Context Menu 或者Sub Menu
+ `raw/` 存放多媒体文件，该文件夹下的文件在生成APK时不会被压缩成二进制，会保存其原有的格式。这点类似于`asset/`文件夹。
+ `values/` 该文件夹下存放了App中核心的数值文件，包括`strings.xml`,`style.xml`,`array.xml`,`integers.xml`,`attrs.xml`,`colors.xml`,`dimens.xml`等APP中需要的颜色样式，距离数值字符串等文件，具体的样式类型可参考具体的Resource部分。
+ `xml/`该文件夹下存放Runtime中能获取的或需要的xml类型的各种文件，可以通过`Resources.getXML(int resId)`来获取该文件的解析器`XmlResourceParser`对象。

> `asset/`与`res/raw`下的文件在生成APK包时，都不会被压缩。他们的区别是
> + `asset/`文件夹下的文件不会在R.java文件中生成映射，因此不能通过Id来引用，但是`res/raw/`下的文件可以在R.java下生成映射，可以通过Id进行索引。
> + `asset/`文件夹下可以再继续添加文件夹，但是`res/raw/`下不许再出现子文件夹。
> + `asset/`下的文件或文件夹通过`AssetManager`类来访问，`res/raw/`文件夹下的文件通过`Resources.openRawResource(int resId)`来访问。



##适配不同的屏幕

上述说明了资源文件的基本结构，上述的结构中是Android资源文件组织的最基本结构，如果要适配不同的屏幕，就需要在上述基础上使用更复杂的文件结构来适配不同的布局。

1. 在`res/`下创建`<resources_name>-<config_qualifier>`这种格式的文件夹，`<config_qualifier>`的属性可以添加多个，中间使用`-`隔开即可,但是要注意分类的顺序，大类属性在前，小类属性在后即可(大类属性中包含了小类属性)，前面的属性包含后面的属性举例如下:

{% highlight xml%}
res/
    drawable/   
        icon.png
        background.png    
    drawable-hdpi/  
        icon.png
        background.png  
    drawable-hdpi-landscape/  
    	icon.png
    	background.png
{% endhighlight%}

上述`drawable`就是规定格式中的`<resources_name>`，`hdpi`就是规定格式中的`<config_qualifier>`。只要按照规定格式`<config_qualifier>`部分的内容可以自定义，但是不推荐这样做。

> 使用了所有的`<config_qualifier>`的资源文件夹命名，排列的顺序是按照优先级排列的，结果为：
values-mcc310-en-sw320dp-w720dp-h720dp-large-long-port-car-night-ldpi-notouch-keysexposed-nokeys-navexposed-nonav-v7
> 具体的解释下:
values-mcc310(sim卡运营商)-en(语言)-sw320dp(屏幕最小宽度)-w720dp(屏幕最佳宽度)-h720dp(屏幕最佳高度)-large(屏幕尺寸)-long(屏幕长短边模式)-port(当前屏幕横竖屏显示模式)-car(dock模式)-night(白天或夜晚)-ldpi(屏幕最佳dpi)-notouch(触摸屏模类型)-keysexposed(键盘类型)-nokey(硬按键类型)-navexposed(方向键是否可用)-nonav(方向键类型)-v7(android系统版本API level 7)

2. 多套资源的命名必须和默认的(drawable、layout等)相同。具体的文件命名和组织结构可参考[不同资源的命名和组织](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)


##为多个尺寸屏幕提供最好的适配
为了适配大多数的机型，在组织App的资源文件时必须提供一套默认的资源数据，这样做的目的就是在无法找到合适的`<config_qualifier>`数据的时候可以使用默认的资源，而不至于找不到资源文件而崩溃，在API level 4以后，drawable资源可以不需要指定默认的资源文件，因为Android会根据屏幕的信息自己找到合适的图片资源并进行必要的缩放，但是此处还是推荐大家指定所有的默认资源！

##Android是如何找到最合适的资源文件的

如果提供了多套资源文件时，Android在运行时会根据设备的属性找到合适的资源文件来使用。Android查找合适的资源文件的过程是一个循环排除的过程，它会读取一堆设备的属性，然后根据这些属性循环过滤所有的资源文件，最后只剩下唯一的一个即为最优的资源文件。对于图片资源的查找，如果找不到最合适的，它会找接近最优解的资源，如果有两类图片资源都是次优解，一类是低像素的图片，一类是高像素的图片，Android会采用将高像素的图像缩小的策略来适配，而不会采用将低像素的图片放大的操作。因此理论上只要提供一套`xxxhdpi`的图片即可。但是一定要注意一点，选取图片像素高的进行放大，并不是说我们只用一套`xlarge`的图像就可以适配所以机型了。因为Android在排除资源的时候，如果当前的机型的屏幕类型是Normal Screen，而资源文件都是`xlarge`类型的没有Normal类型的，那么系统也不会使用这套资源，这样会导致程序崩溃，提示资源不可用或者无法找到等错误，因此在提供多套资源的时候尽量不要省略，一定要提供给默认的资源。

循环排除算法的流程图：
[循环排除流程图](http://blog.tedyin.me/images/201409182210.png)


##参考
[raw和asset的区别](http://www.cnblogs.com/leizhenzi/archive/2011/10/18/2216428.html)
[资源文件的组织](http://developer.android.com/guide/topics/resources/providing-resources.html)
[文件夹属性的使用](http://ivan-ru.iteye.com/blog/1711414)
