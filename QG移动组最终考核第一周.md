# QG移动组最终考核第一周

### Gsonfomt插件

设置-->插件plugins的Browse repositories搜索 GsonFormat  然后安装,重启AS后，就已经安装好了

#### 使用GsonFormat

在一个实体Bean类里，右键打开Generate，选择GsonFormat后，弹出一个代码框，需要我们往里面放很丑很难看的JSON数据，把数据放进去后，点击OK，这个bean类就被创建好了



### AS中快速提交到GitHub保存

1. 打开Android Studio配置界面，找到File——>settings——>Version Control——>Git，输入正确的Git安装目录
2. 再找到Version Control节点下的GitHub，然后输入你自己的GitHub账号和密码，点击Test看一下是否能够成功连接。然后Apply——>OK
3. 从GitHub上检出项目，VCS——＞Checkout from Version Control——>GitHub
4. 更新代码到GitHub，右键项目——>Git——>Add——>Commit。或者是VCS——>Git——>Add——>Commit
5. 提交代码，在Commit的时候提交说明是必须填写的。然后点击Commit and push，代码就上传到GitHub上了；也可以只Commit，然后在Push。

### 登陆，注册

首先，对于登陆注册要有一个明确的逻辑顺序，不管是对于SharedPreferences还是SQLite，流程都是：注册---->检测是否重名---->注册成功---->登录---->验证用户名与密码---->登录成功。清楚了这个流程之后，就可以往下写了。这里为什么要提到SharedPreferences和SQLite两种方式呢？或许会有人问道，SharedPreferences只能存储一些简单的数据保存在xml文件中，而且它的数据存储是覆盖式的（即存储一条数据会立即擦除覆盖上一条记录），可操作性比起SQLite要差很多，为什么还要用这个呢？因为从实用角度来说，如果一台设备或者仪器只需要给一个默认的登录用户，那么这个时候完全不需要写一个SQLite来进行存储，只要给一个默认的键值对来进行一次存储就可以了。但是如果需要实现多人注册功能，那么就需要SQLite来进行一些数据库的操作了。

#### Bean类为什么要implements Serializable

​	一个对象序列化的接口，一个类只有实现了Serializable接口，它的对象才是可序列化的。因此如果要序列化某些类的对象，这些类就必须实现Serializable接口。而实际上，Serializable是一个空接口，没有什么具体内容，它的目的只是简单的标识一个类的对象可以被序列化。

​	需要序列化的情况：

​        1.     当你想把的内存中的对象写入到硬盘的时候。

​        2.     当你想用套接字在网络上传送对象的时候。

​        3.     当你想通过RMI传输对象的时候。（暂时不理解RMI）

 再稍微解释一下:

​        1.    比如说你的内存不够用了，那计算机就要将内存里面的一部分对象暂时的保存到硬盘中，等到要用的时候再读入到内存中，硬盘的那部分存储空间就是所谓的虚拟内存。在比如过你要将某个特定的对象保存到文件中，我隔几天在把它拿出来用，那么这时候就要实现Serializable接口。

