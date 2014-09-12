---
layout: post
title: Theme And Style
date: 2014-09-08 20:34:00
tag: - Android
disqus: 'y'
share: y
categories: [Android]
---

##Theme和Style的区别
+ Theme是应用到一个Activity或者整个Application上的Style，而不是应用于某个View上，应用于某个View上的叫做Style。
+ Theme是针对窗体级别，改变窗体样式的，Style是针对窗体中的UI控件的，用来控件或者Layout的样式。
+ Theme和Style在定义的时候是一样的，都是定义在`/res/values/`目录下。
+ 每个`<style>`都可以被应用到Application/Activity或者应用到某个View，应用到App的时候就叫Theme，应用到View的时候就叫Style。

##定义一个Style
> 在`<resource>`下的每个子节点 ，在编译的时候都会被转化为对象，通过他们定义的Style的名字来引用。


如果想自定义一个Style要从何下手呢？完全从头做起？那你就错了，在定义Style的时候，不需要从头做起，
只需要继承Android提供的Style，并且对你需要自定义的属性进行修改即可。

例如：继承TextView的默认Style，并对其进行修改

```
<style name="GreenText" parent="@android:style/TextAppearance">
        <item name="android:textColor">#00FF00</item>
</style>

```

如果你想继承自己定义的Style，而不是系统默认的Style的话，你可以直接在自定义Style名后面加上“.”再
加新的属性名即可，不需要再去写`parent`。例如：创建一个新的Style并继承上面自定义的GreenText

```
<style name=“GreenText.Big">
        <item name="android:textSize”>30sp</item>
</style>
```

##Style的属性（Properties）
从上面的介绍知道了如何自定义一个Style，你只需继承Android默认的Style并且重写其中你需要自定义的字
段，那么都有哪些自定义的字段是可以重写的呢？你可以从`R.attr`获得所有可以重写的属性信息。但是不是所
有的`R.attr`中的属性都适用于某个指定的View，你需要参考指定的View的属性信息来确定哪些信息是可以被
重写的。如果你给一个View指定了一些它不支持的属性，他会自动忽略这些属性。有些属性不适合于任何View，
他只对Window有效这些属性只能用作Theme的属性去使用，如何区分哪些是对View有效哪些对其无效呢？
在`R.attr`中所有以`Window`开头的属性都是对View无效的，只能用做Theme属性去使用，其余的可以
用View的属性。

##Style的应用
应用Style的方式有两种:

1. 对于一个独立的View使用Style，只需要在View的布局文件中加入`style=“@style/xxxStyle”`即可。
2. 对整个Application或者 Activity使用Style(这个Style就是Theme)，只需要
在Android manifest文件的<application>或者<activity>标签内加上`android:theme`属性即可。

当Style应用给一个View的时候，这个Style只会对这个View有效，如果这个View是一个ViewGroup的话，
那么也仅仅是对这个ViewGroup这个控件有效，对于ViewGroup内部的View是没有任何效果的。如果想对这
个ViewGroup中的所有View都有效的话，那么应该将这个Style当做Theme来使用，而非Style来使用。
(当Theme使用的意思就是将这个Style应用到这个ViewGroup所在的Activity或者整个Application)

##Theme的选择
在选择使用什么样的Theme的时候，需要根据系统所支持的版本来确定，高版本的系统中会提供一些Theme是低
版本中不含有的。因此为了对各个版本兼容，Android在res目录下生成了多个values目录来提供对不同版本的
兼容。

举例如下：
假如当前版本为3.0以下，我们可以在res/values目录下定义style.xml:

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
     ···
    <style name="LightThemeSelector" parent="android:Theme.Light”>
          ···
    </style>
     ···
</resources>
```

假如当前版本为3.0~4.0之间我们可以在res/values-v11目录下定义style.xml:

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
     ···
    <style name="LightThemeSelector" parent=“android:Theme.Holo.Light”>
          ···
    </style>
     ···
</resources>
```

Holo主题是在API 11后提供的。如果在API 14以上，我们可以在res/values-v14目录下定义。
这样就可以很好的兼容多个版本，保持视觉上的统一。

##如何去引用资源

引用可以通过：@、？来引用。那么这两者的区别在哪里呢？“?”主要用来引用私有资源，“@"主要用来引用公有
资源。因为Android的资源Style等之间存在着继承关系，因此”?”就相当于类中的”this”,而”@“则相当于一
个公共的对象（R），来对资源进行引用。通常”?”引用的资源都是当前包(目录)中的，而”@“引用的资源既可以
是当前目录中的也可以不同目录中的。在对Android属性继承修改的时候，我们可能需要别的属性，如果该属性
在父类中不存在的话，那么我们可以在`res/values/attrs.xml`中通过`declare-styleable`标签来定
义我们所需要的属性资源以及这些属性资源的`format`格式，然后就可以在Style文件中直接使用。

##资源
[Android的Style资源](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/styles.xml)

[Android的Theme资源](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/themes.xml)

[Android的属性资源](http://developer.android.com/reference/android/R.attr.html)

[可以在Theme中使用的属性](http://developer.android.com/reference/android/R.styleable.html#Theme)

{% highlight ruby %}
def whaaa
  puts "I have a friend called Bobobmob."
end
{% endhighlight %}


{% highlight xml %}
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
     ···
    <style name="LightThemeSelector" parent=“android:Theme.Holo.Light”>
          ···
    </style>
     ···
</resources>
```
{% endhighlight %}