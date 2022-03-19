---
layout: post
title: RecyclerView
category: android
tags: [android,RecyclerView]
---

RecyclerView可以高效显示大量数据，可以取代ListView、GridView。

## 跟ListView的区别
### 优势
1. ItemView切换动画方便；
2. 局部刷新（最重要的优势）。


### 区别
1. ListView复用的是View，RecyclerView复用的是`ViewHolder`（View的封装类）；
2. 多了`LayoutManager`类，用来测量和绘制整个RecyclerView；
3. 使用`ItemAnimator`设置ItemView切换动画（ListView没有）；
4. 通过`ItemDecoration`设置下划线等；
5. 有两个暂存区`ChangedScrap`和`AttachedScrap`（用来做ItemView动画）
6. 多个RecyclerView可以共用同一个回收池（RecyclerViewPool）。


## RecyclerView的基本结构
- RecyclerView：是一个类，也是一个ViewGroup；
- LayoutManager：负责测量、绘制整个RecyclerView；
- Adapter：负责把数据映射成ItemView（绘制ItemView，封装到ViewHolder）；


其他的一些类：
- Recycler：复用回收器；
- ItemAnimator：ItemView局部刷新动画；
- ItemDecoration：添加额外的View，比如下划线。


## RecyclerView的运行机制

1. RecyclerView通知LayoutManager要显示View；
2. LayoutManager向Recycler（复用回收器）获取ItemView；
3. Recycler通过缓存或者新建ItemView，通知Adapter把数据Data绑定到ItemView，返回ViewHolder；
4. LayoutManager通过ViewHolder里面的ItemView进行测量、布局并显示整个ViewGroup（RecyclerView）。

![](https://gitee.com/jairustes/blogimages/raw/master/img/20220319184150.png)

## RecyclerView的缓存机制
暂存区：一次重新布局发挥临时作用（局部刷新一个position同时有两个ItemView，一个进场一个出场）
- ChangedScrap：存放旧的ItemView（存放pre-layout）
- AttachedScrap：存放新的ItemView（存放post-layout）

缓存区：
- CacheViews：可以直接复用的View（滑动出屏幕重新可见的ItemView）
- RecyclerViewPool：重新绑定数据后可以复用（不需要重新创建View）


## notifyDataSetChanged()

RecyclerView调用`notifyDataSetChanged()`，所有的ItemView都会直接进入回收池`RecyclerViewPool`（不会进入暂存区scrap），RecyclerViewPool保存的每种ViewTag的数量是有限的，超出的ItemView需要重新创建`onCreate`、重新绑定数据`onBind`，创建ItemView是开销比较大的处理，绑定数据复杂开销也比较大。  


**RecyclerView更新数据的API：**
```
notifyItemInserted(position) //增加ItemView
notifyItemRemoved(position) //删除ItemView
notifyItemChanged(position, "payload") //更新，默认整个ItemView重新绑定数据；payload：局部刷新
notifyDataSetChanged() //所有ItemView进入回收池
```



