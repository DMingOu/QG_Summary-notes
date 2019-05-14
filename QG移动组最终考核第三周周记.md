# QG移动组最终考核第三周周记

```
<android.support.v7.widget.Toolbar   
   ...
   ...
   app:layout_scrollFlags="scroll|enterAlways|snap"/>
```

scroll ：当RecyclerView向上滚动的时候，ToolBar会跟着一起向上滚动并实现隐藏
enterAlways ：当RecyclerView向下滚动的时候，Toolbar会跟着一起向下滚动并重新显示
snap ：当ToolBar还没有隐藏或者显示的时候，会根据当前滚动的距离，自动选择时显示还是隐藏

若要恒定显示出Toolbar的话，修改代码

```
<android.support.v7.widget.Toolbar
	...
    app:layout_scrollFlags="enterAlways|enterAlways|enterAlways"/>
```

##### 问题：Toolbar会挡住了RecyclerView的显示

#### 解决：

用AppBarLayout能解决前面的覆盖问题

其实只需要两步就可以了：

1.将ToolBar嵌套到AppBarLayout中

2.给RecyclerView指定一个布局的行为

```
修改前：
<android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent" /> 
            
修改后：
<android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" //这里不是match_view的状态了
            app:layout_behavior="@string/appbar_scrolling_view_behavior"/>
```

#### 把软键盘的回车按键变成搜索按键

xml文件设置 EditText 属性，在activity.java文件设置监听和软键盘的隐藏

```
android:imeOptions="actionSearch"
android:singleLine="true"//保证不会有换行键
```

```
/**
 * 隐藏软键盘
 * @param view ：一般是EditText使用
 */
public void hideKeyboard(View view) {
    InputMethodManager manager = (InputMethodManager) view.getContext()
            .getSystemService(Context.INPUT_METHOD_SERVICE);
    manager.hideSoftInputFromWindow(view.getWindowToken(), 0);
}
```

```
//监听软键盘的输入
mSearchEt.setOnEditorActionListener(new TextView.OnEditorActionListener() {
    @Override
    public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
        if (actionId == EditorInfo.IME_ACTION_SEARCH) {
            //点击搜索的时候隐藏软键盘
            hideKeyboard(mSearchEt);
            Toast.makeText(Search_ArticleActivity.this,"搜索按钮被点击",Toast.LENGTH_SHORT).show();
            // 这里写搜索键的操作----网络请求数据
            return true;
        }
        return false;
    }
});
```

改变EditText样子--好看点

创建一个在drawable文件夹中创建一个对应shape文件

shape_editview_search.xml

```
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle" >

    <padding
        android:bottom="7dp"
        android:left="7dp"
        android:right="7dp"
        android:top="7dp" />
    <!-- 设置圆角矩形 -->
    <corners android:radius="5dp" />
    <stroke
        android:width="5px"
        android:color="@color/green" />
    <solid 
    android:color="@color/shallow_white" />

</shape>
```

然后在EditText对应的XML文件里引用它就可以

```
android:background="@drawable/shape_editview_search"
```

#### JSON数据含有html语言的内容的处理

搜索接口返回的数据，title属性带有

```
”<em class='highlight'>"  "</em>"
 "title": "<em class='highlight'>Flutter</em>豆瓣客户端(仿)，诚心开源",
```

本意应该是让搜索关键词可以变为斜体，有特定样式

但我为了不让它们显示在标题上，需要写一个方法来让它们变为""

```
    /**
     * 搜索接口返回的数据，title属性带有<em class='highlight'>与</em>，不让它们显示在标题上
     * @param uncorrent_title
     * @return
     */
    public static String title_fix(String uncorrent_title){
        String corrent_title = "";
        uncorrent_title = uncorrent_title.replace("<em class='highlight'>","");
        corrent_title = uncorrent_title.replace("</em>","");
        return corrent_title;
    }
}
```

##### 问题：如果要求显示关键词就是要有特殊样式捏？

