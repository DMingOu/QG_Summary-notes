# QG训练营移动组第一周周记 :2019年3月22日

## 生活随记
这周开始了QG训练营的生活，了解到训练营是讲关于数据结构的内容，赶紧买了本实体书，然后从群上白嫖了本电子书大话数据结构，打算先看看前面的内容，以免跟不上师兄的节奏。
以后得在工作日做完学科作业，留下周末写训练营作业hh。不然就跟不上熬夜早起基础好的dl们了。


## 一周总结
### 3月22日
周五晚上第一次训练营，师兄讲了20分钟的链表知识，然后留下了链表作业。接着训练营的是小组培训，一轮自我介绍后认识很多新dl和志同道合的朋友。师兄介绍了Git和Markdonwn笔记的用法，得认真学学，非常有用。还要去了解一下C语言的多文件编程。
### 3月23日
早餐时间，看一篇技术博客，相比于知乎贴吧似乎更能下饭。
#### Markdown
有道云的Markdown笔记做的不错，某些语法如果忘了，可以通过工具栏召唤出来，这样子的笔记，排版就舒服很多了，目前发现的问题有：笔记换行需要在编辑页面多换一行，不然文字会忽略空格粘在一起；  

---
解决换行后字粘在一起的方法：在行尾加上两个空格再换行就OK


#### C语言多文件编程
概括：
```
    main.c文件：放main.c函数

    xxxx.h文件：声明函数和某些常量
      
    xxxx.c文件：实现xxxx.h中声明的函数
```

##### 关于#ifndef
头件的中的#ifndef，这是一个很关键的东西。比如你有两个C文件，这两个C文件都include了同一个头文件。而编译时，这两个C文件要一同编译成一个可运行文件，于是问题出现，大量的声明冲突。 

还是把头文件的内容都放在#ifndef和#endif中吧。不管你的头文件会不会被多个文件引用，你都要加上这个
每个头文件的这个“标识”都应该是唯一的。标识的命名规则一般是头文件名全大写，前后加下划线，并把文件名中的“.”也变成下划线，如：stdio.h

#ifndef_STDIO_H_

#define_STDIO_H_

#endif

//命名规则：头文件名全大写，前后加下划线，并把文件名中的“.”也变成下划线。 宏指令：接个空格，不然貌似会出现无效指令的error

#ifndef_XXXXXXXX_H_

#define_XXXXXXXX_H_

...
...

#endif

注意问题：变量一般不要定义在.h文件中。
#### 练手一个简单的多文件计算器
main.c

```
#include <stdio.h>
#include "getNum.h"
#include "display.h"

int main()
{
    int num_1,num_2;

    num_1=getNum_1();
    num_2=getNum_2();

    add(num_1,num_2);
    sub(num_1,num_2);
    mul(num_1,num_2);
    chu(num_1,num_2);

    return 0;
}

```
getNum.h

```
/*getNum.h用来声明输入计算器的两个数字的函数
 *如果声明的函数需要某个头文件，在此h文件中可以不用列出
 *#include <stdio.h>
 */

#ifndef _GETNUM_H_
#define _GETNUM_H_

int getNum_1();
int getNum_2();

#endif

```
getNum.c

```
#include <stdio.h>

 int getNum_1()
 {
   int a;
   printf("Let's intput your first number: ");
   scanf("%d",&a);
   printf("\n");
   printf("OK,your first number is %d\n",a);
   printf("\n");
   return a;
 }

 int getNum_2()
 {
   int a;
   printf("Let's intput your second number: ");
   scanf("%d",&a);
   printf("\n");
   printf("OK,your second number is %d\n",a);
   printf("\n");
   return a;
 }

```
display.h

```
/*声明关于计算的函数*/
#ifndef _DISPLAY_H_
#define _DISPLAY_H_

void add();//加
void sub();//减
void mul();//乘
void chu();//除

#endif

```
display.c

