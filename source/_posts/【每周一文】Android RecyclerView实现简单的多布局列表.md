---
title: 【每周一文】Android RecyclerView实现简单的多布局列表
date: 2017-07-12 21:30:42
tags: [Android, RecyclerView]
---



在项目开发过程中，经常会出现多布局列表，本文的主要目的是帮初学者用 RecyclerView 实现简单的多布局列表，最终效果如下图：

![RecyclerView](/images/20170712_1.png "RecyclerView")

我们开始讲解如何实现。

<!-- more -->

# 一、编写布局代码

1、在主布局 ( content_multi_recycler_view.xml ) 中加入 RecyclerView 控件

```xml

<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    tools:context="me.entere.myapp.multirecyclerview.MultiRecyclerViewActivity"
    tools:showIn="@layout/activity_multi_recycler_view">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/id_recycler_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

    </android.support.v7.widget.RecyclerView>

</android.support.constraint.ConstraintLayout>


```

2、添加多个（2个）不同的 item 布局 item_multi_recycler_view_one.xml 和 item_multi_recycler_view_two.xml
item_multi_recycler_view_one.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:id="@+id/card_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"

    android:layout_marginBottom="10dp"
    android:foreground="?attr/selectableItemBackground"
    android:clickable="true"
    card_view:cardCornerRadius="0dp"
    card_view:cardElevation="1dp"
    card_view:contentPadding="3dp"
    >
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginTop="16dp"
        android:layout_marginBottom="16dp"
        android:orientation="vertical"
        >
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp"
            android:orientation="horizontal"
            >

            <TextView
                android:id="@+id/tv_category"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="以下内容来自公司栏目"
                android:textSize="14sp"
                android:textColor="@color/darkgrey"/>
            <TextView
                android:id="@+id/tv_publish_at"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="right"
                android:text="2小时前"
                android:textSize="12sp"
                android:textColor="@color/darkgrey"/>

        </LinearLayout>

        <View
            android:layout_width="match_parent"
            android:layout_height="1.0dp"
            android:layout_marginTop="16dp"
            android:layout_marginBottom="16dp"
            android:background="@color/whitesmoke" >

        </View>


        <LinearLayout

            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp" >

            <ImageView
                android:id="@+id/iv_image"
                android:layout_width="match_parent"


                android:layout_height="180dp"
                android:scaleType="centerCrop"
                android:src="@mipmap/item_recycler_image"
                />
            <TextView
                android:id="@+id/tv_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textSize="18sp"

                android:textColor="@color/black"
                android:text="项目需求讨论分页加载实现分析加载实现分析加载实现分析"
                android:lineSpacingMultiplier="1.3"
                android:layout_marginTop="16dp"

                android:maxLines="2"
                />
            <TextView
                android:id="@+id/tv_summary"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textSize="12sp"
                android:textColor="@color/gray"
                android:lineSpacingMultiplier="1.5"
                android:layout_marginTop="16dp"


                android:maxLines="2"
                android:text="大家好，又是新的一期项目需求讨论，这期的需求是关于分页加载。我本来先是网上看RecycleView的分页加载的方式，但是看到很多文章都是帮你封装好，然后让你拿来直接用，一是直接拿别人封装的东西自己还是不理解，二是如果要加定制化的东西，改别人的代码毕竟不方便，或者你就用了一个功能，别人封装好的可能包含很多功能，就多余了。所以我主要还是来分析，分页加载到底是怎么样一步步来实现，而不是说封装好来让大家使用。"
                />

        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginTop="8dp"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/tv_offline"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_gravity="center"

                android:text="已离线"
                android:textSize="8sp"
                android:layout_weight="1"
                android:textColor="@color/darkgrey"/>
            <ImageView
                android:id="@+id/iv_favourite"
                android:layout_width="20dp"
                android:layout_height="20dp"
                android:src="@drawable/ic_favorite_black_24dp"
                />
            <TextView
                android:id="@+id/tv_fav_num"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"

                android:layout_marginLeft="8dp"
                android:text="23"
                android:textSize="8sp"
                />
        </LinearLayout>
    </LinearLayout>
</android.support.v7.widget.CardView>

```

item_multi_recycler_view_two.xml


```xml