##### 解决：

思路：在安卓项目里面使用html语言，设置进TextView

Adapter.java文件onBindView() 方法

```
前：
holder.mTitleTv.setText((article.getTitle());
后：
holder.mTitleTv.setText(Html.fromHtml(article.getTitle()));
```



##### 问题：注册界面和登录界面设置成可以互相跳转，这样导致跳转几次后按返回键要返回n次才回主界面

暂时解决：

```
<activity
    android:name=".Activity.LoginActivity"
    android:launchMode="singleTask"/>
<activity
    android:name=".Activity.RegisterActivity"
    android:launchMode="singleTask"/>
```

singleTask模式，只创造一次这个活动，如果有就直接启动它，抹除掉之前栈上面的活动，没有创造过就初次启动它



#### 下拉刷新数据：

##### 问题：ToolBar和SwipeRefreshLayout布局冲突怎么办

解决：使用`SwipeRefreshLayout`包裹`RecyclerView`需要把`app:layout_behavior="@string/appbar_scrolling_view_behavior"`这句添加到`SwipeRefreshLayout`中。

#### 问题：报错-- java.lang.IndexOutOfBoundsException: Inconsistency detected. Invalid view holder adapter positionViewHolder{f0c6804 position=9 id=-1, oldPos=-1, pLpos:-1 no parent}

导致APP闪退（貌似是谷歌的问题？但还是要自己修复下）

解决：

重写一个继承LineLayoutManger的类

```
public class RecyclerViewNoBugLinearLayoutManager extends LinearLayoutManager{

    public RecyclerViewNoBugLinearLayoutManager(Context context) {
        super( context );
    }

    public RecyclerViewNoBugLinearLayoutManager(Context context, int orientation, boolean reverseLayout) {
        super( context, orientation, reverseLayout );
    }

    public RecyclerViewNoBugLinearLayoutManager(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super( context, attrs, defStyleAttr, defStyleRes );
    }

    @Override
    public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
        try {
            super.onLayoutChildren( recycler, state );
        } catch (IndexOutOfBoundsException e) {
            e.printStackTrace();
        }

    }
}
```

然后用这个类的实例

```
//        lineLayoutManager = new LinearLayoutManager(MainActivity.this);
//        lineLayoutManager.setOrientation(OrientationHelper.VERTICAL);
        RVlinelayoutManager = new RecyclerViewNoBugLinearLayoutManager(MainActivity.this);
        RVlinelayoutManager.setOrientation(OrientationHelper.VERTICAL);
        mRecyclerView.setLayoutManager(RVlinelayoutManager);
```

但还是会有下拉刷新瞬间上滑的话，会导致程序闪退回桌面

#### 解决方法：在RecyclerViewNoBugLinearLayoutManager里重写一个方法：不让下拉刷新瞬间可以上滑

```
@Override
public int scrollVerticallyBy(int dy, RecyclerView.Recycler recycler, RecyclerView.State state) {
    try {
        return super.scrollVerticallyBy(dy, recycler, state);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return 0;
}
```



##### 在首次启动的页面加载数据时就显示刷新动画

在 onCreate() 里面，将原来的

 mALTask.execute(ArticleListPath);

 替换成

```
//首次启动就加载刷新动画
mSwipeRefreshLayout.post(new Runnable() {
      @Override
      public void run() {
          mSwipeRefreshLayout.setRefreshing(true);
          new ArticleListTask().execute(ArticleListPath);
      }
  });
```





notifyItemChanged(int position) 更新列表position位置上的数据可以调用 

notifyItemInserted(int position) 列表position位置添加一条数据时可以调用，伴有动画效果 notifyItemRemoved(int position) 列表position位置移除一条数据时调用，伴有动画效果

notifyItemMoved(int fromPosition, int toPosition) 列表fromPosition位置的数据移到toPosition位置时调用，伴有动画效果

 notifyItemRangeChanged(int positionStart, int itemCount) 列表从positionStart位置到itemCount数量的列表项进行数据刷新 

