---
title: Loading Large Bitmap
layout: post
date: 2014-07-06 11:00:00
---
在我们加载图片的时候，经常会出现这种情况，我们加载的图片的解析度已经远远超出我们设备的解析度，而且太
大的图片会占据比较大的内存带来大的内存开销，如果对这些内存回收管理不及时，很容易造成OutOfMemory运
行时异常，导致整个应用崩溃，因此为了避免这种情况的发生，我们在decode图片的时候应当根据自己设备的解
析度来解析，只需要解析出适合我们当前设备的图片即可，不需要解析出超高分辨率的图片，因为超高分辨率的图
片对我们的视觉体验带来不了任何帮助，反而会占用宝贵的内存，产生性能问题。下面我们将讨论如何将一张大图
片解析成一个符合当前设备内存要求的小图片。

##Read Bitmap Dimensions and Type
`BitmapFactory`类提供了许多解析图片的方法(`decodeByteArray()` `decodeFile()`
`decodeResource()` `decodeStream()`等)来解析各种形式的图片。这些方法在执行的时候都会为图片
分配一块内存去解析图片，这样做很容易导致OOM的发生。但是系统为每个图片解析的方法都提供了一个
`BitmapFactory.Option`对象做为参数，来设置我们的解析过程是否分配内存给被解析的图片。
设置`Options.inJustDecodeBounds = true`，则在解析的时候不会为Bitmap分配内存，因此解析完成
后返回的Bitmap对象将会是null。
因此如果我们想知道这个图片的尺寸和类型，我们可以先设置`Options.inJustDecodeBounds = true`，
然后调用decode方法并且传入Options对象，然后在decode结束后，我们可以通过`Options.outWidth`、
`Optinons.outHeight`和`Options.outMimeType`,获得图片的尺寸以及图片的类型。
代码如下：

```
BitmapFactory.Options opt = new BitmapFactory.Options();
opt.inJustDecodeBounds = true; //解析时不分配内存，仅仅是获取图片信息
BitmapFactory.decodeResource(getResource(),R.id.image,opt);
int imageWidth = opt.outWidth;
int imageHeight = opt.outHeight;
String imageType = opt.outMimeType;
```
这样我们就得到了图片的尺寸和类型。
为例避免OOM发生我们在解析前，我们需要在解析前检查图片的尺寸和大小，是否符合我们的需求。

##Load a Scaled Down Version into Memory
既然我们已经知道了图片的尺寸，下来我么就需要考虑是加载这个图片呢还是加载一个低解析度的版本。我们可以
通过如下几个因素来考虑

+ 估算加载整个图片所需要的内存大小
+ 是否有足够多的内存来装载图片
+ 装载图片的目标Image UI的尺寸（如果是小尺寸，那么只需要低解析度的图片即可）
+ 当前设备的屏幕尺寸和像素密度

例如，我们不需要将一个1024 * 768 px的图片加载到一个126 * 96 的ImageView中去。

为了告诉解析器，我们只需要加载一个合适的解析度的图片，可以通过设置
`BitmapFactory.Options.inSampleSize = 数字`即可。
例如解析一个**`2048 * 1536` 的图片(12M)，使用的`inSampleSize = 4`，
解析出来的图片的为 `512*384`(0.75M)大小变为原来的`inSampleSize^2`分之1**。
*即： 0.75 M= 12M/(4 * 4)*

>注意:这里假定使用的图片是ARGB_8888编码的32位图片

下面是一个计算`inSampleSize`取值的方法：

```
public static int calculateInSampleSize(
	BitmapFactory.Options opt, int reqWidth,
	 int reqHeight){
	final int height = opt.outHeight;
	final int width = opt.outWidth;
	int inSampleSize = 1;
	if(height > reqHeight || width > reqWidth){
		final int halfHeight = height/2;
		final int halfWidth = width/2;
		while(halfHeight/inSampleSize > reqHeight &&
			halfWidth/inSampleSize > reqWidth){
			inSampleSize *= 2;
		}
	}
	return inSampleSize;
}
```
在使用上述方法之前，必须先获取图片的尺寸信息。

有了上面介绍的方法，以后在加载图片我们可以按照下面步骤进行：

1. 获取图片的尺寸和类型
2. 根据所需要的图片大小，获取最佳的`inSampleSize`值
3. 使用2中获得的`inSampleSize`来解析图片

```
public static Bitmap decodeSampledBmpFromResource(
	Resource res,int resId,int reqHeight,int reqWidth){
	// 1.获取图片的尺寸信息
	BitmapFactory.Options opt = new BitmapFactory.Options();
	opt.inJustDecodeBounds = true;
	BitmapFactory.decodeFromResource(res,resId,opt);
	// 2.计算最佳inSampleSize值
	opt.inSampleSize=
	calculateInSampleSize(opt,reqWidth,reqHeight);
	// 3.将原图解析成需要的Bitmap
	opt.inJustDecodeBounds = false;//否则解析出的bmp为null
	return BitmapFactory.decodeFromResource(res,resId,opt);
}
```
