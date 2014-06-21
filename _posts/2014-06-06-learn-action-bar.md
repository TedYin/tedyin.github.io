---
title: Learn ActionBar
layout: post
date: 2014-06-04
---

#ActionBar Overview
ActionBar有如下几个特点:

+ ActionBar 是android系统提供专门用来应用导行功能的一个控件
+ 提供一种非常便捷和高效的导航方式
+ 支持不同页面间的导航功能切换

是android在3.0时添加的一个重要组件。它可以通过support library的形式被集成到2.1及其以上的版本中使用。

#ActionBar Use
下面主要介绍一下使用support v7 library中的ActionBar。
###Add ActionBar

1. 先[导入v7包](http://developer.android.com/tools/support-library/setup.html).
2. 你的Activity必须继承自ActionBarActivity
3. 在你的应用中使用Theme.AppComapt主题或者使用继承自Theme.AppCompat主题的自定义。

*注：Theme.AppCompat 相当于3.0中添加的Theme.Holo主题*
这样在你的Activity中就完成了ActionBar的添加。

###Removing the action bar
关闭ActionBar的方法如下:

+ getSupportActionBar()得到ActionBar的对象，调用hide方法即可关闭ActionBar。如果想再次显示，则可以是用show方法即可
+ 使用Them.AppCompat.NoActonBar方法，不使用Actionbar

>注意

>+ support包中的getSupportXXX()方法就等同于在加入该控件的API level以上的SDK中调用 getXXX()方法。
>+ 在使用ActionBar的show()和hide()方法的时候，会导致界面布局调整，整个界面会被重绘，增加性能成本，如果需要经常对ActionBar进行隐藏和显示操作，我们可以使用ActionBar Overlay来完成这个需求。ActionBar Overlay的实现方式是使用Custom的主题继承自Theme.AppCompat并且设置windowActionBarOverlay这个属性为true即可。它会使得ActionBar浮在Layout的上面，此时如果对ActionBar进行show或者hide操作，就不会进行界面重绘了。如果你的布局不想被ActionBar挡住，则可以在该布局的根一级的Layout中加入paddingTop 或者marginTop属性，给他们赋值为"?android:attr/actionBarSize"即可。

###Add Action Items

上面已经有了ActionBar，但是如何让这个ActionBar进行工作呢?因此我们需要给ActionBar添加内容。ActionBar相当于一个导航容器，他是一个什么都能接收到东西。你可以自定义许多内容给他，他都可以将这些东西组织并展现出来。ActionBar跟3.0之前的版本中的菜单一样，提供一堆功能导航，只是在3.0之后将它的作用明确了，尤其是在4.0之后配合Android Design一起使得它的功能性更明确了就是处理Activity中的动作交互。添加Action Item我们只需在res/menu/xxx_menu.xml中定义好要添加的Item内容，然后重载OnCreateOptionsMenu()方法，并且在其中使用MenuInflater将这个xml布局inflate成Menu对象，并且设置给ActionBar即可。

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu items for use in the action bar
        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.main_activity_actions, menu);
        return super.onCreateOptionsMenu(menu);
    }

xxx_menu.xml布局文件如下：

    <menu xmlns:android="http://schemas.android.com/apk/res/android" >
        <item android:id="@+id/action_search"
              android:icon="@drawable/ic_action_search"
              android:title="@string/action_search"/>
        <item android:id="@+id/action_compose"
              android:icon="@drawable/ic_action_compose"
              android:title="@string/action_compose" 
              yourStyle:showAsAction="ifRoome"/>
    </menu>

*yourStyle:showAsAction*是需要用户自定义的属性，在3.0以上版本中属于Android的默认属性。`showAsAction`属性的选项有`ifRoom/always/never `,作用如下:

+ ifRoom 表示如果有做够的空间，Action将会显示在ActionBar，否则会隐藏到overFlow中
+ always 表示永远显示在ActionBar上
+ never表示永不显示在ActionBar上，即永远显示在overflow中

当用户点击上述按钮的时候会调用`onOptionsItemSelected(MenuItem item)`方法，并且将被点击的Item传入到Menu中去，因此我们可以重载该方法并且在该方法中处理按钮点击事件，和处理普通的点击事件一样。

