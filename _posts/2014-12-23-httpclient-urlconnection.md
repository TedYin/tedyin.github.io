---
title: Android HttpURLConnection VS HttpClient
date: 2014-12-22 23:40:00
layout: post
---
(原文)[http://android-developers.blogspot.tw/2011/09/androids-http-clients.html]
在Android中提供了两种Http操作的封装，一种使用HttpURLConnection去进行网络操作，另外一种是使用HttpClient进行网络操作的处理，这两者在Android中共存，但是官方建议使用HttpURLConnection来进行网络处理。

##HttpClient与HttpURLConnection在Android中的比较

Android中直接封装了Apache的HttpClient来作为网络操的处理类，该类是非常强大的类，比较重量级，功能和方法非常丰富，因此带来了一个问题，那就是定制性非常差，修改起来非常麻烦。相比而言HttpURLConnection要轻量许多，方法较少而且都比较简单，因此定制性更强，修改起来容易，Android团队内部也更倾向使用定制性更好的HttpURLConnection来进行网络处理。

但是因为在FROYO版本以及之前版本，Android对HttpURLConnection支持不好，HttpURLConnection有许多bug，所以在2.3版本之前建议使用HttpClient来进行网络操作。在FROYO以及之后的版本Android修复了HttpURLConnection的许多问题，而且在4.0之后HttpURLConnection还加入了对缓存的支持，使用下面方法可以在4.0版本中开启Http缓存:

{% highlight java %}
private void enableHttpResponseCache(){
	try{
		long httpCacheSize = 10 * 1024 * 1024; // 10 MiB
	    File httpCacheDir = new File(getCacheDir(), "http");
	    Class.forName("android.net.http.HttpResponseCache")
	        .getMethod("install", File.class, long.class)
	        .invoke(null, httpCacheDir, httpCacheSize);
	}cache(Exception httpResponseCacheNotAvailable){
	}
}
{% endhighlight %}

在客户端配置好缓存处理后，同样需要在服务端进行配置支持HttpCache，否则Cache是不会生效的。
在2.3以及以后的版本中，HttpURLConnection默认是支持`Accept-Type:gzip`的，只需要在服务端配置一下即可，在消息传输和获取的过程中使用压缩数据进行传输，这样会更加快速的传递数据，而且省电省流量。但是如果服务端支持gzip格式数据传输，那么在使用`getContentLength()`方法获取的就是压缩后的数据大小，这一定要注意。

##到底使用哪一个？
上面已经说的很明确了，在2.3版本之前使用HttpClient进行网络请求，在FROYO版本之后使用HttpURLConnection进行网络请求，因为2.3以前的版本市场份额已经很小很小了，所以在以后的开发中可以略去选择直接使用HttpURLConnection进行网络操作，而且Android官方也会持续改进HttpURLConnection在Android中的性能。
