# QG训练营xx组第一周周记：2019年4月10日

## 生活随记





## 一周总结



**评估算法的时间复杂度**（用大O计数法）

•算法的时间复杂度用 **符号**O表示

•*O*(*n*)：其中*n*为数据输入量，这里表示算法的用时与输入量成正比，代表着一个算法的最坏情况的运行时间（上界）

注意！大O记法对数据量大的时候才会体现优势，并且丢失了原函数的一些信息，如：

T(N) = N +10000和 T(N) = N^2+10 对应了O(n)和 O(n^2)

大O记法小例子：

```c
for (int i = 1; i <= n;i++) {
    nums[i-1] = 0;
   for(j = 1; j <= n;j += i)
     nums[i-1]++;
}
```

   O(n+n/1+n/2+n/3+…+n/n)

=O(n+n(1/1+1/2+1/3+…+1/n))

=O( n+nln⁡(n) )

=O(nlgn)

**尾递归在编译器上的优化**

1.容易优化成为普通循环

2.如果有尾部归递，就只需要叠套一个堆栈

前提：递归函数不使用栈来记录变量，也不记录状态。尾递归被优化的本质是它的上下文不是保存在栈上，与调用者无关，这样就可以重用当前的栈帧。

**尾递归的优势：重用当前栈帧，不会造成堆栈溢出**

与普通递归相比，由于尾递归的调用处于方法的最后，因此方法之前所积累下的各种状态对于递归调用结果已经没有任何意义，因此完全可以把本次方法中留在堆栈中的数据完全清除，把空间让给最后的递归调用。这样的优化便使得递归不会在调用堆栈上产生堆积，意味着即时是“<u>无限”递归也不会让堆栈溢出</u>。这便是尾递归的优势。

```java
//尾递归，阶乘例子
static long fact(long n, long lastValue) {//lastValue表示要被相乘的上次求出的数，初始值应为1
		if (n < 0) return 0;
	    else if (n == 0) return 1;
	    else if (n == 1) return lastValue;
		return fact(n - 1, n *lastValue); //末尾自己调用自己
}

//普通递归，阶乘例子
static long fact(long n) {
		if (n < 0) return 0;
	    else if (n <= 1) return 1; //n == 1 || n == 0
		return n * fact(n - 1); //占用的临时内存空间随着次数越来越大，直至有直接返回值时，再从底层一                                   路向上返回到顶层。每次递归的结果都要暂时保留，无法清除
	}

```



#### C语言产生随机数方法

rand() 和 srand() 搭配

srand() 重新播种

```
//正确例子
# include <time.h>
# include <stdlib.h>
srand((unsigned)time(NULL)); /*该程序每次运行结果都不一样，因为每次启动程序的时间都不同。另外需要注意的是，使用time()函数前必须包含头文件time.h */
	for (int i = sizeof(a) / sizeof(int),j = 0; i >= 1; i--) {
		a[j++] = rand() ;
	}

//错误例子
# include <time.h>
# include <stdlib.h>

	for (int i = sizeof(a) / sizeof(int),j = 0; i >= 1; i--) {
		srand((unsigned)time(NULL)); //播种放在for循环内会导致种子都是相同，随机数全相同
		a[j++] = rand() ;
	}
```

- 求一定范围内的随机数。

如要取[0,10)之间的随机整数，需将rand()的返回值与10求模。

```
randnumber = rand() % 10;
```

那么，如果取的值不是从0开始呢？你只需要记住一个通用的公式。

要取[a,b)之间的随机整数（包括a，但不包括b)，使用：

(rand() % (b - a)) + a

- 伪随机浮点数。

要取得0～1之间的浮点数，可以用：

rand() / (double)(RAND_MAX)

如果想取更大范围的随机浮点数，比如0～100，可以采用如下方法:

rand() /((double)(RAND_MAX)/100)



##### C语言获取某段代码块的执行时间	

```
# include <time.h>

clock_t start = clock();
//代码块
clock_t finish = clock() - start;
```



#### 快速排序非递归版

```
//快速排序（非递归版）
void QuickSort(int *a, int size) {
	if(size <= 1) return;
	SqStack stk;  //需要准备一个栈
	int elem;
	int pivot;
	int begin = 0, end = size - 1; //end = size；会出bug
	initStack(&stk, size);
	pushStack(&stk, begin);
	pushStack(&stk, end);
	while (!isEmptyStack(&stk)) {
		if(getTopStack(&stk, &elem))
			end = elem;
		if(popStack(&stk, &elem))
			if(getTopStack(&stk, &elem))
				begin = elem;
		popStack(&stk, &elem);
		pivot = Partition(a, begin, end); //Partition方法用来取得头中尾三位置中值确定基准
		if (pivot - 1 > begin) {
			pushStack(&stk, begin);
			pushStack(&stk, pivot -1);
		}
		if (pivot + 1  < end ) {
			pushStack(&stk, pivot +1);
			pushStack(&stk, end);
		}
	}
}
```

思考：C语言是无法用C++里的STL的，只能自己写关于栈的函数，幸好之前写了栈的题目，可以拿来用。

快速排序，递归版比非递归版的优势就是快很多，几乎要快一半的时间，而且内存占满的情况暂时还没遇到，猜测单片开发的话，要对内存精打细算吧。

##### 程序运行时会出现 xxxx.exe 中的 0x00fa1c29 处有未经处理的异常: 0xC00000FD: Stack overflow

