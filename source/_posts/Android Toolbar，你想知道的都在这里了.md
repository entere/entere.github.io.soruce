---
title: Android Toolbar，你想知道的都在这里了
date: 2017-06-20 22:17:23
tags: [Android, Toolbar]
---

[转自yifeng.studio](http://yifeng.studio/2016/10/12/android-toolbar/)


Android 3.0之后，Google引入了ActionBar，想统一安卓应用的导航栏样式。但由于ActionBar难以定制，很大程度上限制了开发人员，比如标题文字大小、间距等不易实现个性化，很多开发者放弃了ActionBar的使用，而是使用普通的ViewGroup来封装自己的App Bar，或者使用 [JakeWharton大神](https://github.com/JakeWharton) 的 [ActionBarSherlock](https://github.com/JakeWharton/ActionBarSherlock) 库。

后来，自2014年Google I/O 上Material Design 横空出世后，市场上的应用又逐步趋向了样式的风格统一，support library中很快就出来了 Toolbar 控件，一个定制化的 ViewGroup，来完善 ActionBar 的使用，App Bar又迎来了春天。

<!-- more -->


# 基本使用

第一步，在Theme中隐藏现有的ActionBar，有以下两种方式：

```xml
<style name="AppTheme.Base" parent="Theme.AppCompat">
    <item name="windowActionBar">false</item>
    <item name="android:windowNoTitle">true</item>
    ......
</style>

```

或者

```xml
<style name="AppTheme.Base" parent="heme.AppCompat.Light.NoActionBar">
    ......
</style>

```

第二步，在布局中添加v7包中的Toolbar控件（注意在builde.gradle文件中添加support.v7包的依赖：compile ‘com.android.support:appcompat-v7:23.3.0’），如：

```xml

<android.support.v7.widget.Toolbar
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/tb_toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:title="@string/app_name"
    app:titleTextColor="@android:color/white"
    android:background="@color/colorPrimary">

</android.support.v7.widget.Toolbar>

```

> 注意：如果此处不设置 title 属性，默认使用 manifest 文件中 <activity> 标签的 android:label 属性值作为标题内容。

第三步，在Activity代码中使用Toolbar对象替换ActionBar：


```java

Toolbar mToolbarTb = (Toolbar) findViewById(R.id.tb_toolbar);
setSupportActionBar(mToolbarTb);

```

效果如下：

![](/images/20170612_toolbar-sample-01.png)

诸如logo、title、subTitle、navigationIcon等，都可以通过app:xxx属性和Java代码来控制。titleTextAppearance、subtitleTextAppearance也可以用来控制标题颜色和大小，如：


```xml
<style name="Theme.ToolBar.Base.Title" parent="@style/TextAppearance.Widget.AppCompat.Toolbar.Title">
    <item name="android:textSize">18sp</item>
    <item name="android:textColor">@android:color/white</item>
</style>

```

# Options Menu

与 ActionBar 的用法一致，在menu资源目录下新建一个search.xml文件，添加menu内容，如：

```xml

<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/action_search"
        android:title="search"
        android:icon="@android:drawable/ic_menu_search"
        app:showAsAction="collapseActionView"/>

</menu>

```

在Java代码中添加为Toolbar添加对应的Menu Item，并设置点击事件，如：

```java

@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.search, menu);
    return super.onCreateOptionsMenu(menu);
}

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.action_search:
            //TODO search
            break;
    }
    return super.onOptionsItemSelected(item);
}

```


效果如/images/下：

![](/images/20170612_toolbar-sample-02.png)

如图，溢出按钮的颜色是黑色，那么怎么修改成与主题色搭配的白色呢？可以通过修改Toolbar的theme和popupTheme属性来改变，在Toolbar中添加如下属性：


```xml

<android.support.v7.widget.Toolbar
    ......
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

</android.support.v7.widget.Toolbar>

```

还有另一种方式，就是使用theme中的actionMenuTextColor属性设置Menu Item的字体颜色，如：

```xml

<style name="OverFlowMenuTheme" parent="Theme.AppCompat.NoActionBar">
    <item name="android:actionMenuTextColor">@android:color/white</item>
    <item name="overlapAnchor">false</item>
</style>

```

Toolbar中的相关theme设置如下：



```xml

<android.support.v7.widget.Toolbar
    ......
    app:popupTheme="@style/OverFlowMenuTheme"
    app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

</android.support.v7.widget.Toolbar>

```

一般也是采用这种做法，将Toolbar相关属性集中写到一个style中，比如：

```xml

<style name="OverFlowMenuTheme" parent="Theme.AppCompat.NoActionBar">
    <!-- 设置Menu菜单的背景色 -->
    <item name="android:itemBackground">@android:color/white</item>
    <!-- 设置Menu菜单的字体颜色 -->
    <item name="android:textColorPrimary">@android:color/black</item>
    <!-- 设置Menu窗口不覆盖Toolbar视图 -->
    <item name="overlapAnchor">false</item>
</style>

```


这样设置下的效果如下图所示：

![](/images/20170612_toolbar-sample-04.png)

另外，也可以获取 MenuItem 对象，然后通过如下方法设置 Item 的显示与隐藏：


```java

MenuItem item = menu.findItem(R.id.action_search);
item.setVisible(false);

```

或者直接移除所有 Item 内容：

```java

menu.clear();
ActionMenuView

```

Toolbar 默认将 Menu 内容显示在右边，那如何将其显示在左边或者中间呢？不妨试一下 ActionMenuView 。

ActionMenuView 是将原本位于 Toolbar 或者 ActionBar 中的 Menu 内容移到自己的名下，以 ViewGroup 的姿态将一系列的 Menu Item 囊括其中，再将自己搁置于 Toolbar 容器中，这样，更方便于管理和呈现 Menu 内容。所以，原本孤立的 Toolbar 控件，就有了一个 Child，代码如下：

```xml

<android.support.v7.widget.Toolbar
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/tb_toolbar"
    android:layout_width="match_parent"
    android:layout_height="?actionBarSize"
    android:background="@color/colorPrimary"
    app:theme="@style/OverFlowMenuTheme"
    app:popupTheme="@style/OverFlowMenuTheme"
    app:title="@string/app_name"
    app:titleTextColor="@android:color/white">

    <android.support.v7.widget.ActionMenuView
        android:id="@+id/amv_search"
        android:layout_gravity="center"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</android.support.v7.widget.Toolbar>

```



再在 Activity 文件中，将 Menu 资源文件加载到使用 findViewById() 方法获取到的 ActionMenuView 对象中：


```java

@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.home, mHomeAmv.getMenu());
    return super.onCreateOptionsMenu(menu);
}

```



至于 Menu Item 的点击事件方法 onOptionsItemSelected 无需改动，只要将 ActionMenuView 对象的点击事件设置转移即可：


```java

mHomeAmv = (ActionMenuView) findViewById(R.id.amv_home);
mHomeAmv.setOnMenuItemClickListener(new ActionMenuView.OnMenuItemClickListener() {
    @Override
    public boolean onMenuItemClick(MenuItem item) {
        return onOptionsItemSelected(item);
    }
});

```


还可以利用 getMenu() 方法获取到 Menu 对象，动态添加或删除 Menu Item，甚至加载另外一个 Menu 资源文件，比如：


```java

mHomeAmv.getMenu().clear();
getMenuInflater().inflate(R.menu.search, mSearchAmv.getMenu());

```

现在有这样一个需求，设计图如下 (图片来源：stack overflow)：

![](/images/20170612_toolbar-sample-11.png)


就可以利用 ActionMenuView 控制 Menu Item 的位置了。当然，这里面还需要另外几个知识点，顺便补充说明一下。

第一，Menu Item 间距问题。Item 默认的宽度是56dp ，可以利用 style 中的属性修改，并将样式设置给 Activity 主题即可，比如：


```xml

<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/colorPrimary</item>
    <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    <item name="colorAccent">@color/colorAccent</item>
    <item name="android:actionButtonStyle">@style/MyActionButtonStyle</item>
</style>

<style name="MyActionButtonStyle" parent="@android:style/Widget.Holo.ActionBar">
    <item name="android:minWidth">72dip</item>
    <item name="android:padding">0dp</item>
</style>

```


第二，Menu Item 图标高亮问题。当然，你可以使用两套图标，在点击时修改图标资源。但借助 ColorFilter 使用一套图标也能做到，还能减少 APK 文件大小，比如：

```java

public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.option_walk:
            item.getIcon().setColorFilter(ContextCompat.getColor(this,
                    android.R.color.darker_gray), PorterDuff.Mode.MULTIPLY);
            break;
    }
    return super.onOptionsItemSelected(item);
}

```


# actionLayout

通常情况下，Toolbar 中的 Menu Item 只显示为一个 Icon，通过 标签定义 title 和 icon 属性即可。但是，存在一些特殊情况，需要自定义 Menu Item 的内容，常见的有搜索页面，比如网易云音乐的搜索栏：

![](/images/20170612_toolbar-sample-12.jpg)

这种情况下，就需要额外定义一个 layout 文件，然后通过 actionLayout 属性引入到 标签中，比如：


```xml

<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto">

  <item
    android:id="@+id/action_search"
    android:title="搜索"
    app:actionLayout="@layout/menu_search"
    app:showAsAction="always"/>

</menu>

```

这里有两点需要注意：第一，menu_search 布局文件的内容必须以 RelativeLayout 作为根容器布局，否则，actionLayout 对应的视图宽度不足以填充满 Toolbar 或者说 ActionBar 的宽度，显示效果如同设置 layout_width 属性值为 wrap_content 一般；第二，actionLayout 属性必须使用 app 作为命名空间，如果使用 android 的话，会导致 menuItem 对象通过 getActionView() 始终获取的对象为 null 。

# Up Enable

在二级界面等Activity中，通过如下设置可以在Toolbar左边显示一个返回按钮：

```java

getSupportActionBar().setDisplayHomeAsUpEnabled(true);

```

然后通过android.R.id.home监听返回按钮的点击事件，比如可以返回上级Activity中：

```java

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case android.R.id.home:
            finish();
            break;
    }
    return super.onOptionsItemSelected(item);
}

```

当然，也可以通过为Toolbar设置导航图标的点击事件来达到这个监听效果：

```java

mToolbarTb.setNavigationOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        finish();
    }
});

```

显示效果如下：

![](/images/20170612_toolbar-sample-05.png)

至于导航返回按钮的图标，可以通过mToolbarTb.setNavigationIcon方法或者app:navigationIcon属性来修改。

# Toolbar Shadow

根据 Material Design 设计规范，Toolbar 是存在于页面 Content 之上的，也就是有一个 elevation 上的视差，类似阴影效果。

然而，无论是通过 android:elevation 属性，还是使用 AppBarLayout 包裹 Toolbar，都只能兼容 Lollipop 及更高版本的系统。要想兼容 Pre-Lollipop 设备，必须自己实现阴影效果。有两种选择，一种是使用点9背景图，另一种是使用 <gradient> 标签定制一个阴影渐变效果。看下后者的实现方式。

创建 xml 阴影形状，res/drawable/shadow.xml 文件：


```xml

<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient
        android:startColor="@android:color/transparent"
        android:endColor="#40000000"
        android:angle="90" />
</shape>

```

# 与 Toolbar 一起使用即可：


```xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <android.support.v7.widget.Toolbar
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary" />

    <View
        android:layout_width="match_parent"
        android:layout_height="4dp"
        android:background="@drawable/shadow" />

</LinearLayout>

```

备注：为了保持不同设备 App 体验的一致性，推荐统一使用这种方式实现 Toolbar 阴影效果，而不是类似这样做版本区分：在 Lollipop 及以上版本隐藏 shadow view，使用 elevation 属性；在 pre-Lollipop 中使用上述 shadow view。

# 标题居中

前面我们说过，Toolbar就是一个定制化的ViewGroup，所以可以在Toolbar里面放置一个TextView控件作为居中的标题来使用，再将Toolbar的Title隐藏起来即可实现Toolbar标题居中的效果，如：


```xml

<android.support.v7.widget.Toolbar
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/tb_toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:popupTheme="@style/OverFlowMenuTheme"
    app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:background="@color/colorPrimary">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="SecondActivity"
        style="@style/TextAppearance.AppCompat.Widget.ActionBar.Title"/>

</android.support.v7.widget.Toolbar>

```

这里给TextView设置了style属性，与ActionBar.Title保持一致，然后还需要去除Toolbar自有的Title，在布局中使用app:title=""是不会起作用的，会显示ActionBar的标题，所以在代码中隐藏ActionBar的标题即可：

```java

getSupportActionBar().setDisplayShowTitleEnabled(false);

```

效果如/images/下：

![](/images/20170612_toolbar-sample-06.png)

# “withText” 问题

Google 在设计 ActionBar 时，就约束了 menu item 中 icon 与 title 属性的关系。非溢出状态下不显示 title 文本。所以，即使在 app:showAsAction 属性中添加 withText 值也不一定能显示 title。更不用提只显示 title 不显示 icon 的情况。官方说明如下：

Note: The “withText” value is a hint to the action bar that the text title should appear. The action bar will show the title when possible, but might not if an icon is available and the action bar is constrained for space.
如果一定要展示 title text 的话，也不是不行，可以使用 showAsAction 属性指定 item 的布局内容。具体做法可参考：StackOverFlow。

# 搜索功能

系统提供了search dialog和search widget两种方式给Toolbar添加搜索功能，实现方式可参考 API 指南：Creating a Search Interface。但系统提供的搜索功能略显僵硬，既无法引起用户的搜索欲望，也无法满足多数的产品设计需求，适用性不强，所以这里不作过多介绍。相反，使用自定义的搜索功能并为之添加适当的转场过度动画，效果立竿见影，参考开源项目：Material-SearchTransition，对应博文介绍：Exposing the Searchbar，效果如下：

![](/images/20170612_transition-to-search.gif)

# Fragment中使用

有时候需要在Fragment中使用Toolbar，比如Activity中不同的Tab显示不同的Fragment，同时每个Tab的Toolbar标题、Menu均不相同，这时在Activity中使用同一个Toolbar就相当不方便了。我们可以在每个Fragment的布局中添加各自的Toolbar，然后在Fragment中单独控制。

与Activity中使用Toolbar有所不同。替换ActionBar时，需要给setSupportActionBar方法添加作用对象：

```java

((AppCompatActivity)getActivity()).setSupportActionBar((Toolbar) mContentView.findViewById(R.id.tb_toolbar));

```

添加Options Menu时，需要额外调用setHasOptionsMenu(true);方法，确保onCreateOptionsMenu()方法得以调用，并且onCreateOptionsMenu()方法多了一个MenuInflater参数：


```

@Override
public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
    super.onCreateOptionsMenu(menu, inflater);
    inflater.inflate(R.menu.search, menu);
}

```


