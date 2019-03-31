# QG训练营移动组第二周周记：2019年3月25日

## 生活随记

电工学真的挺难，离散真的挺离散，感觉时间越来越不够用了。

打代码过久会导致颈椎炎的感觉QVQ，但还是要坚持修bug！



## 一周总结

### 数据持久化技术

持久化技术提供了一种机制可以让数据在瞬时状态和持久状态之间转换。

Android系统主要提供了三种方式用于简单地实现数据化持久化功能。

#### （1）文件储存（JAVA流）

##### 将数据存储到文件

Context 类中提供了一个 openFileOutput() 方法，接受两个参数，第一个是文件名，第二个是操作模式。操作模式主要有两种，MODE_PRIVATE 是默认的操作模式，指定同样文件名所写入内容会覆盖原文件中的内容。 MODE_APPEND则表示如果已存在就往里面追加内容，不存在就创建新文件。

通过 openFileOutput() 方法能够得到一个 FileOutputStream对象，借助它构建出一个 OutputStreamWriter对象

使用OutputStreamWriter对象 构建出一个 BufferedWriter 对象。用write(data),将数据写入文件。



##### 从文件中读取数据

Context 类中提供 openFileInput() 方法，只接收一个参数，即读取的文件名，方法自动到目录下加载这个文件，并且返回一个FileInputStream对象，用Java流的方式将数据读出来。

 openFileInput() 方法能够得到一个 FileInputStream对象，借助它构建出一个 InputStreamWriter对象。

InputStreamWriter对象 构建出一个 BufferedReader对象。

```java
/*reader是BufferReader对象,content是StringBuilder对象*/
String line = "";
while ((line = reader.readline()) != null) {
    content.append(line);
}
...
...
/*内容都存放在content中，最后就将内容返回*/
return content.toString(); 
```

**TextUtils.isEmpty()** 方法可以一次性进行两种空值的判断。当传入的字符串等于 null 或者等于空字符串的时候，这个方法都会返回 true。

进行文件读和写的操作时需要将过程放进 **try catch finally** 块里，try代码块里进行读写操作，catch IOException

finally 代码块进行关闭Writer，Reader对象的操作。

##### 总结

核心技术就是Context类里提供的openFileOutput() 方法，openFileInput()方法，利用JAVA的流进行读写操作。

但文件存储方式并不适合保存一些较为复杂的文本数据。

#### （2）ShardPreferences存储

使用**键值对**的方式来存储，读写数据需要提供一个键。

ShardShardPreferences支持多种不同的数据类型存储。

Android系统提供三种方式得到 ShardPreferences对象

###### 1.context类中的 getShardPreferences() 方法

第一个参数指定文件名，第二个参数指定操作模式，目前只有MODE_PRIVATE可选。

###### 2.Activity类中的 getShardPreferences() 

只接受一个操作模式参数，自动将当前活动的类名作为 ShardPreferences 的文件名。

###### 3.PreferenceManager 类中的 getDefaultShardPreferences() 方法

静态方法，接收Context参数，自动将当前活动的类名作为 ShardPreferences 的文件名。



得到后，用 ShardPreferences 对象的 edit() 方法获取一个  ShardPreferences.Editor 对象

**存入数据：**

 ShardPreferences.Editor 对象，用putBoolean(),putString()等方法添加数据

 apply() 方法将添加的数据提交，完成数据存储操作！

**读取数据：**

与put方法对应的get方法，接收两个参数，第一个是键值，第二个是键值找不到时会返回的默认值

调用ShardPreferences 对象的 getInt() ,getString()等方法



ShardPreferences.clear() 可以清除掉文件中的全部数据。



将密码以明文方式存储在 ShardPreferences文件中是非常不安全的，容易被盗取。

#### 面向对象

java基础感觉还不够扎实，重温面向对象知识。

##### 打印日历

