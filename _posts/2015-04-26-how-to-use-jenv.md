---
layout: post
title: How To Use Jenv To Master Your Java Environnement
date: 2015-04-26 20:22:00
---
> 对于一个Java开发者来说，系统上安装多个Java版本是很常见的事，但是在各个版本之间进行切换是一件非常痛苦的事情，今天向大家介绍一个工具`Jenv`，用它来终结我们的Java版本管理之痛。

# 介绍
Jenv的作用类似于rbenv，只是rbenv是用来管理系统上的不同Ruby版本的。Jenv运行在Linux或者Mac上，不能在Windows上使用（建议Win的用户转向Linux或者Mac）。
> 本文以Mac系统位例进行介绍。

# 安装
1. 将代码下载到`~/.jenv`目录下
{% highlight bash %}
$ git clone https://github.com/gcuisinier/jenv.git ~/.jenv
{% endhighlight %}

2. 将`~/.jenv/bin`加入到`$PATH`环境变量中

3. 添加初始化Jenv的脚本到环境变量中
{% highlight bash %}
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile
{% endhighlight %}

4. 重启命令行，使PATH的设置生效，现在可已使用jenv来管理JDK版本了。

# 使用
下面介绍几个最常用的Jenv命令

> 1. `jenv version` 显示当前正在使用的JDK版本号

> 2. `jenv versions` 显示当前系统上已将安装的所有JDK版本号

> 3. `jenv local [java version]` 指定当前目录下要使用的JDK版本号，只在该目录及其子目录内有效，在该目录以外还是使用原有的JDK版本。

> 4. `jenv global [java version]` 指定全局的JDK版本号，在整个系统中有效。

> 5. `jenv info java` 检查`JAVA_HOME`设置是否有效，如果有效会输出`JAVA_HOME`的路径

> 6. `jenv local --unset` 消除jenv local的设置，恢复使用默认的JDK版本


# 该项目Github地址

[Jenv](https://github.com/gcuisinier/jenv)





