### NullPointException --粗心才会出的异常

AS 编译没报错，但是APP闪退，查看异常，发现是空对象的引用。

是一个 recycleView的 setLayoutManager()报异常，一开始以为是我的LayoutManger没有正确创建，后来给整段关于LayoutManger的代码打上注释，继续报错，才回神根本就是recycleView自己出错，导致空对象引用。

一开始没看出，后来发现，应该是传入recycleView的 id出了错，我是在一个独立的xml文件写了recycleView出来，但是那个活动并没有引用这个布局文件，所以算是白引用。

解决方法：

将对应活动的布局文件换成拥有RecycleView的那个布局文件，就可以成功创建出RecycleView



#### Article实体类，Article的item布局，采用CardView方式展示i文章的item，Article的RecycleView的适配器，MainActicity的调用)

最简单的部分--RecycleView的布局

```
<!--显示文章列表的布局，采用RecycleView-->
    <android.support.v7.widget.RecyclerView
        android:id="@+id/rv_item_article"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
    />
```

然后是子项布局--我尝试使用CardView，说难不难，只是一个布局外面用一个 CardView将这个布局包裹起来，然后设置这个CardView的属性，例如圆角，内容与边缘的距离等等

```
<android.support.v7.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_marginLeft="10dp"
    android:layout_marginRight="10dp"
    android:layout_marginTop="10dp"
    app:cardBackgroundColor="@color/white"
    android:foreground="?attr/selectableItemBackground" //点击后有水波纹
    android:clickable="true"
    android:focusable="true"
    app:contentPadding="10dp"
    app:cardElevation="1dp"//卡片的阴影
    app:cardCornerRadius="2dp"
    app:contentPaddingBottom="6dp">

<!--卡片CardView展示文章的标题，作者，日期等信息-->
    <RelativeLayout
       //这里面放需要展示的控件的属性
    </RelativeLayout>
</android.support.v7.widget.CardView>
```

重头戏---自定义RecycleView的适配器--→ArticleAdapter

```
public class ArticleAdapter extends RecyclerView.Adapter<ArticleAdapter.ItemArticleViewHolder> {

    private List<Article> mArticleList;
    private Context mContext;

    public ArticleAdapter(List<Article> ArticleList) {
        mArticleList = ArticleList;
    }

    public  static  class ItemArticleViewHolder extends RecyclerView.ViewHolder{
         //此处定义要用到的CardView内部的子控件

        //适配器的构造函数内-实例化CardView里面的子控件
        public ItemArticleViewHolder(View itemView){
            super(itemView);
            //此处要实例化CardView的子控件
        }
    }

    //将文章item的布局加载进ViewHolder中
    @Override
    public ItemArticleViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if(mContext == null){
            mContext = parent.getContext();
        }
        View view = LayoutInflater.from(mContext).inflate(R.layout.item_article, parent ,false);
        return new ItemArticleViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ItemArticleViewHolder holder, int position) {
        Article article = mArticleList.get(position);
       //为所有需要赋值的子控件，设置文本等属性(eg:  holder.xxx.setText();)
    }

    @Override
    public int getItemCount() {
        return mArticleList.size();
    }
}
```



MainActivity里的 initViews() 里实例化  RecycleView

```
 public class MainActivity extends AppCompatActivity {
 //...
 private DrawerLayout mDrawerLayout;
    private List<Article> articleList = new ArrayList<>();
    private ArticleAdapter articleAdapter;
    private RecyclerView mRecyclerView;
    private LinearLayoutManager lineLayoutManager;
 
 protected void initViews(){
        //....   
        mRecyclerView = (RecyclerView)findViewById(R.id.rv_item_article);
        lineLayoutManager = new LinearLayoutManager(MainActivity.this);
        lineLayoutManager.setOrientation(OrientationHelper.VERTICAL);
        mRecyclerView.setLayoutManager(lineLayoutManager);
        articleAdapter = new ArticleAdapter(articleList);
        mRecyclerView.setAdapter(articleAdapter);
    }
    //初始化文章列表里面的数据,调用这个函数就可以为ArticleList装填数据了
    protected void initArticlesData(){
        articleList.clear();
        for(int i = 1; i <= 20  ;i++){
            articleList.add(new Article("文章类别"+i,"作者"+i, "热度时间"+i,"文章标题"+i));
        }
    }
```



### Android Studio 的  Gradle Running  突然变得巨慢，helloWorld都要跑四分钟

解决方法：

1、在C:\User\<用户名>\.gradle 目录下新建一个gradle.properties文件，并在里面添加一行：    

​       org.gradle.daemon=true

2、打开AS，在Settings中设置Gradle的工作模式为offline  ，打上勾

设置完成后，瞬间变快了很多很多



#### 弄出一个可以进行GET请求的工具类，返回JSON数据字符串