```java
import java.time.*;

public class CalendarTest
{
    public static void main(String[] args){
        LocalDate date = LocalDate.now();
        int month = date.getMonthValue();
        int today = date.getDayOfMonth();

        date = date.minusDays(today-1); // 设置为每月的第一天
        DayOfWeek weekday = date.getDayOfWeek();
        int value = weekday.getValue(); //1 = Monday,...7 = Sunday

        System.out.println("Mon Tue Wed Thu Fri Sat Sun");
        for (int i = 1;i < value; i++)  //打印首行的缩进，使得月份第一天指向对应的星期几
            System.out.print("    ");
        while (date.getMonthValue() == month){
            System.out.printf("%3d",date.getDayOfMonth());
            if (date.getDayOfMonth() == today)
                System.out.printf("*"); //与今天重合就标记个*
            else
                System.out.print(" ");
            date = date.plusDays(1);
            if (date.getDayOfWeek().getValue() == 1) System.out.println();//到达新一周的第一天，就换行再打印
        }
        if (date.getDayOfWeek().getValue() != 1) System.out.println();
    }
}
```

输出示例：

 ![1553578110014](C:\Users\ODM\AppData\Roaming\Typora\typora-user-images\1553578110014.png)

#### 类

一个类包含实例域，构造器xN，和方法xN。

关键字**public**意味着任何类的任何方法都可以调用它。

关键字**private**确保实例域只有这个类自身的方法能够访问这些实例域，其他类的方法不能读写。

构造器总是伴随着**new**操作符的执行而被调用，但不能对一个已经存在的对象重新构造。例如：

```java
Tony.Employee("Tony Stark",9999999,1972,1,1);  //Error
```

[^注意]: 不要在构造器中定义与实例域重名的局部变量。

```java
public Employee (String n,double s,....){
    String name = n; //Error
    double salary = s; //Error
    ...
}
```

#### Java中方法参数的使用情况

1. 一个方法不能修改一个基本数据类型的参数（即数值型或者布尔型）

2. 一个方法可以改变一个对象参数的状态

3. 一个方法不能让对象参数引用一个新的对象

   [^说明]: Java对对象采用的不是引用调用，而是按值传递。

   

   #### 构造器的参数问题

   如果类中提供了一个有参数的构造器，但是没有提供无参数的构造器，则在构造对象时如果没有提供参数，就会被视为不合法。仅当类没有提供任何构造器的时候，系统才会提供一个默认的无参数构造器。

   ##### this关键字

   关键字this 引用方法的隐式参数，在类中，this指示隐式参数，也就是构造的对象啦。

   ```java
   public Employee (String aName, double aSalary) {
       this.name = aName ;
       this.salary = aSalary
   }
   ```

   如果构造器的第一个语句如this(...),这个构造器将会调用同一个类的另一个构造器。

   ```
   public Employee (double s) {
       //calls Employee(String , double)
       this("Employee #" + nextId, s);
       nextId++;
   }
   ```

   这样对公共的构造器代码部分只编写一次即可。

   #### 包

   注意：只能用星号（*）导入一个包，而不能使用import java.* * 导入以java为前缀的所有包。

   发生命名冲突时候，需要注意导入的包的名字，例如 java.util 和 java.sql 包都有日期(Date)类。

   假如这两个Date类都要使用，就要在每个类名的前面加上完整的包名。

   #### 类设计技巧

   - 一定要保证数据私有，不要破坏封装性
   - 一定要对数据初始化
   - 不要在类中使用过多的基本类型，用其他的类代替多个相关的基本类型
   - 不是所有的域都需要独立的域访问器和域更改器
   - 将一个很复杂的类分解成多个更为简单的类
   - 类名和方法名要能够体现它们的职责
   - 优先使用不可变的类

   #### 异常

   throws，在方法首部列出所有的异常类，每个异常类用逗号隔开。

   但是不需要声明JAVA的内部错误，即从Error继承的错误，同样，不应该声明从RuntimeException继承的那些非受查异常。我们应该将更多时间花费在修正程序中的错误上，而不是说明这些错误发生的可能性。

   总之，一个方法必须声明所有可能抛出的受查异常，而非受查异常要么不可控制，要么就应该避免发生。如果方法没有声明所有可能发生的受查异常，编译器会报错。

   [^注意]: 子类方法中可以抛出比父类更特定的异常，或者根本不抛出任何异常。如果父类方法没有抛出任何受查异常，子类也不能抛出任何受查异常

   手动抛出异常，例如：

   ```
   throw new FileFormatException();
   ```

   创建自己的异常类：

   定义的类应该包含两个构造器，一个默认的构造器，另一个带有详细描叙信息的构造器。

   抛出自定义的异常类型：

   先在方法首部声明自定义的异常，在代码内部，设置手动抛出异常对象。