<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:id="@+id/card_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"

    android:layout_marginBottom="10dp"
    android:foreground="?attr/selectableItemBackground"
    android:clickable="true"
    card_view:cardCornerRadius="0dp"
    card_view:cardElevation="1dp"
    card_view:contentPadding="3dp"
    >
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginTop="16dp"
        android:layout_marginBottom="16dp"
        android:orientation="vertical"
        >
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp"
            android:orientation="horizontal"
            >

            <TextView
                android:id="@+id/tv_category"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="以下内容来自公司栏目"
                android:textSize="14sp"
                android:textColor="@color/darkgrey"/>
            <TextView
                android:id="@+id/tv_publish_at"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="right"
                android:text="2小时前"
                android:textSize="12sp"
                android:textColor="@color/darkgrey"/>

        </LinearLayout>

        <View
            android:layout_width="match_parent"
            android:layout_height="1.0dp"
            android:layout_marginTop="16dp"
            android:layout_marginBottom="16dp"
            android:background="@color/whitesmoke" >

        </View>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="horizontal"
            >

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical"
                android:layout_marginLeft="16dp"
                android:layout_marginRight="16dp"
                android:layout_weight="3"

                >

                <TextView
                    android:id="@+id/tv_title"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:textSize="18sp"

                    android:textColor="@color/black"
                    android:text="项目需求讨论分页加载实现分析加载实现分析加载实现分析"
                    android:lineSpacingMultiplier="1.3"

                    android:maxLines="2"
                    />
                <TextView
                    android:id="@+id/tv_summary"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:textSize="12sp"
                    android:textColor="@color/gray"
                    android:lineSpacingMultiplier="1.5"
                    android:layout_marginTop="16dp"
                    android:maxLines="2"
                    android:text="大家好，又是新的一期项目需求讨论，这期的需求是关于分页加载。我本来先是网上看RecycleView的分页加载的方式，但是看到很多文章都是帮你封装好，然后让你拿来直接用，一是直接拿别人封装的东西自己还是不理解，二是如果要加定制化的东西，改别人的代码毕竟不方便，或者你就用了一个功能，别人封装好的可能包含很多功能，就多余了。所以我主要还是来分析，分页加载到底是怎么样一步步来实现，而不是说封装好来让大家使用。"
                    />

            </LinearLayout>
            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="match_parent"

                android:layout_marginRight="16dp"
                android:layout_weight="6">
                <ImageView
                    android:id="@+id/iv_image"
                    android:layout_width="wrap_content"
                    android:layout_height="90dp"
                    android:scaleType="centerCrop"
                    android:src="@mipmap/item_recycler_image"
                    />
            </LinearLayout>
        </LinearLayout>
        
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginTop="8dp"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/tv_offline"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_gravity="center"

                android:text="已离线"
                android:textSize="8sp"
                android:layout_weight="1"
                android:textColor="@color/darkgrey"/>
            <ImageView
                android:id="@+id/iv_favourite"
                android:layout_width="20dp"
                android:layout_height="20dp"
                android:src="@drawable/ic_favorite_black_24dp"
                />
            <TextView
                android:id="@+id/tv_fav_num"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:layout_marginLeft="8dp"
                android:text="23"
                android:textSize="8sp"
                
                />
        </LinearLayout>
    </LinearLayout>
</android.support.v7.widget.CardView>

```


# 二、在MultiRececlerViewActivity中初始化我们的控件 


```java

    mRecyclerView = (RecyclerView) findViewById(R.id.id_recycler_view);
    mAdapter = new MultiRecyclerViewAdapter(this);
    mRecyclerView.setAdapter(mAdapter);

    LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false);
    mRecyclerView.setLayoutManager(linearLayoutManager);

```



三、创建 RecyclerView 适配器 MultiRecyclerViewAdapter

```java

public class MultiRecyclerViewAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder>{

    private ArrayList<DataModel> mDatas = new ArrayList<>();
    private LayoutInflater mLayoutInflater;


    public MultiRecyclerViewAdapter(Context mContext) {

        mLayoutInflater = LayoutInflater.from(mContext);
    }

    /**
     * 创建一个方法供外面操作此数据
     * @param list
     */
    public void addListData(ArrayList<DataModel> list) {
        mDatas.addAll(list);
    }


    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        switch (viewType) {

            case DataModel.TYPE_ONE:
                return new TypeOneViewHolder(mLayoutInflater.inflate(R.layout.item_multi_recycler_view_one,parent,false));

            case DataModel.TYPE_TOW:
                return new TypeTwoViewHolder(mLayoutInflater.inflate(R.layout.item_multi_recycler_view_two,parent,false));

        }
        return null;


    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        ((TypeAbstractViewHolder)holder).bindHolder(mDatas.get(position));
    }

    @Override
    public int getItemCount() {
        return mDatas.size();
    }

    @Override
    public int getItemViewType(int position) {
        return mDatas.get(position).type;
    }
}

