---
title: Http代理设置
date: 2014-10-26 21:10:00
layout: post
---


Android应用程序访问互联网时，如果处于WIFI或者CMNET、UNINET或者3GNET，CTNET等接入方式时，无需设置代理即可顺利的访问网络，但是如果处于WAP环境下，那么就需要首先设置代理，之后才能访问互联网。跟设置超时一样，设置代理同样有HttpClient和HttpURLConnection两种方式：

##HttpClient方式
{%highlight java%}
HttpClient httpClient = new DefaultHttpClient();
String host = Proxy.getDefaultHost(); //默认代理服务器地址
int port = Proxy.getDefaultPort(); //默认代理服务器端口号
HttpHost httpHost = new HttpHost(host, port);
HttpParams params = httpClient.getParams();
params.setParameter(ConnRouteParams.DEFAULT_PROXY, httpHost); //设置默认代理
{%endhighlight%}

##HttpURLConnection方式


{%highlight java%}
String host = android.net.Proxy.getDefaultHost(); // 默认代理服务器地址
int port = android.net.Proxy.getDefaultPort(); // 默认代理服务器端口号
SocketAddress socketAddr = new InetSocketAddress(host, port);
// 构造代理对象
java.net.Proxy proxy = new java.net.Proxy(java.net.Proxy.Type.HTTP, socketAddr);
try {
  URL url = new URL("www.baidu.com");
  // 设置代理
  HttpURLConnection conn = (HttpURLConnection) url.openConnection(proxy);
} catch (MalformedURLException e) {
  e.printStackTrace();
} catch (IOException e) {
  e.printStackTrace();
}
{%endhighlight%}

##参考
[Android通过WAP方式联网](http://blog.csdn.net/ace1985/article/details/7844159) 