```
/**
 * @param Path  进行GET请求的接口字符串
 * @param resultdata  用来接收JSON数据的字符串
 * @param handler     与主线程通信的handler
 * @return resultdata 返回接收到JSON数据的字符串
 */
public  static  String  sendGet(final String Path , String resultdata , final Handler handler){

            HttpURLConnection connection;
            try {
                System.out.println("正在进行sendPost");
                URL url = new URL(Path);
                connection = (HttpURLConnection) url.openConnection();
                connection.setRequestMethod("GET");
                connection.connect();
                //接受数据
                if (connection.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    InputStream inputStream = connection.getInputStream();
                    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream, "utf-8"));
                    String line;
                    //不为空进行操作
                    while ((line = bufferedReader.readLine()) != null) {
                        resultdata += line;
                    }
                    Log.e("resultdata",resultdata);
                    if (resultdata == null) System.out.println("接收数据一开始就为空了QAQ");
                    handler.sendEmptyMessage(0x01);  //请求完毕，返回自己自定义的信息 id，与主线程通知，开启下一步操作
                }
            } catch (MalformedURLException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return resultdata;
        }
```



### 放弃自己新开线程展示文章列表，改用AsyncTask类

自定义自己需要的MyAsyncTask至少重写两个方法，也是最关键的那两个！

doInbackground方法和onPostExecute方法

```
public class ArticleListTask extends AsyncTask<String,Integer,List<Article> > {
//第一个参数是doInbackground方法的形参参数，也是调用时传入参数
//第三个参数是onPostExecute方法的形参参数，也是doInbackground返回值的类型
//第二个参数跟onProgressUpdate方法有关，调整进度条，如果重写这个方法，就会紧接着doInbackground方法执行后再执行，从而才到onPostExecute方法。目前还没用上
    
    /**
     * @param params  启动任务执行的输入参数 params[0]
     * @return  后台计算结果,返回給onPostExecute方法
     */
    @Override
    protected List<Article> doInBackground(String... params) {
        articleList.clear();
        for (int i = 0; i < 10; i++) {
            //初始化文章列表里面的数据
            resultdata =  GetUtil.sendGet(params[0] + i + "/json", articleJsondata, handler);
        }
        return articleList;
    }


    /**
     * 为Recycler的Adapter装填数据
     * @param articleList  文章列表数据，doInbackground方法返回的结果
     */
    @Override
    protected void onPostExecute(List<Article> articleList) {
        System.out.println("正在执行onPostExecute方法");
        super.onPostExecute(articleList);
        articleAdapter = new ArticleAdapter(articleList);
        mRecyclerView.setAdapter(articleAdapter);
        articleAdapter.notifyDataSetChanged();//刷新数据
    }

}
```

调用也很简单，说是必须要在主线程内调用，但其实可以不管是不是主线程都可以调用

```
MainActivity.java
//在onCreate方法内
ArticleListTask  mALTask = new ArticleListTask();
mALTask.execute(ArticleListPath);


```



#### 实现ArticleRecycleView的点击事件（CardView也OK啊）

要分别实现两个自定义的接口和短点击，长点击方法

```
@Override
public void onClick(View v) {
    if (onArticleItemClickListener != null) {
        //注意这里使用getTag方法获取数据
        onArticleItemClickListener.onArticleItemClickListener(v, (Integer) v.getTag());
    }
}

@Override
public boolean onLongClick(View v) {
    return onArticleItemLongClickListener != null && onArticleItemLongClickListener.onArticleItemLongClickListener(v, (Integer) v.getTag());
}

/*设置点击事件*/
public void setRecyclerViewOnItemClickListener(ArticleRecyclerViewOnItemClickListener onArticleItemClickListener) {
    this.onArticleItemClickListener = onArticleItemClickListener;
}

/*设置长按事件*/
public void setOnItemLongClickListener(ArticleRecyclerViewOnItemLongClickListener onArticleItemLongClickListener) {
    this.onArticleItemLongClickListener = onArticleItemLongClickListener;
}

/**
 * 接口：子项点击事件接口
 */
public interface ArticleRecyclerViewOnItemClickListener {

    void onArticleItemClickListener(View view, int position);

}


/**
 * 接口：子项长按点击事件
 */
public interface ArticleRecyclerViewOnItemLongClickListener {

    boolean onArticleItemLongClickListener(View view, int position);

}
```

```
//将文章item的布局加载进ViewHolder中
@Override
public ItemArticleViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    //其他代码
    //为item设置点击事件
    view.setOnClickListener(this);
    view.setOnLongClickListener(this);
    return new ItemArticleViewHolder(view);
}
```

```
@Override
public void onBindViewHolder(ItemArticleViewHolder holder, int position) {
    //其他代码
    //设置Tag,方便进行点击事件数据的处理
    holder.mItemArcticleCV.setTag(position);//设置Tag
}
```



### 用WebView显示点击文章后的网页

编写一个WebView的活动和对应的布局

WebContentActivity.java里面创建WebView实例

```
 
private WebView mContentWV;

mContentWV = (WebView) findViewById(R.id.wv_content);
mContentWV.getSettings().setJavaScriptEnabled(true);
mContentWV.setWebViewClient(new WebViewClient());
//url需要通过从文章展示页面的对应点击事件中通过intent传进来
String url = getIntent().getStringExtra("url");
mContentWV.loadUrl(url);
```

#### setwebviewclient()和setwebchromeclient() 主要区别

