---
title: 【每周一文】Android iconfont的使用
date: 2016-07-22 23:58:47
tags: [Android,iconfont]
---

# iconfont 简单使用

iconfont是阿里妈妈MUX倾力打造的矢量图标管理、交流平台。关于如何把 iconfont 应用到 Android 项目中，官网有很详细的说明 [http://www.iconfont.cn/help/detail?helptype=code](http://www.iconfont.cn/help/detail?helptype=code)

<!-- more -->

总结起来有以下几步：

1、从iconfont平台选择要使用到的图标加入购物车，然后下载代码到本地

2、把下载代码中的 iconfont.svg 和 iconfont.ttf 文件导入到项目中的 assets/iconfont 文件夹中；

3、用 TextView 代替 ImagerView，找到图标相对应的 HTML 实体字符码给 textView 设置；


4、textview 设置大小跟颜色，图标的大小颜色也会改变（这里大小最好用dp为单位，这样不会随着手机字体大小的改变而改变图标的大小）；

5、为 Textview 设置指定的 ttf 文字；


# iconfont 简单封装

如果按[官方帮助文档](http://www.iconfont.cn/help/detail?helptype=code)每次都给 TextView 设置指定文字是不是也很繁琐,而且一直不断的在读取 iconfont.ttf 文字,也很浪费内存,我们完全可以把这个抽离出来,说干就干。

首先我们要读取到的是 assets/iconfont 目录下的 iconfont.ttf 文件；这里我把它放到自定义的 Application 中，这样就只要读取一次，代码如下：

```java

public class IconfontApplication extends Application{

    public static Typeface iconfont;

    public static Typeface getIconfont(Context context) {
        if(iconfont != null ) {
            return iconfont;

        } else {
            iconfont = Typeface.createFromAsset(context.getAssets(),"iconfont/iconfont.ttf");
        }
        return iconfont;
    }
}

```

这里就实例化了 iconfont。然后给每 TextView 设置 Typeface，这肯定不是我们想要的，所以我们自定义一个 TextView 然后初始化时 setTypeface 就可以了代码如下：

```java

public class MyIconTextView extends AppCompatTextView {

    public MyIconTextView(Context context) {
        super(context);
        init(context);
    }

    public MyIconTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context);
    }

    public MyIconTextView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context);
    }

    private void init(Context context) {
        setTypeface(IconfontApplication.getIconfont(context));
    }
}

```

然后就可以直接在 layout 文件中使用了：


```xml

<me.entere.myapp.iconfont.MyIconTextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/iconfont_add"
        android:textColor="@color/red"
        android:textSize="72dp"
        />

```

strings.xml:

```xml

<resources>
    
    <string name="iconfont_add">&#xe6df;</string>

</resources>


```

# iconfont动态设置

动态设置通俗的说就是在代码里动态的调整图标的大小颜色或改变图片，iconfont 改变大小颜色这很简单直接调用 TextView 的 setTextSize 和 setTextColor 就可以了，动态设置图片是不是 setText 呢？

```java

textview.setText("&#xe643;");

```

运行发现并不如我们所愿，这里涉及到 unicode字符 的问题,把代码稍改一下：

把 "&#x" 替换成 "\u" 用unicode字符表示

```java

textview.setText("\ue643;");

```

这样问题就解决了：























