---
title: Handling Runtime Changes
layout: post
date: 2014-09-21 20:22:00
---

##Runtime Changes 是什么
在Android运行期间，当设备的一些属性改变时，为了更好的使得App适应设备的改变，Android会主动destory当前的Activity，并且重新创建并启动它，以适应设备属性的改变。这样做的目的是为了更好的方便我们处理这些Change。

##常见的改变
常见的当以下情况其中之一发生时都会引起Activity被Destory并且重新调用onCreate去创建启动
+ 当屏幕方向改变时
+ 当键盘隐藏或者显示时
+ 当系统语言改变时

以上是常见的几个会引起，Activity被Destory掉，并且立即被重新创建的操作，其他的操作可以参考[开发文档](http://developer.android.com/reference/android/content/res/Configuration.html)

##处理这些改变
处理这些改变有两类操作

+ 在Configuration改变时保存当前的上下文状态对象
+ 自己手动处理Configuration改变导致的页面重启

> 这里以屏幕方向改变为例

###保存当前状态对象

如果在屏幕改变方向时重启当前的Activity时需要很多的数据，并且消耗很多时间的话，我们可以在Activity被Destory之前保存这些需要重新加载的数据，然后等到Activity启动后直接加载这些数据就可以了。那么Activity都被销毁了，这些数据怎么存储呢？当然有办法！但不是使用Bundle，因为它没有办法存储大量的数据，它的设计也不是被用来存储大量数据的，因为它内部的数据都是序列化的，如果将数据存储到Bundle中需要先序列化，然后取数据的时候再反序列化，这样一来一回对于有很多数据时开销就会很大，得不偿失。
     我们可以使用Fragment来存储这些数据，当系统因为屏幕方向的改变，销毁了你的Activity时，用来保存Activity状态的对象不会被销毁，当Activity再次被重启时可以通过FragmentManager来获取这个Fragment，并从中得到Activity存入的状态对象。具体实现方法如下：

1. 创建一个用来存储Activity状态对象的Fragment，DataFragment
2. 在onCreate方法中调用 setRetainInstance(true)方法
3. 把这个Fragment添加到FragmentManager中，确保当Activity再次启动时还可以得到该Fragment的引用。

{% highlight Java%}

public class DataFragment extends Framgent{
     private MyDataObject mData;
     public void onCreate(Bundle savedInstanceState){
          // retain this fragment 
          setRetainInstance(true);
     }     
     
     public MyDataObject getData(){
          return mData;
     }

     public void setData(MyDataObject data){
          this.data = mData;
     }
}

{% endhighlight %}

> + 注意：在Fragment中保存数据时，禁止保存和Activity绑定的数据，比如View，Adapter或Drawable等。因为Activity会被销毁后再重新启动，当重启后View等对象中引用的Activity已经失效，会引起错误导致程序崩溃。


> + 在setRetainInstance(boolean)方法设置为true的作用就是防止当Fragment所在的Activity被销毁的时候，Fragment不被销毁，设置为true后，Fragment的生命周期会有所改变，`onDestory()`方法不会被调用，但是`onDeteach()`方法会被调用，当Activity再次启动后，`onCreate()`方法不会被调用，`onAttach()`方法会被调用，`onActivity()`方法也会被调用。（就是跳过了Fragment的创建和销毁过程）

当Activity重启之后，可通过FragmentManager获取，保存数据的Fragment，然后从Fragment中恢复保存的数据，代码如下:

{% highlight Java %}
public class MyActivity extends Activity{

     private MyDataObject mData;
     private DataFragment dataFragment;
     @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        // find the retained fragment on activity restarts
        FragmentManager fm = getFragmentManager();
        dataFragment = (DataFragment) fm.findFragmentByTag(“data”);

        // create the fragment and data the first time
        if (dataFragment == null) {
            // add the fragment
            dataFragment = new DataFragment();
            fm.beginTransaction().add(dataFragment, “data”).commit();
            // load the data from the web
            dataFragment.setData(loadMyData());
        }

        // the data is available in dataFragment.getData()
        ...
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        // store the data in the fragment
        dataFragment.setData(collectMyLoadedData());
    }
}
{% endhighlight %}

这样就可以在Activity重启之后获取保存的状态对象了。

> 这里是将Fragment当容器来使用，利用了它的特性，存储Activity的状态对象。而且在获取时，由FragmentManager来管理Fragment的状态，这样既方便又安全。


###自定义处理Configuration的变化
如果在Configuration变化时没有资源文件的更新(横竖屏不同布局)，或者因为性能的要求，不需要重启当前Activity的话，我们可以通过自定义处理Configuration的变化来代替系统的默认处理(重启Activity)。使用`<activity>`标签下的`<android:configChanges>`属性来指定需要自定义处理的属性变换。代码如下:

{% highlight xml %}

<activity android:name=".MyActivity"
    android:configChanges="orientation|keyboardHidden|screenSize"
    android:label="@string/app_name"]]>

{% endhighlight %}

当在`<activity>`中配置好之后，我们可以通过重写`onConfigurationChanged(Configuration)`方法来自定义处理Configuration的属性变化。


{% highlight Java %}

@Overried
public void onConfigurationChanged(Configuration newConfig){
     super.onConfigurationChanged(newConfig);

    // Checks the orientation of the screen
    if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE) {
        Toast.makeText(this, "landscape", Toast.LENGTH_SHORT).show();
    } else if (newConfig.orientation == Configuration.ORIENTATION_PORTRAIT){
        Toast.makeText(this, "portrait", Toast.LENGTH_SHORT).show();
    }
}

{% endhighlight %}

> 注意：在3.2版本 API level 13之后，`screen size`属性的改变也会引起Activity的销毁并重启。而且congfigChanges的属性都是Integer的，如果是自定义处理属性改变，那么在属性改变后所需要重新加载的数据都要重新设置一遍。比如，某个图片横屏和竖屏是不同的，那么就需要在onConfigurationChanged()方法中重新设置该图片的资源。

##参考
[Handling Runtime Changes](http://developer.android.com/guide/topics/resources/runtime-changes.html)