---
layout: post
title: Gson的使用
date: 2014-09-14 22:15:00
disqus: tedyin
---

## Gson概述
Gson是Google内部为了使用的一个将Java对象序列化为JSON的Java类库，从2008年5月Google开源此项目后被广泛使用。类似的项目还有[FastJson](https://github.com/alibaba/fastjson/wiki)、[JackSon](http://jackson.codehaus.org)等。

## Gson使用

###Gson对基本数据类型的解析

{% highlight Java %}

Gson gson = new Gson();
toJson：
gson.toJson(1) --> 1
gson.toJson("abc") --> abc
gson.toJson(new Long(10)) -->10
gson.toJson(new int[]{1,2,3}) -->[1,2,3]
gson.toJson(new String[]{"aa","bb","cc"}) --> ["aa","bb","cc"]


fromJson：
int one = gson.fromJson("1",int.class);
int[] ints = gson.fromJson("[1,2,3]",int[].class);
Integer integer = gson.fromJson("1",Integer.class);
Boolean boolean = gson.fromJson("false",Boolean.calss);

{% endhighlight %}

###Gson解析对象

{% highlight Java %}

String playerJson = gson.toJson(new Player());
Player player = gson.fromJson(playerJson,Player.class);

{% endhighlight %}

###Gson对集合类的解析

{% highlight Java %}

toJson:
Collection<Integer> collection = 新建一个集合类，并存入1,2,3,4
gson.toJson(collection) --> [1,2,3,4]


fromJson:
Type type = new TypeToken<Collection<Integer>>(){}.getType();//匿名内部类

Collection<Integer> collection = gson.fromJson(json,type);

{% endhighlight %}

## 使用Gson的几点注意事项
> eg:使用Gson解析A.class

1. 如果A含有内部类且不是static的，则在解析是会忽略该内部类。
2. Gson会自动加载A.class中所引用的类，并对其进行解析。(类似于克隆)
3. 如果A中含有transient修饰的变量时，Gson会忽略该变量。
4. 如果A中含有组合类型，Gson在双向解析时都会忽略
5. 如果A中的变量赋值为null，Gson在toJson的时候会忽略该变量，在输出的json中也不会含有该字段(变量)。
6. 如果A中有字段(变量)id，但json中没有，则id会初始化为对应的默认值(int 0,obj null)
如果A中没有字段(变量)id，但json中有，则生成A对象时会忽略id这个变量。
7. Gson是不含有状态量的对象，因此只需要初始化一次就可以反复使用。
8. Gson提供了另外一种构造器GsonBuilder类，来为用户提供需要自定义的Gson解析对象。

##Gson还提供了很多自定义的功能

+ 使用@Since(versionCode)可以指定相应的版本对某个变量进行解析或者忽略。
+ 还可以使用GsonBuilder的excludeFieldsWithModifiers()方法来忽略某些特殊修饰词进行修饰的成员变量的解析。
+ 支持用户自定义的解析策略
更多自定义的功能，可以查看[Gson的帮助文档](https://sites.google.com/site/gson/gson-user-guide)。