问题导致排序过程，数据量超过10万就会异常，因为堆栈保留内存默认只是1MB

该异常表示栈溢出，也就是局部变量太大了，超过了编译器的设定的值。

方法一：

局部变量通过动态分配，分配堆内存，即，new或者malloc来分配堆上面的内存

方法二：

将“项目属性、链接器、系统、堆栈保留大小”设大一点比如16000000(大数)



#### 基数排序 （有点复杂）

算法执行步骤：

(1)遍历序列找出最大的数(为的是确定最大的数是几位数)；

(2)开辟一个与数组大小相同的临时数组temp；

(3)用一个count数组统计原数组中某一位(从低位向高位统计)相同的数据出现的次数；

(4)用一个bucket数组计算原数组中某一位(从最低位向最高位计算)相同数据出现的位置；

(5)将桶中数据从小到大用temp数组收集起来；

(6)重复(3)(4)(5)直到所有位都被统计并计算过，用temp收集起来；

(7)将temp数组拷回到原数组中；

```
void RadixCountSort(int *a, int size) {
	int digit = 1;
	int base = 10;
	//获取数据最高的位数
	for (int i = 0; i < size; i++) {
		while (a[i] >= base) {
			++digit;
			base *= 10;
		}
	}
	base = 1;
	int *temp = (int *)calloc(size, sizeof(int));
	while (digit--) {
		int count[10] = { 0 };
		//统计某一位出现相同数字的个数,为了后来保持稳定
		for (int i = 0; i < size; i++) {
			int index = a[i] / base % 10;
			count[index]++;
		}
		int bucket[10] = { 0 };
		//统计个位相同的数在数组a中出现的位置
		for (int i = 1; i < 10 ; i++) {
			bucket[i] = count[i - 1] + bucket[i - 1];
		}
		//在桶里也要重新排序数据
		for (int i = 0; i < size; i++) {
			int index = a[i] / base % 10;
			temp[bucket[index]++] = a[i];
		}
		//将temp数组元素拷回原数组
		memcpy(a, temp, size * sizeof(int));
		base *= 10;
	}
	free(temp);
}
```



#### 找到第K大的数

##### 基于快速排序的方法，时间复杂度为o(n)

选取枢轴点，用快速排序和分治法将数组分为两部分，位于枢轴点左边的数都比它大，位于枢轴点右边的数都比它小，

1）如果枢轴点的索引刚好是k-1,则此时它对应的就是数组的第k大的数；

2）如果比k-1大，那么第k大的数位于它的左边部分，继续递归左边部分。

3）如果比k-1小，那么第k大的数位于它的右边部分，继续递归右边部分。

```c
//找到第K大的数
int findKthLargest(int *a, int K, int  begin, int end) {
	int low = begin;
	int high = end;
	int temp = a[low]; //枢轴点
	while (low < high) {
		while (low < high && a[high] <= temp) {
			high--;
		}
		//从高到低，遇到比枢轴小的数，放到低位low
		a[low] = a[high];
		while (low < high && a[low] >= temp) {
			low++;
		}
		//从低到高，遇到比枢轴大的数，放到高位high
		a[high] = a[low];
	}
	    //枢轴归位
		a[high] = temp;

		if (high == K - 1) {
			return temp;  //此时枢轴即是所求
		}
		else if (high > K - 1) {
			return  findKthLargest(a, K, begin, high - 1); //K在枢轴左边
		}
		else {
			return findKthLargest(a, K, high + 1, end);	//K在枢轴右边
		}
	}
```

#### 颜色排序(三色问题)

方法：使用两个索引控制0和2的放置位置，再用一个索引p1进行遍历，遇到0或2时与p1对应位置进行交换，
并再检查一次交换过来的值是否需要再放置，直到p1与p2相遇则结束

```c
void ColorSort(int *a, int size) {
	int p0 = -1;   //要让 a[0...p1]都为0
	int p2 = size; //要让 a[p2...size-1]都为2
	for (int p1 = 0; p1 < p2; ) { 
		if (a[p1] == 1) p1++;
		if (a[p1] == 2  ) {
			p2--;
			if (a[p1] != a[p2]) {
				swap(a, p1, p2);
			}
		}
		if (a[p1] == 0 ) {
			++p0;
			if (a[p0] != a[p1]) {
				swap(a, p0, p1);
			}
			p1++;
		}
	}
}

```

### bug

##### 数组名与指针的其中一点不同。。

##### 情况1：

int *a = (int *)calloc(dataCount, sizeof(int));

此时 a，只是一个指针，一个地址值，

sizeof(a) = 4      所以→    sizeof(a)/sizeof(int)  = 1

##### 情况2：

int a[10000]; 

a代表了一大块的内存

sizeof(a)/sizeof(int)  = 100000



## 存在问题



写算法时，有点粗心，在做排序算法的作业时，用了大量数据，而没有用小序列先测试，导致数据看的眼花缭乱没有留意到其实还没实现排序这个功能，时间越来越少，逼迫自己不停的重看代码错哪里，一遍遍调试，心里想如果第一遍就做对了，后面就没这么辛苦了，说明一定要细心加谨慎。

本周高数加讲座，大部分时间用来看排序算法，导致移动端知识几乎没怎么学，必须要挤时间啊，不然作业做不完啊。

## 下周规划

APP还剩一周时间，看来时间必须都是挤出来的。要拼一拼了。。



