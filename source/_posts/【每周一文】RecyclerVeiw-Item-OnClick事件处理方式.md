---
title: 【每周一文】RecyclerVeiw Item OnClick事件处理方式
date: 2017-07-31 22:18:31
tags: [Android, RecyclerView]
---

RecyclerView 需要我们自己手动处理它的点击事，本文用两种方式来实现 RecyclerView 的 OnClick事件。

# 方法一 利用RecyclerView.OnItemTouchListener

### 1.1 新建 RecyclerItemClickListener.java:

<!-- more -->

```java

public class RecyclerItemClickListener implements RecyclerView.OnItemTouchListener{
    private OnItemClickListener mListener;
    private GestureDetector mGestureDetector;

    // 点击回调
    public interface OnItemClickListener {
        public void onItemClick(View view, int position);

        public void onItemLongClick(View view, int position);
    }

    public RecyclerItemClickListener(Context context,
                                     final RecyclerView recyclerView, OnItemClickListener listener) {
        mListener = listener;
        // 识别并处理手势
        mGestureDetector = new GestureDetector(context,
                new GestureDetector.SimpleOnGestureListener() { ////这里选择SimpleOnGestureListener实现类，可以根据需要选择重写的方法
                    @Override
                    public boolean onSingleTapUp(MotionEvent e) {
                        // 轻击触摸屏后,弹起，必须返回true，否则无法触发单击
                        View childView = recyclerView.findChildViewUnder(
                                e.getX(),e.getY());
                        if(childView != null && mListener != null){
                            mListener.onItemClick(childView,recyclerView.getChildLayoutPosition(childView));
                            return true;
                        }
                        return false;

                    }

                    @Override
                    public void onLongPress(MotionEvent e) {
                        // 长按
                        // 根据findChildViewUnder(float x, float y)来算出哪个item被选择了
                        View childView = recyclerView.findChildViewUnder(
                                e.getX(), e.getY());
                        // 有item被选则且监听器不为空触发长按事件
                        if (childView != null && mListener != null) {
                            mListener.onItemLongClick(childView,
                                    recyclerView.getChildLayoutPosition(childView));
                        }
                    }
                });
    }


    @Override
    public boolean onInterceptTouchEvent(RecyclerView rv, MotionEvent e) {
        if(mGestureDetector.onTouchEvent(e)) {
            return true;
        } else {
            return false;
        }


    }

    @Override
    public void onTouchEvent(RecyclerView rv, MotionEvent e) {

    }

    @Override
    public void onRequestDisallowInterceptTouchEvent(boolean disallowIntercept) {

    }
}


```


### 1.2 在 Activity 中调用

```java

mRecyclerView.addOnItemTouchListener(new RecyclerItemClickListener(getApplicationContext(), mRecyclerView, new RecyclerItemClickListener.OnItemClickListener() {
            @Override
            public void onItemClick(View view, int position) {
                Toast.makeText(getApplicationContext(),"Click "+mData.get(position),Toast.LENGTH_SHORT).show();
                
            }

            @Override
            p
            ublic void onItemLongClick(View view, int position) {
                Toast.makeText(getApplicationContext(),"Long Click "+mData.get(position),Toast.LENGTH_SHORT).show();
            }
        }));


```

结束，这种方法不用在adapter侵入代码，松耦合


# 方法二 在 Adapter 里面创建 View 时添加点击事件

此方法不建议～～

RecyclerView 的更全面的使用请查看 [Android RecyclerView 使用完全解析 体验艺术般的控件](http://blog.csdn.net/lmj623565791/article/details/45059587)