---
layout: post
title: BitmapFactory.Options 参数详解
date: 2014-07-20 22:24:00
---


常用参数： `inJustDecodeBounds` `inSampleSize` `inPurgeable` `inInputShareable` `inTempStorage` `outWith` `outHeight` `outMimeType`

1. inMutable true 返回一个可变（可被修改）的Bitmap对象，false 返回一个不可变的Bitmap对象（即不可修改的）

2. inJustDecodeBounds 为true时，只是获取图片的信息，并不对图片进行解析，所以可以通过Options获取图片的信息，false时返回解析后的Bitmap对象

3. inSampleSize 默认为1，小于1的值也当1处理。eg： 200 * 200 的图片，if inSampleSize == 1 then 200 * 200
if inSampleSize == 2 then 100 * 100； 整个图片的大小变为原来的1/4 (即1/2^2)

4. inInputShareable 和 inPurgeable 是在一起使用，如果inPurgeable为false，那么inInputShareable会被忽略，如果为true，那么当inInputShareable为true时，the bitmap can share a reference to the input data or make a deep copy

5. inPreferQualityOverSpeed 为true时，解析高质量的图片，不在乎解析快慢。

6. inPreferredConfig if non-null then 按照这个Config的配置来解析图片，else 按照系统配置来解析，默认按照ARGB_8888来解析。

7. inPremultiplied默认为true，if false then 关闭Alpha通道。不常用，只有在进行图片深度处理RenderScript或者OpenGL处理的时候才会用到。普通的canvas之类的不需要修改保持默认就好，修改会报错。

8. inPurgeable if true then 当Bitmap被回收后，如果又需要这个Bitmap时，会自动re-decode。因此inPurgeable 可以帮助系统节省内存，但是在快速滑动（ListView等）中不要使用，这样会增加GC负担，性能下降。如果是在fluid 和fast fly UI中建议使用inBitmap来减少内存消耗。这个属性在使用decodeResource和decodeFile的时候被忽略。

9. inScaled if true then 按照inTargetDensity来对图片进行拉伸缩放，而不是按照系统条件缩放，默认是true，点9图会忽略这个选项，永远都会被拉伸。

10. inTempStorage 是byte[]数组，建议大小为16K。类似于view.setTag(Object).

11. outWidth,outHeight,outMimeType在获取图片信息时，会被赋值。分别代表图片的宽、高、类型

12. mCancel decode过程是否可以被中断。

13. inDither true 让图片抖动。

14. inDensity inScaled inTargetDensity inScreenDensity 一起使用，用来设置图片是否需要拉伸，和要拉伸的像素密度。（系统默认的density为160）
