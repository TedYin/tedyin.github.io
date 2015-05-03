---
title: Android开发中遇到的小问题汇总
layout: post
date: 2015-05-03 22:53:00
---
在实际的项目开发过程中，经常会遇到一些莫名其妙的问题，或者很容易忽略的但是会造成很多麻烦的小问题，这篇帖子用来记录下这些问题，并给出注意事项或者解决方法，后续会不断更新所遇到的问题和解决方法。


1. Fragment中使用onActivityResult方法无效不能有效的回调

###解决方法
在Fragment中不要使用getActivity().startActivityForResult()方法去启动Activity，而应该直接使用startActivity方法去启动Activity，否则会被挂载Fragment的Activity中的onActivityResult的方法截获，导致Fragment的onActivityResult方法无法被回调。

###参考
[参考Stackoverflow](http://stackoverflow.com/questions/6147884/onactivityresult-not-being-called-in-fragment)

2. ViewPager.setCurrentItem(0)后onPageSelect方法没有被触发

###解决方法
调用完setCurrentItem(0)后再手动调用加载函数。原因是ViewPager内部的mCurrentItem默认值为0，设置了setCurrentItem（0）之后dispatchSelect为false认为已经调用过了onPageSelect方法，因此在第一次调用完setCurrentItem(0)时无效。

###参考
[参考Stackoverflow](http://stackoverflow.com/questions/11794269/onpageselected-isnt-triggered-when-calling-setcurrentitem0)