```

上面这几行代码：

```java

switch (viewType) {

            case DataModel.TYPE_ONE:
                return new TypeOneViewHolder(mLayoutInflater.inflate(R.layout.item_multi_recycler_view_one,parent,false));

            case DataModel.TYPE_TOW:
                return new TypeTwoViewHolder(mLayoutInflater.inflate(R.layout.item_multi_recycler_view_two,parent,false));

        }

```
这是通过三个不同的ViewHolder实现不同的三个布局。其实此处是抽象出一个抽象的TypeAbstractViewHolder,而TypeOneViewHolder，TypeTwoViewHolder 分别继承于 TypeAbstractViewHolder，并实现bindHolder()方法

# 四、创建 DataModel 

```java

public class DataModel {

    public static final int TYPE_ONE = 1;
    public static final int TYPE_TOW = 2;

    public int type;
    public String id;
    public String name;
    public String title;
    public int image;
    public String publishAt;
    public String summary;
    public String category;

    public int getType() {
        return type;
    }

    public void setType(int type) {
        this.type = type;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public int getImage() {
        return image;
    }

    public void setImage(int image) {
        this.image = image;
    }

    public String getPublishAt() {
        return publishAt;
    }

    public void setPublishAt(String publishAt) {
        this.publishAt = publishAt;
    }

    public String getSummary() {
        return summary;
    }

    public void setSummary(String summary) {
        this.summary = summary;
    }

    public String getCategory() {
        return category;
    }

    public void setCategory(String category) {
        this.category = category;
    }
}

```

五、创建 TypeAbstractViewHolder  TypeOneViewHolder TypeTwoViewHolder


TypeAbstractViewHolder 代码

```java

public abstract class TypeAbstractViewHolder extends RecyclerView.ViewHolder{
    public TypeAbstractViewHolder(View itemView) {
        super(itemView);
    }

    public abstract void bindHolder(DataModel model);
}

```

TypeOneViewHolder 代码

```java

package me.entere.myapp.multirecyclerview;

import android.support.v7.widget.RecyclerView;
import android.view.View;
import android.widget.ImageView;
import android.widget.TextView;

import me.entere.myapp.R;

/**
 * Created by entere on 17/6/13.
 */

public class TypeOneViewHolder extends TypeAbstractViewHolder{
    TextView tvTitle;
    TextView tvSummary;
    TextView tvCategory;
    TextView tvName;
    TextView tvPublishAt;
    ImageView ivImage;

    public TypeOneViewHolder(View itemView) {
        super(itemView);

        tvTitle = (TextView) itemView.findViewById(R.id.tv_title);
        tvSummary = (TextView) itemView.findViewById(R.id.tv_summary);
        tvCategory = (TextView) itemView.findViewById(R.id.tv_category);

        tvPublishAt = (TextView) itemView.findViewById(R.id.tv_publish_at);
        ivImage = (ImageView) itemView.findViewById(R.id.iv_image);;
    }

    @Override
    public void bindHolder(DataModel dataModel) {
        tvTitle.setText(dataModel.getTitle());
        tvSummary.setText(dataModel.getSummary());
        tvCategory.setText(dataModel.getCategory());
        tvPublishAt.setText(dataModel.getPublishAt());
        ivImage.setImageResource(dataModel.getImage());

    }
}


```

TypeTwoViewHolder 代码

```java

package me.entere.myapp.multirecyclerview;

import android.support.v7.widget.RecyclerView;
import android.view.View;
import android.widget.ImageView;
import android.widget.TextView;

import me.entere.myapp.R;

/**
 * Created by entere on 17/6/13.
 */

public class TypeTwoViewHolder extends TypeAbstractViewHolder{
    TextView tvTitle;
    TextView tvSummary;
    TextView tvCategory;
    TextView tvName;
    TextView tvPublishAt;
    ImageView ivImage;