​        2.    在进行[Java](http://lib.csdn.net/base/javase)的Socket编程的时候，你有时候可能要传输某一类的对象，那么也就要实现Serializable接口。最常见的你传输一个字符串，它是JDK里面的类，也实现了Serializable接口，这样做为的是将数据变为二进制来传输，所以可以在网络上传输。

        3.    如果要通过远程的方法调用（RMI）去调用一个远程对象的方法，如在计算机A中调用另一台计算机B的对象的方法，那么你需要通过JNDI服务获取计算机B目标对象的引用，将对象从B传送到A，就需要实现序列化接口。



#### 按钮风格，抛弃原生

```
在布局文件中，按钮的属性里面
android:background="@drawable/shape_user_button_login"

在对应drawable文件夹中，创建一个对应的xml文件，里面存有改变控件样式的shape

<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >

    <solid android:color="#FF72CAE1" />
    <!--
  设置圆角
  注意：	bottomRightRadius是左下角而不是右下角  bottomLeftRadius右下角
    -->
    <corners
        android:bottomLeftRadius="10dp"
        android:bottomRightRadius="10dp"
        android:topLeftRadius="10dp"
        android:topRightRadius="10dp" />

</shape>
```

### Android TextView部分文字实现点击事件

**SpannableStringBuilder.class**

This is the class for text whose content and markup can both be changed. 
（这是一个内容和标记都可以更改的文本类）

SpannableStringBuilder和SpannableString主要通过使用setSpan(Object what, int start, int end, int flags)改变文本样式。

```
setSpan()方法对应的参数如下：

start： 指定Span的开始位置
end： 指定Span的结束位置，并不包括这个位置。
flags：取值有如下四个 
Spannable. SPAN_INCLUSIVE_EXCLUSIVE：前面包括，后面不包括，即在文本前插入新的文本会应用该样式，而在文本后插入新文本不会应用该样式 
Spannable. SPAN_INCLUSIVE_INCLUSIVE：前面包括，后面包括，即在文本前插入新的文本会应用该样式，而在文本后插入新文本也会应用该样式 
Spannable. SPAN_EXCLUSIVE_EXCLUSIVE：前面不包括，后面不包括 
Spannable. SPAN_EXCLUSIVE_INCLUSIVE：前面不包括，后面包括
```

SpannableStringBuilder，这个类实际上就是对你的TextView中的文字进行简单的配置，配置好你想要的属性后，直接调用下面代码即可：

```
 //设置光标如何移动计量的方法
 textView.setMovementMethod(LinkMovementMethod.getInstance());
 //配置给TextView
 textView.setText(style)
以字体设置颜色为例：

 //设置部分文字颜色
 //创建字体颜色的Span，并初始化字体颜色属性
 ForegroundColorSpan foregroundColorSpan = new    ForegroundColorSpan(Color.parseColor("#0000FF"));
 //我们设置第11~15个中间的字符为蓝色
 style.setSpan(foregroundColorSpan, 11, 15, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
```

[SpannableStringBuilder详解]: https://www.jianshu.com/p/f004300c6920

```
SpannableString spannableString = new SpannableString("整段文字");//创建实例
...
textView.setText(spannableString);//将Spannable配置上文本实例
```

### Android 用HttpURLConnection或HttpClient接口来开发网络程序。

网络通信使用最多的是Get和Post。Get和Post的不同之处在于Get的参数放在URL字符中，而Post的参数放在http请求数据中，通过输出流的方式发送给服务器。

主要步骤为：

1、创建一个URL对象：

URL url = new URL("http://XXXXXXX");</span>
用户登录存在用户名和密码的参数，如果是GET方式，那么需要将参数加载URL后面，如果是POST的方式，需要将参数作为输出流的方式发送给服务器：

GET方式：

			String data = "username=" + userName + "&password=" + password;
			URL url = new URL("http://XXXX?" + data);
POST方式：
			String data = "username=" + username + "&password=" + password;
			// 获得一个输出流，向服务器写数据，默认情况下，不允许程序向服务器输出数据
			OutputStream os = conn.getOutputStream();
			os.write(data.getBytes());
			os.flush();
			os.close();

在实际应用中需要考虑中文的编码问题



#### String的equals方法和contentEquals方法的比较

boolean equals(Object anObject);

boolean contentEquals(CharSequence cs);

这两个方法都可以用来比较String对象内容序列的异同，但也存在差异。

最大的差别就是String的equals方法只有在另一个对象是String的情况下才可能返回true，

而contentEquals只要求另一个对象是CharSequence或其子类的对象



### 在子线程中调用Toast

> 在子线程中弹出Toast，会报错：java.lang.RuntimeException: Can't toast on a thread that has not called Looper.prepare()。

解决方式：先调用`Looper.prepare();`再调用`Toast.makeText().show();`最后再调用`Looper.loop();`



### ERROR：注册POST请求，网络返回码：400

网络返回码：400 说明有异常，服务器不能理解接收的url的语法。

我仔细对比，在Postman得出来的url和自己准备Post的url

```
Url url = new Url(Path);
//再用一个向服务器输出的输出流包裹我的参数data，而问题就出在这个参数data
data = "?username="+ URLEncoder.encode(username,"UTF8")+"&password="+URLEncoder.encode(userpwd,
"UTF-8")+"&repassword="+URLEncoder.encode(userpwd_confirm,"UTF-8");

官网提供的注册接口为
https://www.wanandroid.com/user/register
在Postman上面填写参数后得到模拟的url例子为
https://www.wanandroid.com/user/register?username=13788595&password=12345678&repassword=12345678
所以我就将参数的前面加了个 ?  ，然后导致了服务器判断语法错误。

解决方法：
很简单，在data里面删掉这个多余的  ？ ，服务器就可以正常接收了

```

### Post请求放到一个工具类 PostUtil里面会如何？

对于HttpUrlConnetion来说，本身连接时会有一个隐连接，就藏在了：

```
is = urlConnection.getInputStream();//默认了开启了连接
```

```
/**
     * @param path 接口路径
     * @param data 参数数据
     * @return resultdata  以字符串形式返回POST请求得到的数据
     */
    public String sendPost(String path, String data) {
        //定义结果字符串
        String resultdata = null;
        InputStream is;
        try {
            //请求的地址为path，根据地址创建URL对象
            URL url = new URL(path);
            //根据URL对象打开链接
            HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();
            //设置请求的方式-Post
            urlConnection.setRequestMethod("POST");
            //设置请求的超时时间
            urlConnection.setReadTimeout(5000);
            urlConnection.setConnectTimeout(5000);
            // 设置请求的头
            urlConnection.setRequestProperty("Connection", "keep-alive");
            // 设置请求的头
            urlConnection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
            // 设置请求的头
            urlConnection.setRequestProperty("Content-Length", String.valueOf(data.getBytes().length));
            // 设置请求的头
            urlConnection.setRequestProperty("User-Agent", "Mozilla/5.0 (Windows NT 6.3; WOW64; rv:27.0) Gecko/20100101 Firefox/27.0");
//            //设置请求的头
//            String s = CookieUtil.getCookiePreference(MyApplication.getContext());
//            if(! s.equals("")) {
//                urlConnection.setRequestProperty("Cookie", s);
//                Log.e("Cookie",s);
//            }
            //发送POST请求必须设置允许输入和输出
            urlConnection.setDoInput(true);
            urlConnection.setDoOutput(true);
            //POST不能缓存
            urlConnection.setUseCaches(false);
            //创造对服务器端的输出流
                OutputStream os = urlConnection.getOutputStream();
                os.write(data.getBytes());
                os.flush();
            //cookieVal = urlConnection.getHeaderField("Set-Cookie");
            //System.out.println("获取到的Cookie："+cookieVal);
            //CookieUtil.saveCookiePreference(MyApplication.getContext(),cookieVal);
                os.close();
            if(urlConnection.getResponseCode() >= 400){
                is = urlConnection.getErrorStream();
                //System.out.println("网络码"+urlConnection.getResponseCode());
            } else {
                //System.out.println("网络码"+urlConnection.getResponseCode());
                //获取服务器端响应的输入流对象
                 is = urlConnection.getInputStream();
            }
                //创建字节输出流对象
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                int len = 0;
                byte buffer[] = new byte[1024];
                while ((len = is.read(buffer)) != -1) {
                    //根据读取的长度写入到os对象中
                    baos.write(buffer , 0 , len);
                }
                //释放资源
                is.close();
                baos.close();
                //返回结果字符串（JSON数据）
                resultdata = new String(baos.toByteArray());
                //System.out.println("返回数据"+ resultdata);
                //关闭连接
                urlConnection.disconnect();
            } catch (ProtocolException e1) {
            e1.printStackTrace();
        } catch (MalformedURLException e1) {
            e1.printStackTrace();
        } catch (IOException e1) {
            e1.printStackTrace();
        }
         return resultdata;
    }
```

sendPost() 这个方法，需要的参数是接口路径Path和要伴随POST请求的数据data，这个方法最终也会返回一个

以字符串形式返回POST请求得到的数据resultdata，而这个返回的数据就可以交给处理JSON数据的方法了。



#### 利用SharedPreferences实现自动登录

主要有两种方法，官网推荐第一种，将cookie持久化，但是我发现直接将成功登录后获取下来的cookie，在第二次的登录POST请求的时候，我简单的注入是失败的，

猜测的原因：cookie不能这么简单的注入请求头，携带肯定要携带进去，但是不代表将原封不动的将获取的Cookie又携带上。

1，获取的Cookie不够全面，这点我在Postman模拟登录时，手动填写了一堆Post请求时携带的Cookie值，结果还是失败，让我觉得很疑惑。

2.携带的方式不对，不能原封不动的直接注入。可能要做一部分修改，或者要用CookieManger 实例来管理，通过它来自动在POST请求时自动携带。

曲线救国方法：

SharedPreferences作为轻量级的键值对存储方式，甚至可以存储我成功登录后的用户名和密码，只要我在启动APP的时候，申请登录POST请求时，先判定我是否应该去请求登录。

如果需要登录则获取存储在SharedPreferences里面的用户名和密码，将它放进请求POST的方法里，不就如同在登录页面上做的一样了吧。



#### 显示 “Cannot resolve symbol XXX”

重启 Android Studio，重新 sync gradle，Clean build 都没有用。

多半是因为 Android Studio 之前发生了错误，某些 setting 出了问题。

**解决方法如下：**

**点击菜单中的 “File” -> “Invalidate Caches / Restart”，然后点击对话框中的 “Invalidate and Restart”，清空 cache 并且重启。语法就会正确的高亮了。**