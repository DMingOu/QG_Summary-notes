# QG训练营移动组第三周周记：2019年4月7日

## 生活随记

课余时间全都用来做小组作业，线程安全和socket连接。时间永远都是不等人的，只要用心挤，希望还是会有的！

刚开始做泛型队列作业有点懵逼，但还是挺过来了，VS导入头文件抽风，不得已使用卡顿CodeBlocks来做，幸好还是能做完。。

## 一周总结



#### Socket （很陌生）

Socket编程中,尽量用PrintWriter取代BufferedWriter，下面是PrintWriter的优点：

1. PrintWriter的print、println方法可以接受任意类型的参数，而BufferedWriter的write方法只能接受字符、字符数组和字符串；
2. PrintWriter的println方法自动添加换行，BufferedWriter需要显示调用newLine方法；
3. PrintWriter的方法不会抛异常，若关心异常，需要调用checkError方法看是否有异常发生；
4. PrintWriter构造方法可指定参数，实现自动刷新缓存（autoflush）；
5. PrintWriter 的构造方法更广。



Java内存模型只保证了基本读取和赋值是原子性操作，如果要实现更大范围操作的原子性，可以通过synchronized和Lock来实现。由于synchronized和Lock能够保证任一时刻只有一个线程执行该代码块，那么自然就不存在原子性问题了，从而保证了原子性

**可见性**

　　对于可见性，Java提供了volatile关键字来保证可见性。

　　当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。

　　而普通的共享变量不能保证可见性，因为普通共享变量被修改之后，什么时候被写入主存是不确定的，当其他线程去读取时，此时内存中可能还是原来的旧值，因此无法保证可见性。

　　另外，通过synchronized和Lock也能够保证可见性，synchronized和Lock能保证同一时刻只有一个线程获取锁然后执行同步代码，并且在释放锁之前会将对变量的修改刷新到主存当中。因此可以保证可见性。

#### BufferedReader的使用
BufferedReader:缓冲字符输入流，高级流。从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。可以以行为单位读取字符串。

构造函数

```
//创建一个使用默认大小(8192)输入缓冲区的缓冲字符输入流
public BufferedReader(Reader in);

//创建一个使用指定大小输入缓冲区的缓冲字符输入流
public BufferedReader(Reader in,int sz);

```

常用的方法

```
//读取单个字符
public int read();

//读入字符数组中的某一部分
public int read(char[] cbuf,int off,int len);

//读取一个文本行
public String readLine();

//跳过字符
public long skip(long n);
```



#### BufferedReader 中的readLine方法读不到内容的原因！（Bug）

  write方法必须要有字符串紧跟个\r\n,readline才能正常读取数据进来！

#### ==  与 equals方法 （Bug）

用（==）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。 

JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地 址，但在一些类库当中这个方法被覆盖掉了，如String,Integer,Date在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。
对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（==）进行比较的，所以比较后的结果跟双等号（==）的结果相同。

但因为**string属于复合数据类型，所以应该是使用equals**，假如我们使用==比较，肯定是比较它们的内存地址了。



**泛型交换**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//1.编写int类型的swap
void swap(int *vp1, int *vp2) {
    int a = *vp1;
    *vp1 = *vp2;
    *vp2 = a;
}
//2.引申至泛型swap
void swap(void *vp1, void *vp2, int size){
    char buffer[size];
    memcpy(buffer, vp1, size);
    memcpy(vp1, vp2, size);
    memcpy(vp2, buffer, size);
}
```

使用泛型swap交换C-string:

```
char* husband = "Fred";
char* wife = "Wilma";
swap(&husband,&wife,sizeof(char));
```

#### 线程作业遗留BUG

关于线程作业，无法在另一个类得到某一个类的main方法里面new出来的对象引用，在这个main() 方法里new出的对象引用其实是相当于的main的一个局部变量，所以在另一个类得不到这个引用。暂时没想到有什么好的方法解决这个问题。。

在Server端开启输入流，在Client端开启输出流，Client随机执行购票或退票函数后会在流输出一个字符串给Server端，Server根据这个字符串对票数进行操作。现在问题是，当开启两个Client端，Server端接收的字符串太慢了，暂停了两个Client端的那瞬间，Server端还没接收完那些字符串，但是如果暂停Client，不暂停Server端，Server又会莫名一直接收字符串，导致Server和Client持有票数老是对应不上，想知道这是**线程安全问题还是流问题**，个人感觉更像线程安全问题，但还没琢磨出怎么解决这个bug。。

挂上了百度知道和CSDN两天了，还是暂时没有得到回答。。



#### **匿名对象的使用:**

 **1.匿名对象也是一个对象,具有对象的所有功能**
 **2.每一次使用匿名对象时,都是一个新的对象， 每次创建匿名对象都是不同的对象，一个匿名对象,只能使用一次，即匿名对象只能调用一次**

匿名对象只在堆内存中开辟空间，而不存在栈内存的引用**



#### void *

如果函数的参数不指定，（或者说可以是任意类型）的指针，则使用void *

如：void *func(void *var1) { }

### CodeBlocks工程如何引用其他文件夹的头文件和源程序

假设你的工程名为project，目录为F:\test。但是你想在project中使用文件夹F:\library下面的一些头文件和源程序。由于这些头文件和源程序与工程project不在同一目录下面，所以需要做一些设置。下面介绍两种方法：

```c
1、第一步：在CodeBlocks界面中右击工程名project，点击Add files...菜单，将你所需要的F:\library目录下的头文件和源程序添加到工程project中。第二步：在你的工程project中的源程序要使用F:\library的头文件时，需要包含绝对路径。比如说要使用F:\library目录下的头文件a.h，需要写#include"F:\library\a.h"而不是#include"a.h"。

2、第一步与方法一相同。如果你觉得写绝对路径太麻烦，那你可以采用：点击CodeBlocks界面上方的Settings->Compiler->Global compiler settings->Search directories->Compiler，点击下方的Add按钮，将路径F:\library添加进去，最后点击OK。设置完成后便可以不用写绝对路径，编译器在当前目录要是找不到头文件的话便会去F:\library目录下寻找
```

#### VS 2017 神坑之一：头文件导入

头文件导入别的文件夹之后，需要在编辑器中添加否者文件下部会爆红。

如果爆出来错误有许多莫名的无中生有的关于外部头文件的错，需要什么呢！！

将这个头文件放到最最开头的引用，就可以消除这些莫名错误！

## 下周规划

学安卓知识，做小组作业App。及时做完作业！