    public TypeTwoViewHolder(View itemView) {
        super(itemView);

        tvTitle = (TextView) itemView.findViewById(R.id.tv_title);
        tvSummary = (TextView) itemView.findViewById(R.id.tv_summary);
        tvCategory = (TextView) itemView.findViewById(R.id.tv_category);

        tvPublishAt = (TextView) itemView.findViewById(R.id.tv_publish_at);
        ivImage = (ImageView) itemView.findViewById(R.id.iv_image);;
    }


    @Override
    public void bindHolder(DataModel dataModel) {
        tvTitle.setText(dataModel.getTitle());
        tvSummary.setText(dataModel.getSummary());
        tvCategory.setText(dataModel.getCategory());
        tvPublishAt.setText(dataModel.getPublishAt());
        ivImage.setImageResource(dataModel.getImage());


    }
}


```

# 六、MultiRececlerViewActivity 初始化数据

```java

public class MultiRecyclerViewActivity extends AppCompatActivity {

    public RecyclerView mRecyclerView;
    public MultiRecyclerViewAdapter mAdapter;
    public ArrayList<DataModel> mDatas = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_multi_recycler_view);

        initViews();
        initDatas();



    }

    private void initViews() {
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        toolbar.setNavigationIcon(R.drawable.ic_arrow_back_black_24dp);//设置导航栏图标
        //getSupportActionBar().setDisplayHomeAsUpEnabled(true);

        mRecyclerView = (RecyclerView) findViewById(R.id.id_recycler_view);
        mAdapter = new MultiRecyclerViewAdapter(this);
        mRecyclerView.setAdapter(mAdapter);

        LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false);
        mRecyclerView.setLayoutManager(linearLayoutManager);
    }

    private void initDatas() {

        DataModel dataModel1 = new DataModel();

        dataModel1.setType(2);
        dataModel1.setId("1");
        dataModel1.setName("抑郁症");
        dataModel1.setTitle("Woebot：这个聊天机器人要帮人们对抗抑郁症");
        dataModel1.setSummary("聊天机器人收集情感数据，发现一些人类不易察觉的模式。同时，它也会询问一些问题，定期检查病人的状况。");
        dataModel1.setPublishAt("1 小时前");
        dataModel1.setCategory("以下内容来自「科技」栏目");
        dataModel1.setImage(R.mipmap.pic_recycler_view_1);
        mDatas.add(dataModel1);

        DataModel dataModel2 = new DataModel();
        dataModel2.setType(1);
        dataModel2.setId("2");
        dataModel2.setName("年轻人");
        dataModel2.setTitle("宜家跨界有点猛，不仅和苹果玩 AR，还把设计师扔去当宇航员了");
        dataModel2.setSummary("近几年来，宜家合作的设计师类别开始发生改变。在去年的“民主设计日”上，宜家正式宣布和英国鬼才设计师合作。");
        dataModel2.setPublishAt("2 小时前");
        dataModel2.setCategory("以下内容来自「宜家」栏目");
        dataModel2.setImage(R.mipmap.pic_recycler_view_2);
        mDatas.add(dataModel2);

        DataModel dataModel3 = new DataModel();
        dataModel3.setType(2);
        dataModel3.setId("3");
        dataModel3.setName("苹果");
        dataModel3.setTitle("苹果运动鞋要开卖了，40 台 iPhone 7 可能也买不了一双");
        dataModel3.setSummary("乔布斯或许也有一个时尚梦。");
        dataModel3.setPublishAt("3 小时前");
        dataModel3.setCategory("以下内容来自「时尚」栏目");
        dataModel3.setImage(R.mipmap.pic_recycler_view_3);
        mDatas.add(dataModel3);

        DataModel dataModel4 = new DataModel();
        dataModel4.setId("4");
        dataModel4.setType(1);
        dataModel4.setName("特斯拉");
        dataModel4.setTitle("第二季遇严重车祸，特斯拉被中国人推上了五百强榜单");
        dataModel4.setSummary("可怜鼹鼠老兄三次大事故之后，以后真的要小心飙车了。");
        dataModel4.setPublishAt("4 小时前");
        dataModel4.setCategory("以下内容来自「汽车」栏目");
        dataModel4.setImage(R.mipmap.pic_recycler_view_4);
        mDatas.add(dataModel4);


        mAdapter.addListData(mDatas);
        mAdapter.notifyDataSetChanged();

    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case android.R.id.home:
                finish();
                break;
        }
        return super.onOptionsItemSelected(item);
    }
}

```


