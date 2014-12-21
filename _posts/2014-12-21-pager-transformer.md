---
layout: post
title: ViewPager PageTransformer 应用
date: 2014-12-21 21:07:00
---

最近项目中要有个需求就是做出一个可以滑动的列表，类似于Paper效果的动画，期初设想的比较复杂，自定义监控ViewPager的OnScroll事件，然后再在onPageScrolled中根据`position`, `positionOffset`, `positionOffsetPixels`等数据就算出滑动的百分比，通过滑动的百分比来控制动画播放的百分比。这样虽然也可以实现想要的效果，但是会很复杂，有许多中间状态需要我们自己计算和保存，维护起来非常麻烦。

在这些复杂的实现后，在朋友的建议下使用了`ViewPager.PageTransformer`来重新实现上述效果，瞬间简单了许多，很多中间状态不许需要自己去维护，PagerTransformer会帮你去维护，并且在他提供的方法中`transformPage(View page, float position)`已经帮你计算好了滑动百分比，根本不需要你再去计算，这个简直太贴心了。其实在滑动过程中对UI修改最重要的也是最麻烦的就是去计算滑动的百分比，现在有了这个百分比，你想实现什么动画就实现什么动画，简直太爽了。

在使用ViewPager.PageTransformer的时候需要注意的就是他的接口方法中的postion，只要理解了它的用法，PageTransformer才算是用熟了。首先来介绍下position的用法：

+ position 是一个float值，它代表了一个页面离开屏幕的百分比，以及另一个页面进入的百分比

+ position = 0  : page完全被展示，也就是说page处于屏幕的正中间
+ position = 1  : page完全处于屏幕的右边
+ position = -1 : page完全处于屏幕的左边

如果用户拖拽position=0(当前屏幕正中央的page)的page从右往左滑动，当只滑动一半的时候，原来position = 0的page其position会由0逐渐变为 -0.5，position=1的page（屏幕右侧的page）其position会由1逐渐变为 0.5。

> 注意：
> 从右往左滑 <——  position为负数
> 从左往右滑 ——>  position为正数

好了理解了上面所讲的内容就可以真正的去做出漂亮的动画了，下面给一个动画实例
{% highlight java %}
public static class RotatePageTransformer implements ViewPager.PageTransformer {
    private static float MIN_TRAN = 50f;
    private static int MIN_ROTATE = 15;

    @Override
    public void transformPage(View view, float position) {
        if (position <= 1) {
            float rotateFactor = MIN_ROTATE * position;
            float tranFactor = MIN_TRAN * position;
            if (position < 0) {
                view.setTranslationY(-tranFactor);
            } else {
                view.setTranslationY(tranFactor);
            }
            view.setRotation(rotateFactor);
        }
    }
}
{% endhighlight %}

该动画的效果如下：
![page1](http://blog.tedyin.me/images/page1.png)
![page2](http://blog.tedyin.me/images/page2.png)
![page3](http://blog.tedyin.me/images/page3.png)

在滑动的过程总中左边和右边的会以此导向一个方向并且在Y方向上有一定位移，等到中间后图片的夹角会消失，而且会向上移动一定的距离。

好了，赶紧去体验一下ViewPager.PageTransformer吧。