setWebChromeClient主要处理解析，渲染网页等浏览器做的事情

**WebChromeClient**是辅助WebView处理Javascript的对话框，网站图标，网站title，加载进度等 

**WebViewClient**就是帮助WebView处理各种通知、请求事件的

简单的说，如果除了加载HTML的话，只需要用WebViewClient即可，但是在进行兼容互联网上附加javascript的页面的时候和调用javascript对话框的时候，或者功能较为复杂的内嵌操作的时候，建议使用WebChromeClient 



#### 阅读过的文章标题变灰

在文章的Adapter里面的子项点击事件里面，增添对应子项的标题文本栏的变色

```
public void onBindViewHolder(final ItemArticleViewHolder holder, int position) {
.........
.........
holder.itemView.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        article.setClicked(true);
        holder.mTitleTv.setTextColor(Color.parseColor("#999999"));//灰色
        if (onArticleItemClickListener != null) {
        onArticleItemClickListener.onArticleItemClick(view, (Integer) view.getTag());
        }
    }
 });
}
```

效果：点击文章阅读后，返回可以看到文章标题已经变灰了，但问题是退出后，颜色又变回来了。

猜测是重新进入软件，相当于重新开始绘制RecyclerView，所以要在绘制（绑定数据）时就让它知道已经读过，需要变色。

一开始想到用 将Article的实体类里面再增添一个  isClicked 的Boolean变量，点击就变true，执行onBindView时根据这个变量，可以设置是否变色。。但是失败了

后来想到用个简单的SharePrefences ，存储标题，然后重新进入读取标题就行，做到一半发现，SharePrefences是采用 Key-Value的，无论如何也不能方便的存储读取所有我看过的的文章的标题

所以最后采用 创建一个简单的 SQLite数据库，存储所有我看过的文章的标题

```
public class ArticlebaseHelper extends SQLiteOpenHelper {

    private Context mContext;
    
    //带全部参数的构造函数，此构造函数必不可少
    public ArticlebaseHelper(Context context , String name , SQLiteDatabase.CursorFactory factory,int version) {
        super(context,name,factory,version);
        mContext = context;
    }

    /**
     * 创建数据库
     * @param db
     */
    @Override
    public void onCreate(SQLiteDatabase db) {
        String CREATE_ARTICLE = "create table Article (title text)";
        db.execSQL(CREATE_ARTICLE);
        Toast.makeText(mContext,"Create Succeeded",Toast.LENGTH_SHORT).show();
    }

    /**
     * 数据库升级方法
     * @param db
     * @param oldVersion
     * @param newVersion
     */
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```

重写必须要重写的两个方法后，首先在MainActicity.java里面创建Helper实例，利用它打开数据库，在子项点击事件中，插入当前点击的item的文章标题进数据库，接着在Adapter类里面的onBindView 设置item控件时，创建Helper实例，利用它打开数据库，查询数据库，是否有与当前的item 的标题相同的，有就变色。

MainActivity.java

```
protected void initArticleAdapter(){
    articleAdapter = new ArticleAdapter(articleList);
    //设置ArticleAdapter的每个子项的点击事件--往数据增加已读文章的标题，跳转到对应网页
    articleAdapter.setRecyclerViewOnItemClickListener(new 				    ArticleAdapter.ArticleRecyclerViewOnItemClickListener() {
        @Override
        public void onArticleItemClick(View view, int position) {
            SQLiteDatabase db = dbHelper.getWritableDatabase();
            ContentValues values = new ContentValues(); //创建存放数据的ContentValues对象
            values.put("title",articleList.get(position).getTitle());
            db.insert("Article",null,values); //数据库执行插入命令
            ...
        }
    });

```

ArticleAdapter.java

```
public void onBindViewHolder(final ItemArticleViewHolder holder, int position) {
    final Article article = mArticleList.get(position);
    holder.mTitleTv.setText(article.getTitle());
    SQLiteDatabase db = dbhelper.getReadableDatabase();
    //查询Article表对象，创建游标对象
    Cursor cursor = db.query("Article", new String[] {"title"}, null, null, null, null, null);
    if (cursor.moveToFirst()) {
        do {
            String title = cursor.getString(cursor.getColumnIndex("title"));
            if (article.getTitle().equals(title)) {
                Log.e("Articletitle",article.getTitle());
                holder.mTitleTv.setTextColor(Color.parseColor("#999999"));
                break;
            }
        } while (cursor.moveToNext());
    }
    cursor.close();
	.....
	.....
}
```

修改之后，发现退出APP，重进APP，点击变灰保留下来了，但是又有新的问题。

##### 问题：有很多“随机”的的文章也变成了灰色，

疑惑下，我将设置变色时的代码前加了打印当前的即将变色的标题的 Log，发现Log打印的标题都是我点击过的，但是还是有些标题没被Log打印，仍然变了色。

百思不得其解下，无意中看到博客有固定距离的item重复触发事件的文章，原来是RecyclerView的缓存问题

在MainActicity里面将recyclerView 的缓存设置大了，暂时解决问题~

#### 解决：

```
mRecyclerView.setItemViewCacheSize(500);//设置RecyclerView的缓存数量
```