notifyItemRangeInserted(int positionStart, int itemCount) 列表从positionStart位置到itemCount数量的列表项批量添加数据时调用，伴有动画效果 

notifyItemRangeRemoved(int positionStart, int itemCount) 列表从positionStart位置到itemCount数量的列表项批量删除数据时调用，伴有动画效果

存储已经展示出来的文章的id

```
ContentValues values = new ContentValues();    //创建存放数据的ContentValues对象
values.put("id",mArticleList.get(position).getId()); //存储RecycleView即将显示出来的文章的id
db.insert("Article",null,values); //数据库执行插入命令
```

在处理关于文章的JSON数据的方法内部，判定是否需要添加这个子项进ArticleList

```
for(int i = 0; i <datas.length(); i++) {
//通过传参进来判定此时处理的是需要更新的数据还是普通的加载下一页的数据
 if(isRefresh){
    boolean isShown = false;
    //当前在刷新文章，需要过滤掉已有的文章，需要显示id还没出现在数据库的文章
    Cursor cursor = db.query("Article",null, null, null, null, null, null);
    if (cursor.moveToFirst()) {
        do {
           int id = cursor.getInt(cursor.getColumnIndex("id"));
            if (content.getInt("id") == id) { //数据库有相同的标题（读过的)，设置为灰色，否则设置为黑色
                isShown = true;
                break;
            }
        } while (cursor.moveToNext());
    }
    if(isShown) {
        continue; //如果这篇文章已经被展示了，跳过这篇文章
    }
}
```



监听刷新的动作成立后的操作

```
//下滑刷新操作
mSwipeRefreshLayout = (SwipeRefreshLayout) findViewById(R.id.swipe_refresh_layout_main); //下拉刷新布局
mSwipeRefreshLayout.setColorSchemeColors(Color.parseColor("#009a61"),Color.parseColor("#FF0000"));// green ,red
mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
    @Override
    public void onRefresh() {
        isloading = false;
        load_times = 0;
        //下滑刷新，新开一个Task对象--重新请求网络数据
        isRefresh = true;
        new ArticleListTask().execute(ArticleListPath);
    }
});
}
```

如果的确有被更新出来的文章，它会出现在RecycleView的末尾了，所以我们要把它提上来

```
//如果文章数组小于固定数量20说明里面这部分都是新的文章，需要从列表末尾调回顶部
 if(mArticleList.size() < 20 && mArticleList.size() > 0) {
                for(int i = mArticleList.size() ; i >= 1; i-- )
                articleAdapter.notifyItemMoved(articleAdapter.getItemCount(),0);
                articleAdapter.notifyItemRangeChanged(0,articleAdapter.getItemCount()); //刷新位置，防止数据的位置紊乱
            }
```





#### 上滑加载

监听用户是否有上滑加载的操作

```
mRecyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
        super.onScrolled(recyclerView, dx, dy);
        int endCompletelyPosition = linearLayoutManager.findLastVisibleItemPosition();
        int totalItemCount = linearLayoutManager.getItemCount();
        if(!isloading && totalItemCount < (endCompletelyPosition + 2 ) ) {
            System.out.println("执行上拉加载");
            isloading = true;
            new ArticleListTask().execute(ArticleListPath);
        }
    }
});
```

为了控制每次进行上滑加载时候都可以网络请求下一页的内容，要用一个变量记录

```
protected void onPostExecute(String resultdata) {
....
            if(! isloading && isRefresh ) {
                //下滑刷新后定位回第一篇文章
                mRecyclerView.scrollToPosition(0);
                isRefresh = false;
                isloading = false;
            }else{
                //上拉加载后
                load_times++;//加载次数+1
                isRefresh = false;
                isloading = false;
                //上拉加载后，定位到加载出来的位置附近。每一页都有20篇文章
                mRecyclerView.scrollToPosition(articleAdapter.getItemCount() - 18);
            }
 }
```

