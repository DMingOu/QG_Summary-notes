# QG训练营移动组第五周周记：2019年4月15日

## 生活随记



## 一周总结

#### Android开源分包方式学习

合理地分包，又可以使整个项目模块化，减少包与包之间的依赖，让整个项目的框架更加清晰，更利于后续功能的拓展。

开源中国的分包方式简单明了，它似乎将大量的业务逻辑放在了UI层，不过项目的业务逻辑不多的情况下，这倒也没有什么。Adatper层是UI层的数据源。Bean层中提供了各种实体类。oschina中没有数据库访问相关的包。

- com.weibo.net — 新浪微博SDK源码包
- greendroid.widget — 快捷菜单栏组件(国外UI库[GreenDroid](http://www.oschina.net/p/greendroid))
- net.oschina.app — APP启动及管理包
- net.oschina.app.adapter — APP列表适配器包
- net.oschina.app.api — API访问包
- net.oschina.app.bean — APP实体包
- net.oschina.app.common — APP工具包
- net.oschina.app.ui — APP界面包
- net.oschina.app.widget — APP控件包

##### 按照功能模块来分包：

```
java
           |--- com
                |---example
                    |--- base
                    |    |--- BaseActivity.java
                    |    |--- BaseFragment.java
                    |    |--- xxx.java
                    |
                    |--- network
                    |    |--- HttpClient.java
                    |    |--- xxx.java
                    |
                    |--- image
                    |    |--- ImageManager.java
                    |    |--- xxx.java
                    |
                    |--- db
                    |    |--- DbManager.java
                    |    |--- xxx.java
                    |
                    |--- news
                    |    |--- NewsActivity.java
                    |    |--- NewsFragment.java
                    |    |--- xxx.java
                    |
                    |--- movie
                    |    |--- MovieActivity.java
                    |    |--- MovieFragment.java
                    |    |--- xxx.java
                    |
                    |--- music
                    |    |--- MusicActivity.java
                    |    |--- MusicFragment.java
                    |    |--- xxx.java
                    |
                    |--- entity
                    |    |--- Movie.java
                    |    |--- News.java
                    |    |--- xxx.java
                    |
                    |--- adapter
                    |    |--- AbsAdapter.java
                    |    |--- MovieAdapter.java
                    |
                    |--- widget
                    |    |--- CircleImageView.java
                    |    |--- xxx.java
                    |    
                    |--- util
                         |--- ToastUtil.java
                         |--- xxx.java
```

分包的事情可大可小，分的好，后面的开发也会更加轻松。不然找东西找到头都大了！



#### Android SDK Manger 打不开闪退+没有资源出来

问题：java配置变量出错了！！

问题原因：主要是国内连接到google的服务器太慢了，或者直接连不到。于是那些包也刷不出来。
解决办法：设置代理服务网站，是一个镜像网站，下载刷新很快
操作步骤：
1）打开SDK Manager
2）打开设置界面，Tools>Options

3）如下图设置参数
HTTP Proxy Server：mirrors.neusoft.edu.cn
HTTP Proxy Port：80

结果：暂时无效

解决：重新下载安装了sdk文件夹，可以打开了

# 小米手机不能运行Android Studio程序的问题

**方法一 ：设置Android Studio 中Instant Run中的选项为不选中**

(1)根据以下路径，找到Instant Run中的选项 
File —— Settings——Build,Execution,Deployment——Instant Run 
(2)将Enable Instant Run to hot swap code/resource changes on deploy(default enabled)的选择框取消。



1. 1. 
      PUBLIC
   2. [Stack Overflow](https://stackoverflow.com/questions)
   3. [Tags](https://stackoverflow.com/tags)
   4. [Users](https://stackoverflow.com/users)
   5. [Jobs](https://stackoverflow.com/jobs?med=site-ui&ref=jobs-tab)

2. 1. Teams

      Q&A for work

      Learn More

      

#### java.lang.ClassCastException: android.widget.SearchView cannot be cast to 。。。

Android Studio 真机调试时，app闪退，看报告日志 error类中有这个报错。。

查找资料后，发现是SearchView这个控件对应的包有两个选择，一个是属于V7，一个不属于。

在import...那里将引用改成另一个就没有闪退了



##### WebViewClient的方法的详细说明：

```
public boolean shouldOverrideUrlLoading(WebView view,String url){
   return true;
}
//在点击请求的是连接是才会调用，重写此方法返回true表明点击网页里面的链接还是在当前的webView里跳转，不跳到浏览器里边

2.onReceivedSslError()
public void onReceivedSslError(WebView view,SslErrorHandler handler,android.net.http.SslError error){
     handler.proceed();
}
//重写此方法可以让webView处理https请求

3. shouldOverrideKeyEvent()
   public boolean shouldOverrideKeyEvent(WebView view,KeyEvent event){
   return super.shouldOverrideKeyEvent(view,event);
   }
   //重写此方法才能够处理在浏览器中的按键事件
4. onLoadResource();
   public void onLoadResource(WebView view, String url){
    if(DEBUG){
          Log.d(TAG , "onLoadResource");
     }
     super.onLoadResource(view, url);
   }
    // 在加载页面资源是会调用，每一个资源（比如图片）的加载都会调用一次
5. onPageStarted()';

public void onPageStarted(WebView view, String url, Bitmap favicon){
   if(DEBUG){
         Log.d(TAG,"onPageStarted");
    }
     if(url.endsWith(".apk")){
           download(url);  //下载处理
      }
      super.onPageStarted(view, url, favicon);
}
  //在页面开始加载时调用

6. onPageFinished()

public void onPageFinished(WebView view,String url){
     if(DEBUG){
          Log.i(TAG,"onPageFinished");
      }
      super.onPageFinished(view, url);
}
在页面加载结束的时候调用
```

程序需要联网并读取网络连接情况所以就要添加两个权限：

<uses-permissionandroid:name="android.permission.INTERNET"/>

<uses-permissionandroid:name="android.permission.ACCESS_NETWORK_STATE"/>

一定要注意转换编码格式，不然会出现乱码。

#### 解决Android3.0之后不能在主线程中进行HTTP请求

在Android3.0以后，会发现，只要是写在主线程（就是Activity）中的HTTP请求，运行时都会报错，这是因为Android在3.0以后为了防止应用的ANR（aplication Not Response）异常。

针对此问题有两种解决的方法：

1.可以再Activity的onCreate()方法中加入这样一段代码，如下：

```java
 if (Build.VERSION.SDK_INT >= 11) {      
      StrictMode.setThreadPolicy(new     StrictMode.ThreadPolicy.Builder().detectDiskReads().detectDiskWrites().detectNetwork().penaltyLog().build());    
     StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder().detectLeakedSqlLiteObjects().detectLeakedClosableObjects().penaltyLog().penaltyDeath().build());  
  }
```



#### 为什么在主线程更新UI，在子线程执行耗时操作？
在Android程序创建之初，一个Process呈现的是单线程模型，所有的任务都在一个线程中运行。因此，我们认为，UI Thread所执行的每一个函数，所花费的时间都应该是越短越好。

如果所有的工作都在UI线程，一些比较耗时的工作比如（访问网络，下载数据，查询数据库等），很容易造成主线程的阻塞，导致事件停止分发（包括绘制事件）。轻则降低用户体验，更坏的情况是，如果主线程被阻塞超过5秒，就会导致ANR，弹出应用程序没有响应，是等待还是关闭的警告。

另外，Andoid UI toolkit并不是线程安全的，所以不能从非UI线程来操纵UI组件。必须把所有的UI操作放在UI线程里。

##### ANR

有些应用在使用中会弹出一个对话框，一般这个对话框叫做应用程序无响应对话框（ANR：Application Not Responding）。虽然这个提示框有等待和关闭应用程序两种选择，但是它的弹出就已经影响了用户使用app过程中的体验，所以一般来说，[Android](https://www.2cto.com/kf/yidong/Android/)开发的过程中都会严格控制ANR的出现。

ANR表面原因：应用在一定时间内（一般是5秒）没有响应输入操作（例如输入，按钮，手势触摸等）。

ANR根本原因：Android主线程中进行耗时操作，造成主线程阻塞；BroadcastReceiver生命周期结束前没有完成相应的耗时任务。

Android应用都是运行在主线程中的，包括activity、service的生命周期，ui的刷新（所以主线程又叫ui线程），输入响应操作等等。也就是说任何在主线程中运行的函数都不能进行耗时的动作，否则会阻塞主线程，导致输入无法响应，从而弹出ANR。

BroadcastReceiver的生命周期很短（一般为10秒），如果在它生命周期结束前耗时任务没有完成，Android认为该程序没有响应。

这些耗时的操主要包括**网络，数据库，大量的计算操作**（如bitmap的处理）等。

为了防止主线程被阻塞，通常需要启动子线程来处理耗时任务，子线程的任务完成时通过handler通知主线程，让主线程刷新ui等。下面是一个简单的事例：

```
public class MainActivity extends Activity {
 
    private TextView text;
    private Button button;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        text = (TextView) findViewById(R.id.text);
        button = (Button) findViewById(R.id.button);
 
        button.setOnClickListener(new OnClickListener() {
 
            @Override
            public void onClick(View arg0) {
                System.out.println("-->开启子线程");
                new TaskThread().start();
            }
        });
 
    }
 
    Handler handler = new Handler() {
        public void handleMessage(Message msg) {
            switch (msg.what) {
            case 0:
            {
                System.out.println("-->回到主线程刷新ui任务");
                text.setText("任务完成");
            }
                break;
 
            default:
                break;
            }
        };
    };
 
    class TaskThread extends Thread {
        public void run() {
            System.out.println("-->做一些耗时的任务");
            try {
                sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            handler.sendEmptyMessage(0);
        };
    };
}
```



```
//此接口：http://web.juhe.cn:8080/environment/air/pm?city=城市名称&key=您申请的APPKEY值
@Override  
protected String doInBackground(String... params) {  
    String city = params[0];  
  
    ArrayList<NameValuePair> headerList = new ArrayList<NameValuePair>();  
        headerList.add(new BasicNameValuePair("Content-Type", "text/html; charset=utf-8"));  
  
    String targetUrl = JUHE_URL_ENVIRONMENT_AIR_PM;  
  //这里应该是将接口中的变量后的=补上实例，然后让他去查询
    ArrayList<NameValuePair> paramList = new ArrayList<NameValuePair>();  
    paramList.add(new BasicNameValuePair("key", JUHE_APPKEY));  
    paramList.add(new BasicNameValuePair("dtype", "json"));  
    paramList.add(new BasicNameValuePair("city", city));  
```

opt 替代 get
在上面使用中，我们通过getXXX()获取相应值。但是，会发现其局限性很多，很容易就抛异常，需要我们try...catch去捕获。而optXXX()对此进行了优化。

首先看一下他的用法，在看用法之前，我们回忆之前使用get时的两个问题

其他类型转字符串类型抛出异常
当需要的字段没有时，抛出异常。
看一下opt针对如上问题的解决：

```
JSONObject obj = new JSONObject(json);//最外层的JSONObject对象


        JSONObject user = obj.optJSONObject("user");

        String name = user.optString("name");

        //整形转字符串
        String age = user.optString("age");

        boolean isMan = user.optBoolean("isMan");

        //默认值，如果没有该字段，则会返回默认值
        String sex = user.optString("sex","男");

        System.out.println("name:"+name+"\nage:"+age+"\nisMan:"+isMan+"\nsex:"+sex);
 //结果       
    name:alex
    age:18
    isMan:true
    sex:男
```



- get()取值不正确会抛出异常，必须用try catch或者throw包起
- opt()取值不正确则会试图进行转化或者输出友好值，不会抛出异常

创建json对象：JSONObject jsonobject=new JSONObjcet(传入字符集)

获取json中的内容：

1、获取json中的json：

        JSONObject jo=josnobject.getJSONObject(传入该json的key值)

2、获取josn中的value：

        String result=jsonobject.getString(传入对应的key值)

3、获取josn中的数组：

        JsonArray list=jsonobject.getJSONArray(传入对应的key值)

师兄给的有道翻译的一个api

http://fanyi.youdao.com/translate?&doctype=json&type=AUTO&i=程序员

将这个URL放进***Postman程序***里，可以见到他返回的json数据中有那些需要我们请求的数据

```
{
    "type": "ZH_CN2EN",
    "errorCode": 0,
    "elapsedTime": 1,
    "translateResult": [
        [
            {
                "src": "程序员",
                "tgt": "The programmer"
            }
        ]
    ]
}
```

##### 豌豆荚导致手机adb无法打开，手机无法弹出调试弹窗！(搞了半个早上，—_—,本来还想用豌豆荚更新手机应用）

因为我的小米手机，连接手机，打开开发者选项后，连接电脑后也无法弹出允许电脑连接的弹窗，所以想要打算下载个豌豆荚，安装更新驱动或者adb，结果发现还是打不开，

一开始在命令行里 直接执行命令   adb  ，报错，说明没有配置好变量，遂返回到环境变量的 Path里编辑，将我的adb.exe 所属路径，复制了进去，确定！

命令行执行  adb  ，出来了一大堆内容，说明配置完成了。

用cmd里面执行 adb killer，成功杀掉了，但是准备启动时，bug出现了

cmd 执行   adb start-server，就报错，端口被占用了，开始--运行--CMD 到命令提示符，

输入 netstat -aon|findstr "5037"

发现电脑的5037 端口被一个 PPadbServicer的进程占用了，这不就是我的豌豆荚的进程？

果断卸载豌豆荚，然后再来一次  adb start-server，成功了，连接手机，手机也弹出了是否允许电脑调试的弹窗！

然后用电脑豌豆荚更新手机应用也泡汤了。。再想下其他办法吧。。

**error：Could not find method google() for arguments [] on repository container.**

gradle-wrapper.properties配置不一样

**distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip**

替代为

> **distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip**

失败了

```
//https://fanyi.baidu.com/transapi?from=auto&to=zh&query=god ，用百度翻译这个例子
将完整的api接口放到Postman上面后，可以看到json数据，如果要更加直观，需要将这段json数据放到JsonView上
{
    "from": "en",
    "to": "zh",
    "domain": "all",
    "type": 2,
    "status": 0,
    "data": [
        {
            "dst": "上帝",
            "prefixWrap": 0,
            "src": "god",
            "relation": [],
            "result": [
                [
                    0,
                    "上帝",
                    [
                        "0|3"
                    ],
                    [],
                    [
                        "0|3"
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



#### HttpClient貌似已经在API 23后被抛弃了，要用OKHttp3 或者 HttpConection



##### java.lang.NullPointerException: Attempt to invoke virtual method 'int java.lang.lenth()........

这个异常很常见，经查询，是空指针导致的，换句话说就是空对象，找不到对应对象，于是乎找到了一个没有new出来的实例，解决了这个异常！

#### Aysnc类解决不能在Android UI线程里面进行网络访问的问题。。

`AsyncTask`的使用步骤有4个：

1. 创建 `AsyncTask` 子类 & 根据需求实现核心方法
2. 创建 `AsyncTask`子类的实例对象（即 任务实例）
3. 手动调用`execute（）`从而执行异步线程任务

```
/**
  * 步骤1：创建AsyncTask子类
  * 注： 
  *   a. 继承AsyncTask类
  *   b. 为3个泛型参数指定类型；若不使用，可用java.lang.Void类型代替
  *   c. 根据需求，在AsyncTask子类内实现核心方法
  */

  private class MyTask extends AsyncTask<Params, Progress, Result> {

        ....

      // 方法1：onPreExecute（）
      // 作用：执行 线程任务前的操作
      // 注：根据需求复写
      @Override
      protected void onPreExecute() {
           ...
        }

      // 方法2：doInBackground（）
      // 作用：接收输入参数、执行任务中的耗时操作、返回 线程任务执行的结果
      // 注：必须复写，从而自定义线程任务
      @Override
      protected String doInBackground(String... params) {

            ...// 自定义的线程任务

            // 可调用publishProgress（）显示进度, 之后将执行onProgressUpdate（）
             publishProgress(count);

         }

      // 方法3：onProgressUpdate（）
      // 作用：在主线程 显示线程任务执行的进度
      // 注：根据需求复写
      @Override
      protected void onProgressUpdate(Integer... progresses) {
            ...

        }

      // 方法4：onPostExecute（）
      // 作用：接收线程任务执行结果、将执行结果显示到UI组件
      // 注：必须复写，从而自定义UI操作
      @Override
      protected void onPostExecute(String result) {

         ...// UI操作

        }

      // 方法5：onCancelled()
      // 作用：将异步任务设置为：取消状态
      @Override
        protected void onCancelled() {
        ...
        }
  }

/**
  * 步骤2：创建AsyncTask子类的实例对象（即 任务实例）
  * 注：AsyncTask子类的实例必须在UI线程中创建
  */
  MyTask mTask = new MyTask();

/**
  * 步骤3：手动调用execute(Params... params) 从而执行异步线程任务
  * 注：
  *    a. 必须在UI线程中调用
  *    b. 同一个AsyncTask实例对象只能执行1次，若执行第2次将会抛出异常
  *    c. 执行任务中，系统会自动调用AsyncTask的一系列方法：onPreExecute() 、doInBackground()、onProgressUpdate() 、onPostExecute() 
  *    d. 不能手动调用上述方法
  */
  mTask.execute()；

```

存在的问题：

在doInBackground方法中，如果开启了线程，方法可能会在线程还没执行完毕的时候，就返回了一个值，就会导致doInBackground的线程内容还没好，就开始执行onPostExecute方法，导致出现数据为null的结果，解决方法：加入线程睡眠，和进度条，进度条结束的时候，onPostExecute()重新获取数据，就可以继续执行方法了。



#### 通过git把文件夹上传到github的一个方法

```
//把github上面的仓库克隆到本地
git clone [github仓库]

//定位到打算上传的文件夹
git cd 文件路径

//把项目文件夹下面的文件都添加进来
 git add .
 
 //提交说明，准备提交暂存区中的更改的已跟踪文件
git commit -m "你的信息"

//把本地仓库push到github上面
git push -u origin master

然后就完成了将本地将一个文件夹放到特定仓库了，毕竟直接拖拽upload file 有文件数限制！

```



#### 前缀表达式计算器

```
typedef struct BiTNode {
    TElemType      data;     // 数据域
    struct BiTNode  *lchild,*rchild;  // 左、右孩子指针
} BiTNode, *BiTree;   // 二叉链表

//前缀表达式建树过程 - 由于操作数在叶节点位置和前缀表达式的特点，其建树过程与后缀表达式一致，只不过扫描方向正好相反 
BiTree PreTree(char s[], int len)
{
	printf("字符串长度为%d\n", len);
	//计数变量 
	int i;
	BiTree p, root;
	//临时栈
	typedef struct stack
	{
		BiTree vec[1000];  //存放指向树节点的指针
		int top;
	}Stack;

	Stack q;
	q.top = 0;

	//遍历字符串，若为操作数,则生成根节点并将指向该根节点的指针入栈,若为运算符,则生成节点并在临时栈中弹出两个指向操作数
	//节点的指针，并指向该运算符节点并将其入栈
	for (i = len - 1; i >= 0; i--)
	{    
		//为空格
		if (s[i] == ' ') {
			continue;
		}
		//为操作符 
		if (s[i] == '+' || s[i] == '/' || s[i] == '*' || s[i] == '-')
		{
			p = (BiTree)malloc(sizeof(BiTNode));
			p->data = s[i];
			p->lchild = q.vec[q.top--];    //先弹出的为左节点 
			p->rchild = q.vec[q.top--];     //后弹出的为右节点 
			q.vec[++q.top] = p;           //将根节点入栈 
		}
		else
		{
			//s[i]为操作数 
			p = (BiTree)malloc(sizeof(BiTNode));
			p->data = s[i];
			p->lchild = NULL;
			p->rchild = NULL;
			q.vec[++q.top] = p;      //将指向操作数节点的指针入栈 
		}
	}

	root = q.vec[q.top--];        //构造出的二叉树的根节点最后被保留在了栈中 
	return root;
}

//根据前缀表达式构造出的树求值
int Value(BiTree T) {
	{
		if (T == NULL) {
			printf("空树");
			return 0;
		}
		if (T->data <= '9'&&T->data >= '0')
		{
			return (T->data - '0');
		}
		else
		{    //到了这一步的T->data必为运算符，则必有左右孩子节点且不空
			switch (T->data)           
			{
			case'+':  return Value(T->lchild) + Value(T->rchild); break;
			case'-':  return Value(T->lchild) - Value(T->rchild); break;
			case'*':  return Value(T->lchild) * Value(T->rchild); break;
			case'/':  return Value(T->lchild) / Value(T->rchild); break;
			}
		}
	}
}

int main (){
	char PreStr[1000];     //前缀表达式
 	int len = 0;
	int result = 0;
	char ch;
	    printf("请输入一个简单算术表达式(一位正整数且只有+ - * / 不含有括号):\n");
	while ((ch = getchar()) != '\n') {
	if (ch != ' ') {
		PreStr[len++] = ch;
	}
}
	BiTree PTree = PreTree(PreStr, len);			
	printf("构造前缀表达树成功！\n");
	result = Value(PTree);
	printf("前缀表达式算出来的值是%d\n", result);
	
	return 0;
}
```



## 存在问题



## 下周规划



