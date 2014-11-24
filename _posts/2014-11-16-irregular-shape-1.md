---
layout: post
date: 2014-11-15 23:52:00
title: Irregular Shape
---

接下来几周的内容是一系列文章，来讲解Android中不规则图形的创建和使用。今天先来介绍一下圆角图片的实现。

有一个好消息要告诉大家，那就是在API 20中Android已经默认提供了圆角矩形的图片，RoundRectShape Drawable，但是不好的消息是大多数的人的手机版本都是20或者以上的，因此还是自己动手丰衣足食吧。

实现这种效果的方式有很多先来讨论第一种，最笨效果最差的实现。

## 使用图层方式实现

使用这种方法实现的话，我们需要使用到两张图片，一张是原图，另外一张就是与原图大小尺寸完全相同的模板图片比如下面两幅：

![dog](http://blog.tedyin.me/images/dog.jpg)
![mask](http://blog.tedyin.me/images/mask.png)

> 这里使用绿色的原因只是为了大家看的清楚而已。

有了这两张图之后，我们可以加载这两张图片到内存中，然后使用`PoterDuffXfermode`类来将这两个图片合成为一张图片，就可以生成一个圆角图片的效果了。具体的代码如下：

{% highlight java %}
public Bitmap combineImage(Bitmap srcBitmap, Bitmap maskBitmap){
	Bitmap resultBmp;
	int width = srcBitmap.getWidth()>maskBitmap.getWidth()?srcBitmap.getWidth():maskBitmap.getWidth();
	int height = srcBitmap.getHeight()>maskBitmap.getHeight()?srcBitmap.getHeight():maskBitmap.getHeight();

	resultBmp = Bitmap.createBitmap(width,height,Bitmap.Config.ARGB_8888);
	Paint paint = new Paint();
	paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode_SRC_ATOP));

	Canvas canvas = new Canvas(resultBmp);
	canvas.drawBitmap(srcBitmap, 0, 0, null);
    canvas.drawBitmap(maskBitmap, 0, 0, paint);

}
{% endhighlight%}

合成后的效果如下

![dog_mask](http://blog.tedyin.me/images/dog_mask.jpg)

OK，圆角矩形图片效果实现。但是这个方法实质上是最差劲的，它有如下几个缺点:

> 1. 如果图片的形状有很多种，我们需要为每种图片都生成一张mask图片，这样效率会很低，而且工作量会加大很多。
> 2. 适配性会变得很差，如果是圆角矩形图片，在大屏手机上拉伸回导致圆角效果失真。
> 3. 还有个最大的问题，就是性能问题，如果图片尺寸很大，我们加载到内存中很可能出现内存溢出的情况，这样就得不偿失了。

因此这个方法的可用性较低，不推荐使用。但是此处为什么要介绍这个方法呢，最主要的是这个处理思路，我们可以使用上述的而方法来处理一些水印效果或者其他图片合成的效果，这样才是上述方法最好的使用方式。


接下来看看如何使用高效的方法创建圆角矩形。


##使用BitmapShader来创建圆角矩形
先来介绍一下使用BitmapShader的思路，其实这个很类似于我们平常使用Canvas画图形的方式，只是我们平时在画圆角矩形时填充Canvas的是纯色，这里为了得到圆角图片，我们可以使用上面的思路，将填充物由纯色改为我们要画的图片即可。具体的实现方式如下：
{% highlight java %}

private Bitmap processBitmap(Bitmap bitmap) {
	Bitmap bmp;
	bmp = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), Bitmap.Config.ARGB_8888);
	BitmapShader shader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
	// 随机设置一个圆角的半径大小
	float raidus = getResources().getDisplayMetrics().density * 6 + 0.5f;

	Canvas canvas = new Canvas(bmp);
	Paint paint = new Paint();
	paint.setAntiAlias(true);
	paint.setShader(shader); // 将要填充的shader交给Paint

	RectF rect = new RectF(0, 0, bitmap.getWidth(), bitmap.getHeight());
	canvas.drawRoundRect(rect, raidus, raidus, paint);// 在RectF上绘制圆角图片
	return bmp;
}
{% endhighlight %}

使用上述代码即可实现圆角矩形的绘制，绘制的结果如下：
![dog2](http://blog.tedyin.me/images/dog2.png)

同样的道理，我们可以使用上述方式，绘制出三角形，椭圆，多边形等各种各样的图片，思路和绘制圆角矩形是一样的。都是使用RectF来确定大体形状，然后使用带有BitmapShader的Paint来填充即可实现。



