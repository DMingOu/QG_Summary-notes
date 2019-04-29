# QG训练营移动组第六周周记：2019年4月22日

## 生活随记



## 一周总结

#### error：com.android.support:recyclerview-v7:24.4.0 报错

在 build.gradle 

由于

```
 compileSdkVersion 24
```

 所以将 com.android.support:recyclerview-v7:28.0.0 改成v7：24.0.0即可



#### Error:(11) No resource identifier found for attribute 'roundIcon' in package

```
Error:(11) No resource identifier found for attribute 'roundIcon' in package

删除AndroidManifest.xml里的 android:roundIcon="@mipmap/ic_launcher_round"
//不使用这个圆图标就行了

```



#### 如何不让EditText自动获取焦点问题

```
让EditText之前的控件先获得焦点,即在EditText前面的一个控件上设置属性:
android:focusable="true" 
android:focusableInTouchMode="true"
//但如果EditText前面是按钮控件，会导致按钮要连续点两个才有用！

//在EditText前面放置一个看不到的LinearLayout，让它率先获取焦点，效果拔群！
     <LinearLayout
        android:layout_width="0px"
        android:layout_height="0px"
        android:focusable="true"
        android:focusableInTouchMode="true" />
```



#### Error：android.content.res.Resources$NotFoundException: String resource ID #0x0 找不到资源文件ID #0x0

遇到这种情况，很有可能是把一个int型业务数据的 设置setText()或者类似的方法中， 这样Android系统就会主动去资源文件当中寻找， 但是它不是一个资源文件ID， 所以就会报出这个bug。 将int型业务数据，转换成String类型即可。

找出错误使用的.setText()的方法，将内容改正过来，改成String类型的内容！



#### 监控输入框的文本变化，并作出响应，需要为输入框申请一个 TextWatcher

三个方法，最重要的就是第二个的onTextChanged(),实时作出响应

```
//监控输入框的文本变化
met_search.addTextChangedListener(new TextWatcher() {
    @Override
    public void beforeTextChanged(CharSequence s, int start, int count, int after) {

    }

    @Override
    public void onTextChanged(CharSequence s, int start, int before, int count) {
        //当文本有输入或者更改时，设置翻译内容隐藏起来
        mtv_resultDst.setVisibility(View.GONE);
        mtv_resultSrc.setVisibility(View.GONE);
    }

    @Override
    public void afterTextChanged(Editable s) {

    }
});
```



#### app更换logo，手机图标没变化

解决：

1.卸载，重新安装
2.清除手机缓存，重启手机。
3.更换小米主题。由A主题换成B主题，再换回A主题



#### 将APP顶部的自带标题栏去掉

```
正确的做法是在风格主题文件styles.xml中修改继承关系

在style.xml 找到 parent =“Theme.AppCompat.Light.DarkActionBar”这一行

将parent=“”里替换为Theme.AppCompat.Light.NoActionBar

Android Studio 2.2，Android P亲测可行


```



#### Notification 通知

```
 private NotificationManager ntf_manger;//创建通知管理的实例
 
 ntf_manger = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
 sendNotification();//发送通知
 
  protected  void sendNotification(){
        Notification notification = new      NotificationCompat.Builder(getApplicationContext())
                .setContentText("测试通知内容")
                .setContentTitle("测试通知标题")
                .setSmallIcon(R.mipmap.translate)           //通知小图标，只能用纯alpha图层设置，系统状态栏
                
  //设置通知大图标               		        .setLargeIcon(BitmapFactory.decodeResource(getResources(),R.mipmap.translate))     
                .setWhen(System.currentTimeMillis())//指定创建时间，毫秒为单位
                .setAutoCancel(false)
                .setOngoing(true)   //用户无法删除
                .build();
        //发送基本通知
        ntf_manger.notify(1,notification);
    }
```



```
protected  void sendNotification(){
        //创造 PendingIntent,响应点击通知事件
        Intent notifyIntent = new Intent(this,Search_Quick_Activity.class);
        notifyIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK
                | Intent.FLAG_ACTIVITY_CLEAR_TASK);
        int requestCode = (int ) SystemClock.uptimeMillis();
        PendingIntent pendingIntent = PendingIntent.getActivity(this,requestCode,
                notifyIntent,PendingIntent.FLAG_UPDATE_CURRENT);

        Notification notification = new NotificationCompat.Builder(getApplicationContext())
                .setContentText("测试通知内容")
                .setContentTitle("测试通知标题")
                .setSmallIcon(R.mipmap.translate)           //通知小图标，只能用纯alpha图层设置，系统状态栏
                .setLargeIcon(BitmapFactory.decodeResource(getResources(),R.mipmap.translate))      //设置通知大图标
                .setWhen(System.currentTimeMillis())//指定创建时间，毫秒为单位
                .setAutoCancel(false)
                .setOngoing(true)   //用户无法删除
                .setContentIntent(pendingIntent)
                .build();

        //发送基本通知
        ntf_manger.notify(1,notification);

    }
```