>如果你是在Fragment中使用`OnCreateOptionsMenu`方法inflate一个Menu到ActionBar上，当你在Fragment中点击ActionBar中的Action时，如果你在`OnOptionsItemSelected`中使用了`super.onOptionsItemSelected`方法，系统会先调用Activity中的`OnOptionsItemSelected()`方法去处理，处理完成后才会调用Fragment中的这个方法处理，如果不加则会直接调用Fragment中的`OnOptionsItemSelected()`方法处理。

###Using Split Action bar
Split ActionBar其实就是对ActionBar的分割，当用户使用窄屏幕的时候，如果在ActionBar上面不够放，系统会将Actions都放在屏幕的底部，来为导航和标题留出空间。使用Split Action Bar的效果图如下第1~2图。

![normal ActionBar](http://tedyin.me/images/201406090916.png) 

使用Split action bar 的方法如下:

+ 给`<application>`标签添加属性 `uiOptions="splitActionBarWhenNarrow"`,使得所有的Activity都应用Split Action Bar或者给某些指定的`<activity>`标签添加该属性，指定某几个Activity应用Split Action Bar。这个属性只能用于API level 14及其以上，如果想对其以下的版本进行兼容，需要在`<activity>`标签下使用`<meta-data>`标签来指定

```
    <activity ... >
        <meta-data
            android:name="android.support.UI_OPTIONS"
            android:value="splitActionBarWhenNarrow"/>
    </activity>
```

如果Activity使用了该属性，并且对于ActionBar上的icon和title分别使用`setDisplayShowHomeEnable(false)`和`setDisplayShowTitleEnable(false)`进行隐藏，那那么在有tab的情况下，main ActionBar也会被自动隐藏，效果如上图中第2~3图。

###Navigating Up with the App Icon
Navigating Up返回到上一级Activity，有两种方法：
1. 在`<activity>`标签中设置`android:parentActivityName="me.tedyin.todo.MainActivity"`（API level 16及其以上）即可，对于API level低于16的可以使用`<meta-data>`标签来指定

    <activity  ...     		android:parentActivityName="com.example.app.MainActivity"
    	>
        <meta-data
            android:name="android:support.PARENT_ACTIVITY"
            android:value="com.example.app.MainActivity"
            />
    <activity >
    
 >*注意：上面`android:parentActivityName`的值必须和下面meta-data的value中定义的值相同。*
 

2. 在代码中使用`getSupportActionBar().setDisplayHomeAsUpEnable(true)`来开启navigate up 按钮。

当Up Action 打开后，此时Icon前面会出现一个系统默认的返回按钮，表示可以返回到父Activity中。当点击该Up Action的时候，会调用`OnOptionsItemSelected(MenuItem)`方法，传入的是Id为`android.r.id.home`的`MenuItem`对象，我们可以重载上述方法来处理此点击事件。        

>如果我们的应用在关闭的情况下被第三方打开某个Activity，那么此时我们的activity则是在一个新的task stack中，此时该Activity的Logic Parent是不在该stack中的，为了保证操作的连贯性，你应该处理这种情况。我们可以使用NavUtils 来帮助我们处理，处理的过程如下:
1. 调用shouldUpReCreateTask(Context , Intent)来检查你的Activity是否存在一个App ‘s stack，包含你的Activity，如果返回true（不存在，需要重新创建），如果返回false（存在，不需要重新创建）。
2. 如果需要重新创建，则需要使用TaskStackBuilder来方便我们创新的task stack 。

最佳实践如下:

    //重载OnOptionsItemSelected(MenuItem)方法
    case android.R.id.home:
        Intent upIntent = NavUtils.getParentActivityIntent(this);
        if(NavUtils.shouldUpRecreateTask){
            //需要重新创建一个Task，来保存当前Activity的Parent Activity 到新的task stack中
            TaskStackBuilder.create(this).addNextIntentWithParentStack(upIntent).startActivitise();
        }else{
            //不需要的新建一个task来维护当前Activity和它的Parent Activity之间的stack关系，所以直接启动切换到父Activity即可
            NavUtils.navigateUpTo(this,upIntent);
        }
    break;

>如果是从Fragment中处理Navigation Up,我们需要覆写onSupportNavigateUp()方法去处理fragment transaction。通常我们会用popBackStack()方法处理fragment切换到Parent Fragment中去。

###Adding an Action View
Action View就是显示在ActionBar上面的Widget，他可以不需要改变Activity Fragment，并且也不会替换原有的ActionBar，不会导致界面重绘等问题，可以快速提供一个丰富的Action交互空间。我们常见的Search操作就是使用的ActionView来实现的。
####声明一个ActionView有如下方法:

+ 使用`actionLayout`属性来指定一个resource资源来生成一个ActionView
+ 使用`actionViewClass`属性来指定一个Widget来生成一个ActionView

添加一个ActionView的方法如下:(此处添加一个Widget来作为ActionView)

    <menu ... >
        <item  android:id = "@+id/action_search"
            android:title="search"
            andorid:icon="@drawable/icon"
            yourStyle:showAsAction="ifRoom|collapseActionView"
            yourStyle:actionViewClass="android.support.v7.widget.SearchView" 
            >
    </menu>
上述中collapseActionView的作用即为当点击Action Button时触发ActionView。

上面声明了ActionView，我们可以在OnCreateOptionsMenu(Menu)方法中inflate出这个ActionView并将其加入到ActionBar中。

    @Overried
    public void OnCreateOptionsMenu(Menu menu){
        getMenuInflater().inflate(R.layout.menu_actions,  menu);
        MenuItem searchItem = menu.findItem(R.id.action_search);
        SearchView searchView = (SearchView)MenuItemCompat.getActionView(searchItem);
        ...
        return super.OnCreateOptionsMenu(menu);
    }
使用MenuItemCompat的静态方法getActionView(MenuItem)即可得到ActionView。
*在API leve 11及其以上，`MenuItem`对象也提供`getActionView()`方法，即：`menu.findItem(R.id.action_search).getActionView()`*

###Handling collapsible action views
上面只讲述了如何打开Action view但是没有告诉你怎么collapse 一个 action view，下面就讨论一下如何collapse一个action view。当你点击返回键或者Up navigate button时，ActionView会自动收起。我们可以使用`OnActionExpandListener`来监听ActionView的打开与关闭。

    @Override
    public void OnCreateOptionsMenu(Menu menu){
        MenuItemCompat.setOnActionExpandListener(menuItem, new OnActionExpandListener(){
            @Overried
            public boolean onMenuItemActionCollapse(MenuItem item){
                //do something
            }
            @Overried
            public boolean onMenuItemActionExpand(MenuItem item){
                //do something
            }
        });
    }

###Add an Action Provider
每一个ActionProvider都已经定义好了自己的行为，因此你不需要再在OnOptionItemSelected()方法中对ActionProvider处理。虽然不用处理，但是如果你需要监听ActionProvider的行为的话，依然可以在OnOptionsItemSelected()方法中监听处理，但是一定要注意，然会值应该为false，否则ActionProvider无法收到点击事件。如果ActionProvider提供一个子菜单的话，那么子菜单中的点击事件不会调用OnOptionsItemSelected()方法。使用了ActionProvider我们需要处理的其实就是
ActionProvider使用方法如下:
    
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android"
          xmlns:yourapp="http://schemas.android.com/apk/res-auto" >
        <item android:id="@+id/action_share"
              android:title="@string/share"
              yourStyle:showAsAction="ifRoom"
              yourStyle:actionProviderClass="android.support.v7.widget.ShareActionProvider"
              />
        ...
    </menu>    

上述中`actionProviderClass`属性，在API Level 11以下需要自定义实现。添加完了布局文件，我们需要做的就是给在初始化ActionProvider的时候传入正确的Intent来保证他会按照我们设置的方式来工作。因为Context会变化，因此必须在每次更新的时候都要调用一次`setShareItent()`来保证我们Inent的正确。

    private ShareActionProvider shareActionProvider;
    //...
    @Overried
    public boolean OnCreateOptionsMenu(Menu menu){
        getMenuInflater.inflate(R.id.menu,menu);
        //set up share action provider
        MenuItem shareItem = menu.findItem(R.id.action_share);
        shareActionProvider = (ShareActionProvider)MenuItemCompat.getActionProvider(shareItem);
        //set share intent
        Intent intent = new Intent(Intent.ACTION_SEND);
        intent.setType("image/*");
        shareActionProvider.setShareIntent(intent);
        return supper.OnCreateOptionsMenu(menu);
    }

`ShareActionProvider`默认会根据用户的使用频率动态的调整每个Item的先后顺序，这个排序是保存在一个私有的文件`DEFAULT_SHARE_HISTORY_FIILE_NAME`中，我们可以使用`ShareActionProvider`对象的`setShareHistoryFileName("example.xml")`方法来指定一个排序文件，按照我们喜欢的方式进行自定义排序。

###Creating a custom action provider
使用自定义的Action Provider可以很方便的让你去管理和重用那些Action动作，而不用你再去每个Activity或者Fragment中去处理Action的行为。
创建自定义Action Provider的步骤如下:

1. 继承ActionProvider这个类
2. 实现ActionProvider()这个构造方法，他会传递一个Application Context 给你，你需要保存这个Context为后面其他回调方法提供。
3. 实现`onCreateActionView(MenuItem menuItem)`方法，这个方法是用来inflate action view的。
4. 实现`onPerformDefaultAction()`这个方法是在触发Action的时候系统调用的。这个方法默认是不实现任何操作，并且返回false

*注:如果你的ActionProvider是放在Overflow中的，而且它有subMenu，那么在点击时`onPerformDefaultAction()`这个方法是不会被调用的*

###Adding  Navigation Tabs
Tabs 提供了方便的导航，他的添加方法如下:

1. 实现`ActionBar.TabListener`接口。这个接口提供了许多tab事件的回调方法，你可以在这个接口中处理和tab相关的操作。
2. 对于每个你想添加的tab你在初始化这个tab的时候，可以通过`setTabListener()`方法添加`TabListener`，也可以同过`setTabTitle(title)`来设置tab的属性等。
3. 当初始化好一个tab后可以使用`addTab(yourTab)`方法将它加入到ActionBar去。
4. 必须调用`setNavigationMode(NAVIGATION_MODE_TABS)`方法让tab显示出来。

>注意:
>1. ActionBar.TabListener的回调方法不会指定哪个fragment和具体的tab绑定，它只会提供当前选中了哪个tab，你需要自己根据需要绑定，下面介绍一种绑定实现.
>2. 初始化一个Tab的方法: Tab tab = actionBar.newTab().setText("tab").setTabListener(tabListener);   然后 actionBar.addTab(tab);即可。


    //TabListener的实现
    public static class TabListener<T extends Fragment> implements ActionBar.TabListener{
        private Fragment mFragment;
        private final String mTag;//用来唯一标识Fragment在FragmentTransaction中的key
        private final Activity mActivity;//用来初始化Fragment
        private final Class<T> mClass;//Frangment的类，用来初始化fragment
        public TabListener(Activity activity, String keyTag, Class<T> clz){
            this.mTag = keyTag;
            this.mActivity = activity;
            this.mClass = clz;
        }
        public void onTabSelected(Tab tab, FragmentTransaction ft){
            // when tab selected
            //如果没有初始化，则进行初始化
            if(mFragment == null){
                mFragment = Fragment.instantiate(mActivity, mClass.getName());
                ft.add(android.R.id.content,mFragment,mTag);
            }else{
                //存在则直接attach显示
                ft.attach(mFragment);
            }
        }
        public void onTabUnSelected(Tab tab, FragmenTransaction ft){
            //whne tab unselected
            if(mFragment != null){
                //detach ，因为别的Fragment要attach显示
                ft.detach(mFragment);
            }
        }
        public void onTabReselected(Tab tab. FragmentTransaction ft){
            //user select the already selected tab , Usually do nothing. 因为他就是当前显示的，所以不需要操作什么。
        }
    }

>**注意：在回调中我们禁止调用commit()方法来确认fragment transaction，因为系统会为我们调用它，如果我们自己调用的话会出现异常。你同样也不能将这个fragment transaction 加入到back task stack中！**

>*上面的`android.R.id.content`是root view的Id，在不使用`setContentView`时使用它来作为Fragment的容器*


下面的代码使用了上面的TabListener，示例如下:

    @Overried
    public void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        //use the android.R.id.content as the container for each fragment , it is the root view id. 因此不用setContentView()
        //setup action bar for tabs
        ActionBar actionBar = getSupportActionBar();
        actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS);//显示tabs
        actionBar.setDisplayShowTitleEnable(false);
        Tab tab1 = actionBar.newTab().setText("tab1").setTabListener(new TabListener<Fragment>(this,"tab1",MFirstFragment.class));
        actionBar.addTab(tab);
        Tab tab2 = actionBar.newTab().setText("tab2").setTabListener(new TabListener<Fragment>(thsi,"tab2",MSecondFragment.class));
        actionBar.addTab(tab2);
    }




    

*未完待续~~*