```
#include <stdio.h>

void add(int a,int b)
{
    printf("%d+%d的结果是%d\n",a,b,a+b);
}
void sub(int a,int b)
{
    printf("%d-%d的结果是%d\n",a,b,a-b);
}

void mul(int a,int b)
{
    printf("%d*%d的结果是%d\n",a,b,a*b);
}
void chu(int a,int b)
{
    float c=a;
    float d=b;
    if(d!=0)
      {
      	printf("%.1f/%.1f的结果是%.2f\n",c,d,c/d);
      }else {
	printf("除数不可为0");
      }
}

```
输入输出实例：
![snipaste_20190323_100104](ED829DA501704303AA7653854C0ED1D7)
总结：
代码内容虽然非常简单，但是过程并不简单，例如出现了函数重名报错，宏指令无效报错，忘记自己写的头文件应该用双引号“”，而不是尖括号<>等问题
### Git
注册登陆了自己github账号，创立了几个仓库，上传了自己刚打的代码，然后下载了桌面版的Github，感觉还是网页版比较舒服，上传文件，可以直接拖拽到网页上传，很流畅。虽然不打开Gui Base这样子显得懒，但是直接在网页端创建仓库上传文件写READ.md文档，个人觉得更加快捷方便。
### 代码规范
训练营作业需要有良好的代码风格，久仰阿里巴巴的JAVA开发手册，于是在网上找到后，阅读了其中一部分，我觉得里面的规范都很有意义。摘抄一部分基础的如下：
>
> (一)命名风格
>
> 1. 【强制】代码中的命名均不能以下划线或美元符号开始，也不能以下划线或美元符号结束

> 反例：_name / __name / $Object / name_ / name$ / Object$
> 2. 【强制】代码中的命名严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。
>   说明：正确的英文拼写和语法可以让阅读者易于理解，避免歧义。注意，即使纯拼音命名方式
>   也要避免采用。

> 正例：alibaba / taobao / youku / hangzhou 等国际通用的名称，可视同英文。  
> 反例：DaZhePromotion [打折] / getPingfenByName() [评分] / int 某变量 = 3
> 3. 【强制】类名使用 UpperCamelCase 风格，必须遵从驼峰形式，但以下情形例外：DO / BO /
>   DTO / VO / AO
>   正例：MarcoPolo / UserDO / XmlService / TcpUdpDeal / TaPromotion  
>   反例：macroPolo / UserDo / XMLService / TCPUDPDeal / TAPromotion  
> 4. 【强制】方法名、参数名、成员变量、局部变量都统一使用 lowerCamelCase 风格，必须遵从驼峰形式。
>   正例： localValue / getHttpMessage() / inputUserId
> 5. 【强制】常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长。  
>   正例：MAX_STOCK_COUNT
>   反例：MAX_COUNT
> 6. 【强制】抽象类命名使用 Abstract 或 Base 开头；异常类命名使用 Exception 结尾；测试类
>   命名以它要测试的类的名称开始，以 Test 结尾。
> 7. 【强制】中括号是数组类型的一部分，数组定义如下：  
>   String[] args;  
>   反例：使用 String args[]的方式来定义。