在代码中，我们设置NotifySpecialActivity为我们要跳转的界面，然后在xml的配置文件中，我们重点设置了这三个属性：android:excludeFromRecents=”true”，android:launchMode=”singleTask”，android:taskAffinity=”“，第一个属性的设置，是将该界面从最近任务栏当中移除，防止用户通过最近任务栏而进入到该界面，这样一来，只能通过通知来的点击来进入。第二种属性的设置就很常见了，是为了防止该界面存在的情况下，重复创建该Activity，第三属性是为了配置代码中的这段来设置的：

```java
notifyIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK  
        | Intent.FLAG_ACTIVITY_CLEAR_TASK);  
```

这样的作用是为此次跳转界面的行为重新分配一个任务堆栈，而不从属于其它的任务堆栈，这样的话，当我们点击返回键后，就可以直接返回到刚刚用户所处的任务界面了。这里不再使用TaskStackBuilder，所以最后需要调用PendingIntent.getActivity(this,requestCode,notifyIntent,PendingIntent.FLAG_UPDATE_CURRENT)这个方法来构造一个PendingIntent，然后赋值给notifyBuilder。



#### 为一个活动设置透明主题

```
values文件夹中的color文件中，设置透明背景
 <style name="NoTitleTranslucentTheme" parent="Theme.AppCompat.Light.NoActionBar" >

        <item name="android:windowFrame">@null</item><!--边框-->
        <item name="android:windowIsTranslucent">true</item><!--半透明-->
        <item name="android:windowNoTitle">true</item><!--无标题-->
        <item name="android:windowBackground">@android:color/transparent</item><!--背景透明-->
        <item name="android:windowFullscreen">true</item>
        <item name="android:backgroundDimEnabled">true</item><!--模糊-->
    </style>

注册文件中，特定的活动中引用这个主题，来起到透明效果
<activity
            android:name=".Search_Quick_Activity"
            android:theme="@style/NoTitleTranslucentTheme"
            android:excludeFromRecents="true"
            android:label="Search_Quick_Activity"
            android:launchMode="singleTask"
            android:taskAffinity="">
 </activity>
 
 ----------------------------------------------------------------------------------------
 XML文件中
 单独对子布局如下单独设置透明度，貌似无效
 <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@android:color/transparent"
        android:alpha="0.0" >
    </LinearLayout>
    
 在已经设置整个活动的透明度后，
 在EditView标签里面需要指定好背景或者颜色，不然会看不清在哪里
    android:background="@null"改为了 android:background="#ccffffff"
    
```



# Android打开某个activity时自动弹出输入法键盘

在manifest清单文件对应的activity配置中加入一句

android:windowSoftInputMode="stateVisible|adjustResize"就可以了：

```
<activity
            android:name=".Search_Quick_Activity"
            android:theme="@style/NoTitleTranslucentTheme"
            android:excludeFromRecents="true"
            android:label="Search_Quick_Activity"
            android:launchMode="singleTask"
            android:windowSoftInputMode="stateVisible|adjustResize"  //改这句
            android:taskAffinity="">
        </activity>
```



#### 软键盘点击按钮后，自动收起来

```
//在按钮点击事件里面加入代码↓
InputMethodManager imm = (InputMethodManager)getSystemService(Context.INPUT_METHOD_SERVICE);
imm.hideSoftInputFromWindow(view.getWindowToken(), 0);
```



#### Android Studio 将项目导出为APK

[导出APK]: https://blog.csdn.net/hefeng6500/article/details/79869647

多图流，引用链接比较方便。。



#### 出现org.json.JSONArray cannot be converted to JSONObject异常

在更换翻译API的时候，出现了这个异常，说明将JSON数据中，本应该是JSONArray的东西当作了JSONObject处理，自然会报错。

解决方法：再解析一层JSONArray，再通过标签获取所要内容。

```
下面以good为示例
//有道翻译免费api，translateResult这个数组[]里面还有一个数组[]
{
    "type": "EN2ZH_CN",
    "errorCode": 0,
    "elapsedTime": 1,
    "translateResult": [
        [
            {
                "src": "good",
                "tgt": "好"
            }
        ]
    ]
}

//百度翻译免费API，data数组里面[],包裹着{}，说明里面没有第二层数组
{
    "from": "en",
    "to": "zh",
    "domain": "all",
    "type": 2,
    "status": 0,
    "data": [
        {
            "dst": "好的",
            "prefixWrap": 0,
            "src": "good",
            "relation": [],
            "result": [
                [
                    0,
                    "好的",
                    [
                        "0|4"
                    ],
                    [],
                    [
                        "0|4"
                    ],
                    [
                        "0|6"
                    ]
                ]
            ]
        }
    ]
}
```



## 存在问题



## 下周规划



