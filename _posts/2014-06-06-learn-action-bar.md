---
title: Learn ActionBar
layout: post
date: 2014-06-04
tags:
---

#ActionBar Overview

#ActionBar Style

#ActionBar Use

#ActionBar Note
+ ActionBar中使用的Icon应当保存在res/drawable目录下
+ showAs属性在API level 11以下是不支持的，需要用户去自定义。
+ 


1.icon类的图标应该保存在res/drawable目录下。
2.showAs 在3.0（API level 11以下）需要自定义这个属性
3.performing Up navigation simply requires that you declare the parent activity in the manifest file and enable the Up button for the action bar
4.在低于API level 11 以下，使用meta-data来声明Activity与其父类之间的关系 
<!-- Parent activity meta-data to support 4.0 and lower -->
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value="com.example.myfirstapp.MainActivity" />
            为了支持4.1以下的版本。
5.API Leve > 11 (3.0)的就不需要Support包了，所以在使用的时候直接就是getXXX方法而不是使用getSupportXXX方法。
6.home的按钮id为android.r.id.home，当点击Up Button时，会调用onOptionsItemSelected().并且传入Up Button的id

7.在使用Up Button 的时候我们可以使用NavUtils这个工具类来简化我们的处理。当你的Activity是从另外的应用开启的时候，你的Activity就会在一个新的task中，这个task是属于你的APP的，你应当处理用户对这个activity的Up Button的点击事件，即实现onOptionItemSelected方法。处理id为android.id.home这个回调。处理过程如下：
    1.调用shouldUpRecreateTask(Context,Intent)方法，来检查你的Activity是否在整个系统的App ‘s stack中存在，如果返回true（需要重新创建，Activity不存在），则使用TaskStackBuilder来创建新的task，否则使用navigateUpFromSameTask()方法跳转到目标Activity。
```java
    //使用方法如下
    case android.R.id.home:
        Intent upIntent = NavUtils.getParentActivityIntent(this);
        if(NavUtils.shouldUpRecreateTask){
            //需要重新创建一个Task，来保存当前Activity的Parent Activity 到新的task stack中
            TaskStackBuilder.create(this).addNextIntentWithParentStack(upIntent).startActivitise();
        }else{
            //不需要的新建一个task来维护当前Activity和它的Parent Activity之间的stack关系，所以直接启动切换到父Activity即可
            NavUtils.navigateUpTo(this,upIntent);
        }
    break;
```
*注意:*为了确保addNextIntentWithParentStack()方法能使得Activity以正确的顺序进入新建的task stack中，需要我们在AndroidMainfest文件中指定好Activity与其父类之间的关系。

8.通过ActionBar Style来自定义ActionBar的样式，背景颜色等。
9.在Support包中，Widget.AppCompat.* 就相当于3.0以上的Theme.holo主题风格。
10.在Fragment中使用Up Button的时候，可以使用FragmentManager将逻辑上的Parent Fragment加入到task stack中，用法如下：
getSupportFragmentManager().beginTransaction().add(xxxFragment,"xxx").addToBackStack().commit();//注意，在commit之前要调用addToBackStack()方法将其放入stack中。
11.只要在AndroidMainfest文件中对`<activity>`标签设置了parentActivityName属性，就会默认设置getActionBar.setDisplayHomeAsUpEnabled(true)

12.ActionBar 可以通过Style来自定义ActionBar的各种属性，实现各种ActionBar的样式。如果我们要对ActionBar进行操作，使它能隐藏或者显示，我们会用ActionBar自带的show或者close。但是show或者close的话都会使得Layout界面占满整个屏幕，界面会重新绘制，并且刷新。但是使用ActionBar overlay的时候它是浮在布局上面的，当隐藏或者显示的时候不会导致界面重绘，可以在布局文件中加入 margin或者paddingTop等android:marginTop="?android:attr/actionBarSize"属性即可使得布局文件不被遮挡住。想实现ActionBar Overlay只需要自定义一下ActionBar的style属性，设置`<item name="android:windowActionBarOverlay">true</item>`即可。