##### 再次抛出异常与异常链

子系统故障的异常类型可能会产生多种解释。我的代码可能不想知道发生错误的具体细节原因，但希望明确知道它是否有问题。

```java
/*将原始异常设置为新异常的“原因”*/
try{
    access the database
}
catch (SQLException e) {
    Throwable se = new ServletException("database Error");
    se.initCause(e);
    throw se;
}

/*当捕获异常时，就可以使用下面这条语句重新得到原始异常*/
 Throwable e = se.getCause();
```

建议使用这种包装技术。可以让用户抛出子系统中的高级异常，而不会丢失原始异常的细节。

###### **警告：**

当finally子句中包含return 语句，在方法真正返回前，会执行finally子句。finally子句会有一个返回值，这个返回值覆盖了原始的返回值！

##### 关于异常的技巧

使用异常的基本规则：捕获异常所花费的时间会非常大，应该只在异常情况下使用异常机制。

当一段代码有很多个不同的操作，有必要将整个任务包装在一个try语句块中。

#### 四则运算表达式求值

##### 逆波兰式（后缀表达式）的计算

规则：从左到右遍历一个四则表达式的每一个数字和符号，遇到是数字就进栈，遇到是符号就将位于栈顶的两个数字出栈，进行运算，运算后的结果又进栈，一直到结束



##### 中缀表达式(日常)转换成后缀表达式

日常使用的标准四则运算表达式，叫做中缀表达式。

例如：中缀表达式："9+ (3-1) × 3 + 10 ÷ 2"→后缀表达式:"9 3 1 - 3 * + 10  2 / +"

转换规则：从左到右遍历中缀表达式的每个数字和符号，碰到数字直接输出，输出后的数字成为后缀式的一部分了；碰到了符号，则判断这个符号与栈顶符号的优先级：

1. 若此符号是右括号，则栈顶符号依次出栈输出，直到"("出栈为止后，此符号才入栈//左括号右括号只入栈出栈不输出；
2. 若此符号优先级低于栈顶符号，立刻让栈顶符号依次出栈输出，直到栈顶符号优先级小于此符号，然后此符号才入栈；
3. 若此符号优先级高于栈顶，此符号立刻入栈。

中缀表达式最后一个符号或数字结束后，将符号栈的符号以此出栈。

#### 两栈共享空间

栈是一种先进后出的结构，用顺序存储结构进行实现，也是很方便的，唯一的缺陷就是必须事先确定数组存储空间大小。对于2个**相同类型**的栈，我们可以**共享其存储空间**，最大限度的利用事先开辟的存储空间进行操作

关键思路：两个栈分别是数组的两端，向中间靠拢。top1和top2是栈和栈2的栈顶指针，俩栈顶指针不见面，2个栈就可以一直使用。两个栈见面时，也就是2个指针之间相差1时，即top1+1 == top2为栈满。

对于2栈共享空间的push方法，除了要插入元素值参数外，我们还需要判断是栈1还是栈2的栈号参stackNumber.注意：先判断栈满了没有，先栈顶top值变化，然后在相应栈顶赋值。

对于2栈共享空间pop方法，参数就只需判断栈1和栈2 ，空栈返回ERROR，然后相应栈顶元素出栈，然后top值变化。

#### 指针问题

C语言指针变量可以当做数组运用，在应用之前，应该先让指针指向一个数据块，可以是程序中的某个数组（这个就不说了），也可以指向一段新分配的内存空间；例如

```c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int i,*p;
    p=(int *)malloc(sizeof(int)*10); //分配10个整数的存储空间，p指针指向该空间
    for(i=0;i<10;i++) 
        p[i]=i; //平常数组一样访问p指向的存储空间
    for(i=0;i<10;i++)
        printf("%d ",p[i]);
    free(p);  //释放p指向的存储空间。 
}

```

VS2017 若出现无法打开引用源文件（头文件）xxxx.h 这样的问题

需要去找到这个头文件的路径的所在文件夹，然后按以下图片设置就ok了。

![1554009855437](C:\Users\ODM\AppData\Roaming\Typora\typora-user-images\1554009855437.png)

## 下周规划

看JAVA的**流！线程**

当然是做**小组作业！**