加载时就令 i保持在最新要出来的一页

```
protected String doInBackground(String... params) {
    //mArticleList.clear();
    for (int i = 0; i < 1; i++) {
        //初始化文章列表里面的数据
        //publishProgress(i);
       if(isloading ) {
           i = load_times + i;//加载更多时，令i保持在最新要出来的一页
       }
        resultdata =  GetUtil.sendGet(params[0] + i + "/json", articleJsondata, handler);
    }
    //mArticleList.clear();
    return resultdata;
 }
```

下面的还有滑到最后一项附近的时间，要显示出  加载更多的 这个子项，所以又要在 Adapter 判定，加载布局

```
public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if(mContext == null){
            mContext = parent.getContext();
        }
        //如果达到最后一个item就加载 "加载更多" 这个view
        if(viewType == ITEM_TYPE_FOOTER) {
            View view = LayoutInflater.from(mContext).inflate(R.layout.item_footer_loadingmore,parent,false);
            return new FooterViewHolder(view);
        } else {
        View view = LayoutInflater.from(mContext).inflate(R.layout.item_article, parent ,false);
        dbhelper =  new ArticlebaseHelper(mContext,"Article.db",null,1);
        return new ItemArticleViewHolder(view);
     }
    }
 public void onBindViewHolder(final RecyclerView.ViewHolder holder, int position){
        //传进来的是底部加载的holder
        if(holder instanceof  FooterViewHolder){
            ((FooterViewHolder) holder).mLoadTv.setText("正在加载中");
            return;
        }
        ...
        ...
       }
       
```

何时才有这个展示这个”加载更多“这个特殊子项的信号呢

```
/**
 * 上滑到最后的item时返回0，否则返回1
 * @param position
 * @return
 */
@Override
public int getItemViewType(int position) {
   if (position == getItemCount()-1 ){
        return ITEM_TYPE_FOOTER;
    }else {
        return 1; //只要不是0，emm
    }
}
```



问题：

搜索界面居然每次加载都会导致清空原有数据再重新加载下一页数据，导致无法显示上一页

解决：

找到罪魁祸首，在doInBackground方法里忘记删除这个，导致了每次都清空再加载

```
protected String doInBackground(String... params) {
//articleList.clear();
...
}
```



##### 问题：java.lang.IllegalStateException: attempt to re-open an already-closed object

不要以为多获取了几个SQLiteDatabase对象就可以了，每个线程只能使用一个SQLiteOpenHelper，也就使得每个线程使用一个SQLiteDatabase对象（多线程操作数据库会报错）；

解决办法就是不再关闭内部数据库查询方法的SQLiteDatbase对象或者将那个方法直接集成到外面的查询方法。





问题：Tool标题居中？

网上方法大部分是 Toolbar里面再放一个TextView可以设置居中布局，我用个比较low的方法：

```
//在Toolbar里面设置这个属性
app:titleMarginStart="121dp"
```



#### 去掉在Android P上的提醒弹窗 （Detected problems with API compatibility(visit g.co/dev/appcompat for more info)

网上找的方法：

Android P 后谷歌限制了开发者调用非官方公开API 方法或接口，也就是说，你用反射直接调用源码就会有这样的提示弹窗出现，非 SDK 接口指的是 Android 系统内部使用、并未提供在 SDK 中的接口，开发者可能通过 Java 反射、JNI 等技术来调用这些接口。但是，这么做是很危险的：非 SDK 接口没有任何公开文档，必须查看源代码才能理解其行为逻辑。

在APP启动的时候使用一次就ok了

