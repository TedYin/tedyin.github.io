---
layout: post
date: 2015-01-02 10:00:00
title: 使用反射自定义SearchView
---
最近在优化代码，发现Android support v7包中的SearchView，开放的方法很少，导致SearchView自定义比较麻烦。所以在网上搜索解决办法，发现借助Java强大的反射功能来动态修改SearchView是最合适的方法。

下面来举个例子来说明一下如何使用反射来实现自定义SearchView的效果：

比如SearchView中展开后的底部Hint背景有一个搜索的图标，但是我们要是想隐藏该图标是没有办法直接隐藏的，因此我们可以使用反射来进行动态修改，为了可以使用反射，因此我们要查看SearchView的源码然后找出该图标的所属控件，然后使用反射动态修改该控件的属性，从而达到隐藏该图标的目的。
下面来看下具体步骤：

###查看源码
经过查看SearchView的源码，我们发现这个图标是SearchView中的 SearchAutoComplete的Hint属性，源码如下：
{% highlight java %}
if (mQueryHint != null) {
    mQueryTextView.setHint(getDecoratedHint(mQueryHint));
} else if (mSearchable != null) {
    CharSequence hint = null;
    int hintId = mSearchable.getHintId();
    if (hintId != 0) {
        hint = getContext().getString(hintId);
    }
    if (hint != null) {
        mQueryTextView.setHint(getDecoratedHint(hint));
    }
} else {
    mQueryTextView.setHint(getDecoratedHint(""));
}
{% endhighlight %}

###使用反射修改SearchView
从上述代码可以看出，显示Hint的是`mQueryHint`,因此只要将这个Hint值设为“”空字符串就可以隐藏该icon。下面使用反射的方式获取mQueryTextView这个private级别的控件,并将Hint设为空字符串，代码如下：

{% highlight java %}
private void hideCloseSearchIcon(SearchView searchView) {
	SearchAutoComplete mQueryTextView = (SearchAutoComplete) SystemUiHelper.getFieldByReflect(
			"mQueryTextView", SearchView.class, searchView);
	mQueryTextView.setHint(R.string.hint_search);
}

public static Object getFieldByReflect(String fieldName, Class<?> claz, Object target) {
	try {
		Field searchField = claz.getDeclaredField(fieldName);
		searchField.setAccessible(true);
		return searchField.get(target);
	} catch (NoSuchFieldException e) {
		e.printStackTrace();
	} catch (IllegalAccessException e) {
		e.printStackTrace();
	}
	return null;
}
{% endhighlight %}

这样就可以将SearchView中输入框旁边的icon去掉了，下面看下对比图：

去掉之前的样子：
![search1](http://blog.tedyin.me/images/search1.png)
去掉之后的样子：
![search2](http://blog.tedyin.me/images/search2.png)

同理，可以使用反射做很多类似的修改和操作,只有你想不到的没有你做不到的。