---
> (三)代码格式
> 1. 【强制】大括号的使用约定。如果是大括号内为空，则简洁地写成{}即可，不需要换行；如果
>   是非空代码块则：
>   1） 左大括号前不换行。
>   2） 左大括号后换行。
>   3） 右大括号前换行。
>   4） 右大括号后还有 else 等代码则不换行；表示终止的右大括号后必须换行。
> 2. 【强制】 左小括号和字符之间不出现空格；同样，右小括号和字符之间也不出现空格。详见
>   第 5 条下方正例提示。
>   反例：if (空格 a == b 空格)
> 3. 【强制】if/for/while/switch/do 等保留字与括号之间都必须加空格。
> 4. 【强制】任何二目、三目运算符的左右两边都需要加一个空格。
>   说明：运算符包括赋值运算符=、逻辑运算符&&、加减乘除符号等。
> 5. 【强制】采用 4 个空格缩进，禁止使用 tab 字符。
>   说明：如果使用 tab 缩进，必须设置 1 个 tab 为 4 个空格。IDEA 设置 tab 为 4 个空格时，
>   请勿勾选 Use tab character；而在 eclipse 中，必须勾选 insert spaces for tabs。  
>   正例：（涉及 1-5 点）  
>   public static void main(String[] args) {  
>   // 缩进 4 个空格  
>   String say = "hello";  
>   // 运算符的左右必须有一个空格  
>   int flag = 0;   
>   // 关键词 if 与括号之间必须有一个空格，括号内的 f 与左括号，0 与右括号不需要空格

```
if (flag == 0) {
System.out.println(say);
}
// 左大括号前加空格且不换行；左大括号后换行
if (flag == 1) {
System.out.println("world");
// 右大括号前换行，右大括号后有 else，不用换行
} else {
System.out.println("ok");
// 在右大括号后直接结束，则必须换行
}
}
```
> 6. 【强制】注释的双斜线与注释内容之间有且仅有一个空格。
>   正例：// 注释内容，注意在//和注释内容之间有一个空格。
> 7. 【强制】单行字符数限制不超过 120 个，超出需要换行，换行时遵循如下原则：
>   1） 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例。
>   2） 运算符与下文一起换行。
>   3） 方法调用的点符号与下文一起换行。
>   4） 方法调用时，多个参数，需要换行时，在逗号后进行。
>   5） 在括号前不要换行，见反例。
>   正例：

```
StringBuffer sb = new StringBuffer();
// 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行
sb.append("zi").append("xin")...
.append("huang")...
.append("huang")...
.append("huang");
```
> 反例：
> StringBuffer sb = new StringBuffer();  
> // 超过 120 个字符的情况下，不要在括号前换行  
> sb.append("zi").append("xin")...append  
> ("huang");  
> // 参数很多的方法调用可能超过 120 个字符，不要在逗号前换行  
> method(args1, args2, args3, ...  
> , argsX);  
> 8. 【强制】方法参数在定义和传入时，多个参数逗号后边必须加空格。  
>   正例：下例中实参的"a",后边必须要有一个空格。  
>   method("a", "b", "c");  

<html>
<!--在这里插入内容-->
</html>

https://files-cdn.cnblogs.com/files/han-1034683568/%E9%98%BF%E9%87%8C%E5%B7%B4%E5%B7%B4Java%E5%BC%80%E5%8F%91%E6%89%8B%E5%86%8C%E7%BB%88%E6%9E%81%E7%89%88v1.3.0.pdf  
#### 枚举
枚举类型默认为是int类型，并默认为从0,1,2,3...递增排序，也可为属性赋值，也可改变枚举类型的int类型（如将其换成byte来减少内存的消耗）  

```
typedef enum Status { 
	ERROR,     //默认为int 值为0
	SUCCESS,   //默认为int 值为1
} Status;
```

结构体可以将有限个不同类型的属性变量组合在一起，与枚举类型不同之处是枚举类型内的都是同类型的属性变量，并且结构体可以有结构函数。

### 3月24日
#### 训练营作业
第一周是关于链表的作业，本以为关于链表的题目，不会很难，但是没想到对我还是有一定难度，特别是第一次接触根据接口实现函数，发现有很多地方不懂，需要重新查阅资料，例如，多文件编程和各种形参和实参的类型，特别是还要实现交互，其他人貌似做的好快，所以我也花了好久时间去做，去理解题目。做完作业后，一个周末大部分的时间就过去了。  
**没有付出就没有回报:)**



## 存在问题
训练营作业做的不够快，bug多了，说明C语言知识还不够牢固。


## 下周规划
课余时间阅读第一行代码第五章和JAVA核心技术卷1。
周五下午预习数据结构。
工作日时间搞定学科作业。