```
private void closeAndroidPDialog(){
        try {
            Class aClass = Class.forName("android.content.pm.PackageParser$Package");
            Constructor declaredConstructor = aClass.getDeclaredConstructor(String.class);
            declaredConstructor.setAccessible(true);
        } catch (Exception e) {
            e.printStackTrace();
        }
        try {
            Class cls = Class.forName("android.app.ActivityThread");
            Method declaredMethod = cls.getDeclaredMethod("currentActivityThread");
            declaredMethod.setAccessible(true);
            Object activityThread = declaredMethod.invoke(null);
            Field mHiddenApiWarningShown = cls.getDeclaredField("mHiddenApiWarningShown");
            mHiddenApiWarningShown.setAccessible(true);
            mHiddenApiWarningShown.setBoolean(activityThread, true);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```



收尾部分：

撸一个用户页面，可以跳转去登录注册的界面，也可以退出登录，删除本地用户名和密码

修复若干bug



#### 使用APP，网络断了，弹出强制对话框提醒去连接网络（广播）

从网上学习的一个比较普通的方法

首先写一个类，继承广播类

```
public class InterRecevier extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        ConnectivityManager manager = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo networkInfo = manager.getActiveNetworkInfo();
        if(networkInfo != null && networkInfo.isAvailable()) {
            Toast.makeText(context,"网络成功连接",Toast.LENGTH_SHORT).show();
        } else {
            Dialog(context);
        }
    }
//对话框的属性
    private void Dialog (final Context context) {
        AlertDialog.Builder builder = new AlertDialog.Builder(context);
        builder.setTitle("提示");
        builder.setMessage("网络中断");
        builder.setCancelable(false);//不可以取消
        builder.setPositiveButton("设置", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                //context.startActivity(new Intent("android.net.wifi.PICK_WIFI_NETWORK")); //跳转到手机WIFI设置
                context.startActivity(new Intent(Settings.ACTION_DATA_ROAMING_SETTINGS)); //跳转到手机流量设置
            }
        });
        builder.setNegativeButton("确认", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {

            }
        });
        AlertDialog dialog = builder.create();
        dialog.show();
    }

}
```



注册广播

```
 private BroadcastReceiver receiver = new InterRecevier();
//在onCreate方法里
IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION); //动态注册广播
this.registerReceiver(receiver,filter);//注册广播


@Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(receiver);//注销注册的广播
    }
```

不能忘了去注册权限

```
<uses-permission android:name="android.permission.INTERNET" /><!-- 网络权限 --> `
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> <!-- 读取网络状态 -->
```

问题：要是弱网，断网情况下就进APP，虽然设置后恢复网络可还是要显示出来啊

解决方法：自定义一个Handler类，在广播接收器和主活动搭桥，当网络恢复好后，就通知活动去请求数据，加载文章出来吧。

 HandlerManger.java

```
public class HandlerManger {
    public static final String TAG = "Manager";

    private static HandlerManger mInstance;

    public Handler mHandler;

    public synchronized static HandlerManger getInstance() {
        if (mInstance == null) {
            mInstance = new HandlerManger();
        }
        return mInstance;
    }

    public void setHandler(Handler handler) {
        this.mHandler = handler;
    }

    /**
     * 利用Handler发送消息
     */
    public void sendSuccessMessage() {
        mHandler.sendEmptyMessage(0x01);
    }

}
```

MainActivity.java 

```
Handler handler = new Handler(){//此函数是属于MainActivity.java所在线程的函数方法，所以可以直接调用MainActivity的 所有方法。
     public void handleMessage(Message msg) {
         if (msg.what == 0x01) {
             if(articleList.size() == 0) {
                 //第一篇文章都还没加载出来
                 new ArticleListTask().execute(BaseUrl.getArticleListPath());
             }
             System.out.println("收到handler消息");
         } else {
             Toast.makeText(MainActivity.this, "请重新输入地址：", Toast.LENGTH_SHORT).show();
         }
     }
 };
 
 //onCreate里
 HandlerManger.getInstance().setHandler(handler);
```

广播接收器的onReceive方法的判定网络畅通后，调用

```
HandlerManger.getInstance().sendSuccessMessage();//发送消息
```