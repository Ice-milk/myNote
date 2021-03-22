# C 语言笔记

### 1 判断浮点数逻辑上相等不能用`==`

要用`fabs(f1 - f2) < 0.00001`来近似判断

### 2 `scanf()`的返回值

`scanf()`函数读取数据返回int类型的值，表示读取数据的个数

使用`scanf()`读取后，再次读取char类型的值，例如`scnaf("%c")`或者`getchar()`，要在两句之间使用一个`getchar()`来去除缓冲区留下的换行符，否则后续读取会先读取到换行符，如：

```c
int n;
char c;
scanf(" %d", &n);
// getchar(); //去除换行符
c = getchar();
//否则，c读取到的就是换行符
```

### 3 `i++`与`++i`的异同

都是自增1

`++i`在序列点`;`之前完成自增，`i++`反之。

并且`++i`在运算符的优先级仅次于`()`

​	**在同一条语句中不能使用多条同一变量的自增自减语句，否则会出现混乱。**

### 4 C语言中，只有0为假，正负数都为真

### 5 好习惯：比较是否相等的表达式将常数放在左侧

这样可以防止误用`=`和`==`，如：`5 == num`

### 6 C存储类

存储类定义C程序中函数or变量的范围（可见性）和生命周期。

> 简易理解：
>
> **auto** 是局部变量的默认存储类, 限定变量只能在函数内部使用；
>
> **register** 代表了寄存器变量，不在内存中使用；
>
> **static**是全局变量的默认存储类,表示变量在程序生命周期内可见；
>
> **extern** 表示引用，引用其他文件中的函数或全局变量，不分配新的内存空间；

|          | auto                                                         | register                                                     | static                                                       | extern                                                       |
| -------- | :----------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 局部变量 | 默认，存储在栈内存中，函数调用结束后即释放，默认链接属性为external | 只用于局部变量，在寄存器中，没有内存地址，只能是char,int,unsigned | 存储在静态内存中，作用域在局部函数内，在程序生命周期内存在   | 当不清楚全局变量定义的位置时，引用全局变量，即使全局变量定义在函数后也可使用。 |
| 全局变量 | x                                                            | x                                                            | 作用域限制在本文件内，其他文件无法使用，链接属性变为internal | 引用其他文件中的全局变量                                     |

***作用域是程序中定义的变量所存在的区域，超过该区域变量就不能被访问。**

#### 链接属性

在 C 语言中，链接属性一共有三种：

- external（外部的）-- 多个文件中声明的同名标识符表示同一个实体
- internal（内部的）-- 单个文件中声明的同名标识符表示同一个实体
- none（无）-- 声明的同名标识符被当作独立不同的实体（比如函数的局部变量，因为它们被当作独立不同的实体，所以不同函数间同名的局部变量并不会发生冲突）

#### auto存储类

函数内局部变量的默认存储类，在函数内声明的变量都默认此存储类，它表示此变量只在本函数内有意义。

#### register存储类

变量将存储在CPU的寄存器中，受寄存器大小限制，而且不能进行取址运算`&`，因为没有内存地址。

常用于嵌入式硬件编程中，用于存储需要快速访问的变量，例如计数器。

VSIDE会自动优化代码，将计数器编译为register存储类。

#### static存储类

static修饰局部变量表示在程序运行过程中（生命周期内）始终保持局部变量的值存在，当再次调用子函数时，局部变量的值依然存在。

static修饰全局变量表示全局变量只能在其所声明的本文件内使用，即使同时编译，在不同文件中的函数无法调用本文件中的全局变量。

```c
#include <stdio.h>
 
/* 函数声明 */
void func1(void);
 
static int count=10;        /* 全局变量 - static 是默认的 */
 
int main()
{
  while (count--) {
      func1();
  }
  return 0;
}
 
void func1(void)
{
/* 'thingy' 是 'func1' 的局部变量 - 只初始化一次
 * 每次调用函数 'func1' 'thingy' 值不会被重置。
 */                
  static int thingy=5;
  thingy++;
  printf(" thingy 为 %d ， count 为 %d\n", thingy, count);
}

输出：
 thingy 为 6 ， count 为 9
 thingy 为 7 ， count 为 8
 thingy 为 8 ， count 为 7
 thingy 为 9 ， count 为 6
 thingy 为 10 ， count 为 5
 thingy 为 11 ， count 为 4
 thingy 为 12 ， count 为 3
 thingy 为 13 ， count 为 2
 thingy 为 14 ， count 为 1
 thingy 为 15 ， count 为 0
```

#### extern存储类

extern存储类用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。当您使用extern时，只声明变量名而不分配存储空间，直接引用在其他位置声明的同名全局变量或函数指向的存储空间，可以跨函数，甚至跨文件使用。

当您有多个文件且定义了一个可以在其他文件中使用的全局变量或函数时，可以在其他文件中使用extern来得到已定义的变量或函数的引用。可以这么理解，extern是用来在另一个文件中声明一个全局变量或函数的。

extern修饰符通常用于当有两个或多个文件共享相同的全局变量或函数的时候。

```c
#include <stdio.h>
 
// 函数外定义变量 x 和 y
int x;
int y;
int addtwonum()
{
    // 函数内声明变量 x 和 y 为外部变量
    extern int x;
    extern int y;
    // 给外部变量（全局变量）x 和 y 赋值
    x = 1;
    y = 2;
    return x+y;
}
 
int main()
{
    int result;
    // 调用函数 addtwonum
    result = addtwonum();
    
    printf("result 为: %d",result);
    return 0;
}
```

addtwonum.c文件：

```c
#include <stdio.h>
/*外部变量声明*/
extern int x ;
extern int y ;
int addtwonum()
{
    return x+y;
}
```

main.c文件：

```c
#include <stdio.h>
  
/*定义两个全局变量*/
int x=1;
int y=2;
int addtwonum();
int main(void)
{
    int result;
    result = addtwonum();
    printf("result 为: %d\n",result);
    return 0;
}
```

​	**同时编译两个源文件的方法：**

`gcc addtwonum.c main.c -o main`

### 7 异或`^`等位运算符

`^`：按位异或

`~`：按位取反

`>>`：二进制右移运算符。将一个数的各二进制位全部右移若干位，正数左补0，负数左补1，右边丢弃。

`<<`：二进制左移运算符。将一个运算对象的各二进制位全部左移若干位（左边的二进制位丢弃，右边补0）。

### 8 `switch case` 分支函数

```c
格式
    switch(变量)
    {
        case 1:
            break;
        case 2:
            break;
        default:
            前面的都不匹配，执行default里面的语句
    }
```

### 9 两种参数传递方式

传值调用（值传递）：复制变量值给形参，修改函数内形参的值不会改变实参的值，形参在函数结束时即释放。

引用调用（指针传递）：通过指针传递变量，形参为指向实参地址的指针，当对形参的指向操作时，就相当于对实参本身操作。

>   引用传递：这是C++里面的特性，本质和指针传递一样，只是函数原型中的参数是用&引用表示，传入的参数实际上就是实参。

### 10 数组

```c
声明数组
    double balance[10];
初始化
    double balance[2] = {12.5, 10.3};
或   double balance[] = {12.5, 10.3};
两个数组是一样的
// 当只初始化数组中的某一个或几个元素时，其他的元素会自动初始化为0

// C99标准新增了“可变长度数组”（VLA，variable length array），即数组大小可以在程序运行过程中才被指定，而不一定要在编译时就指定，如：
int n;
scanf(" %d ", &n);
int a[n];
// 这种写法是可以的。但是数组大小一旦确定便无法再更改了。
```

#### 数组作为函数传递参数的三种函数方法

```c
- void myfunction(int *param){}
- void myfunction(int param[10]){} //C语言不会检查数组的边界，所以也可以不输入数组长度
- void myfunction(int param[]){}
```

***实质上都是传递的数组首地址指针**，因为数组名本身实质上就是一个指向数组首地址的常量指针

```c
double *p;
double balance[10];

p = balance;
balance[2]，*(balance + 2)，*(p + 2)具有相同的意义，都是访问数组中的第3个空间
    但是balance是常量不可改变，p是指针变量可以改变
```

#### 数组作为函数返回值

C语言无法直接返回一个完整的数组，只能返回数组的一个值或指针，而局部变量的指针也无法返回，所以需要将要返回的数组用`static`定义为静态变量，函数返回类型定义为指针类型`type * func(){}`，然后在主函数中定义一个指针来读取这个变量。例：

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
 
/* 要生成和返回随机数的函数 */
int * getRandom( )
{
  static int  r[10];
  int i;
 
  /* 设置种子 */
  srand( (unsigned)time( NULL ) );
  for ( i = 0; i < 10; ++i)
  {
     r[i] = rand();
     printf( "r[%d] = %d\n", i, r[i]);
  }
  return r;
}
 
/* 要调用上面定义函数的主函数 */
int main ()
{
   /* 一个指向整数的指针 */
   int *p;
   int i;
 
   p = getRandom();
   for ( i = 0; i < 10; i++ )
   {
       printf( "*(p + %d) : %d\n", i, *(p + i));
   }
   return 0;
}
```

### 11 枚举类型`enum`

```c
声明枚举类型：
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
};
定义枚举变量：
eunm DAY day；

同时进行：
enum DAY
{
      MON=1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

第一个枚举成员的默认值为整型的 0，后续枚举成员的值在前一个成员上加 1。我们在这个实例中把第一个枚举成员的值定义为 1，第二个就为 2，以此类推。

枚举类型实质上是方便声明一系列整型变量。

### 12 指针

- 不能使用未经初始化的指针变量。对未经初始化的指针地址赋值可能会导致危险的问题，轻则程序混乱，重则程序甚至系统崩溃。

- 在指针变量声明的时候，如果没有确切的地址可以赋值，为指针变量赋一个 NULL 值是一个良好的编程习惯。赋为 NULL 值的指针被称为**空指针**，这样可以降低风险；在对指针进行解引用时，先检查该指针是否为 NULL。这种策略可以为你今后编写大型程序节省大量的调试时间。
- void类型的指针称为**通用指针**，任何类型的指针都可以赋值给它，但是解引用时需要进行强制数据类型转换，否则编译器不知道指向的数据类型，非必要，不常用。
- 指针变量本身占用4字节的内存空间（不同系统可能不同），存储一个内存地址。

#### 指针的算术运算`++、--、+、-`

-   指针的每一次递增，它其实会指向**下一个元素**的存储单元。(而非仅仅是地址+1)
-   指针的每一次递减，它都会指向**前一个元素**的存储单元。
-   指针在递增和递减时跳跃的字节数取决于指针所指向变量数据类型长度，比如 int 就是 4 个字节。

#### 指针数组`type *ptr[size]`

这个数组里存放着一系列指向相同类型数据的指针，可以方便存放多个字符串：

```c
#include <stdio.h>
 
const int MAX = 4;
 
int main ()
{
   const char *names[] = {
                   "Zara Ali",
                   "Hina Ali",
                   "Nuha Ali",
                   "Sara Ali",
   };
   int i = 0;
 
   for ( i = 0; i < MAX; i++)
   {
      printf("Value of names[%d] = %s\n", i, names[i] );
      // names[i]是一个指针地址，%s转换说明修饰符接受一个字符串的首地址，打印字符串
   }
   return 0;
}
```

指针作为函数参数传递和返回值的用法同数组

#### 指针与数组名的区别

```c
char str[10];
char* p = str;
int i = 0;
// 虽然*(str+i)和*(p+i)都可以访问数组内容，但是数组名和指针变量有本质的区别:
// 数组名是一个地址常量，是不可变的，它只代表一个地址，也就是数组的首地址，对数组名本身不能进行自增自减运算，例如str++是错误的。
// 而指针是一个左值，它本身有地址，地址内存储着另一个地址，指向了数组首地址，p++可以指向数组的下一元素。
```

#### 数组指针`type (*ptr)[size]`

数组指针是一个指针，它指向的是一个数组。

#### 数组指针和二维数组

```c
int arry[2][3] = ...;
int (*p)[3] = arry;
// (*p)[3]表示指向3个元素组成的数组的指针，arr也表示指向前3个元素组成的数组的地址
// *(*(p+1)+1) == *(*(arry+1)+1) == arry[1][1]
```

数组是**顺序存储结构**，多维数组依然是存储在一个连续的内存空间的，数组名存储的依然是第一个元素的地址，然后通过下标计算出某一个元素的地址。

### 13 函数指针和回调函数

函数指针是指向函数的指针变量，可以像一般函数一样调用函数、传递参数。

声明指针变量：

```c
typedef int (*fun_ptr)(int,int); // 声明一个指向同样参数、返回值的函数指针类型
```

回调函数就是一个作为某个函数参数的函数，就是通过函数指针实现的。

例：

```c
#include <stdlib.h>  
#include <stdio.h>
 
// 回调函数
void populate_array(int *array, size_t arraySize, int (*getNextValue)(void)) //函数指针
{
    for (size_t i=0; i<arraySize; i++)
        array[i] = getNextValue();
}
 
// 获取随机值
int getNextRandomValue(void)
{
    return rand();
}
 
int main(void)
{
    int myarray[10];
    /* getNextRandomValue 不能外加括号(有参数参数列表要加括号)，否则无法编译，因为加上括号之后相当于传入此参数时传入了 int , 而不是函数指针*/
    populate_array(myarray, 10, getNextRandomValue);
    for(int i = 0; i < 10; i++) {
        printf("%d ", myarray[i]);
    }
    printf("\n");
    return 0;
}

带参数的回调函数：
#include <stdio.h>

int sum(int a,int b){
    return a+b;
}

//sum2回调sum函数
int sum2(int num,int (*sum)(int,int),int a,int b){  //回调函数的参数也要作为原函数的参数输入
    return  num * sum(a,b);
}

int main()
{
    int (* p)(int a,int b)=sum;

    printf("SUM=%d\n",sum(1,2));
    printf("SUM2= %d\n",sum2(2,sum,1,2));
    return 0;
}
```

#### 函数指针作为返回值

```c
/* 一个使用函数指针作为返回值的加减计算器
*************************************/
#include <stdio.h>

// 方法：加
int add(int, int);
// 方法：减
int sub(int, int);
// 使用方法计算两个数的值，输入函数指针和两个数，输出计算结果。
int calc(int (*fp)(int,int), int, int);
// 根据输入的字符判断使用的方法，返回函数指针
int (*select(char))(int,int);

int add(int a, int b)
{
  return a+b;
}

int sub(int a, int b)
{
  return a-b;
}

int calc(int (*fp)(int, int), int a, int b)
{
  return (*fp)(a, b);
}

int (*select(char op))(int,int)
{
  switch(op)
  {
    case '+':
      return add;
    case '-':
      return sub;
    default:
      return NULL;
  }
}

int main(void)
{
  int num1, num2;
  char op;
  int (*fp)(int,int);

  printf("请输入一个算式，例如1+2:\n");
  scanf("%d%c%d", &num1, &op, &num2);

  fp = select(op);
  if (fp == NULL) return -1;
  printf("%d %c %d = %d\n", num1, op , num2, (*fp)(num1, num2));

  return 0;
}
```



### 14 操作字符串的函数

| 序号 | 函数 & 目的                                                  |
| ---- | ------------------------------------------------------------ |
| 1    | `strcpy(s1, s2);`复制字符串`s2`到字符串`s1`                  |
| 2    | `strcat(s1, s2);`连接字符串`s2`到`s1`的末尾，`s2`不变        |
| 3    | `strlen(s1);`返回字符串`s1`的长度                            |
| 4    | `strcmp(s1, s2);` 如果`s1==s2`返回`0`；`s1<s2`返回小于`0`；`s1>s2`返回大于`0`； |
| 5    | `strchr(s1, ch);`返回一个指针，指向`s1`中字符`ch`第一次出现的位置 |
| 6    | `strstr(s1, s2);`返回一个指针，指向`s1`中字符串`s2`第一次出现的位置 |

### 15 结构体

数组内存储的都是相同数据类型的变量，想要将不同数据类型的变量放在同一个结构中就要用到结构体。**结构体相当于一种新的数据类型，定义过后用法同`int`这些类似。**

使用流程：定义结构->定义变量->使用变量。

#### 定义结构

```c
struct:结构体数据类型
Tag:结构体类型名称，通常大写第一个单词
variable-list:使用此类型的变量

struct Tag { 
    member-list;
    member-list;
    member-list; 
    ...
} variable-list ;

typedef:自定义数据类型

typedef struct {
    member-list;
    member-list;
    member-list;
    ...
} Tag;
Tag variable-list;
```

例：

```c
//此结构体声明声明了3个成员
//同时又声明了结构体变量s1
//这个结构体并没有标明其标签
struct 
{
    int a;
    char b;
    double c;
} s1;
 
//此结构体声明声明了3个成员
//结构体的标签被命名为Simple,没有声明变量
struct Simple
{
    int a;
    char b;
    double c;
};
//用Simple标签的结构体，另外声明了变量t1、t2、t3
//t2是一个数组，它的每一个成员都被定义为结构体类型
//t3是指针，它指向一个结构体
struct Simple t1, t2[20], *t3;
 
//也可以用typedef创建新类型
typedef struct
{
    int a;
    char b;
    double c; 
} Simple2;
//现在可以用Simple2作为类型声明新的结构体变量
Simple2 u1, u2[20], *u3;
```

结构体的成员可以包含其他结构体，也可以包含指向自己结构体类型的指针，而通常这种指针的应用是为了实现一些更高级的数据结构如链表和树等。

```c
//此结构体的声明包含了其他的结构体
struct Complex
{
    char string[100];
    struct Simple a;
};
 
//此结构体的声明包含了指向自己类型的指针
struct Node
{
    char string[100];
    struct Node *next_node;
};
```

如果两个结构体互相包含，则需要对其中一个结构体进行不完整声明。

```c
struct B;    //对结构体B进行不完整声明
 
//结构体A中包含指向结构体B的指针
struct A
{
    struct B *partner;
    //other members;
};
 
//结构体B中包含指向结构体A的指针，在A声明完后，B也随之进行声明
struct B
{
    struct A *partner;
    //other members;
};
```

#### 结构体变量初始化

```c
#include <stdio.h>
 
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} book = {"C 语言", "RUNOOB", "编程语言", 123456};//对变量赋初值
 
int main()
{
    printf("title : %s\nauthor: %s\nsubject: %s\nbook_id: %d\n", book.title, book.author, book.subject, book.book_id);
}

// 初始化指定成员值
struct Books book = {.title = "C 语言", .author = "RUNOOB"};
```

#### 结构体占用的内存（内存对齐）

参考文献：

[《失传的 C 结构体打包技艺》](https://github.com/ludx/The-Lost-Art-of-C-Structure-Packing)(github翻译版，作者：Eric S. Raymond)

#### 访问结构成员

可以在结构类型的变量后使用`.`(英文句号)来访问成员，或在指向结构类型区域的指针变量后用`->`来访问成员。结构体作为函数参数需要使用指针传递。

```c
#include <stdio.h>
#include <string.h>
 
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
};
 
/* 函数声明 */
void printBook( struct Books *book );//指针传参
int main( )
{
   struct Books Book1;        /* 声明 Book1，类型为 Books */
   struct Books Book2;        /* 声明 Book2，类型为 Books */
 
   /* Book1 详述 */
   strcpy( Book1.title, "C Programming");//结构变量使用句号访问成员
   strcpy( Book1.author, "Nuha Ali"); 
   strcpy( Book1.subject, "C Programming Tutorial");
   Book1.book_id = 6495407;
 
   /* Book2 详述 */
   strcpy( Book2.title, "Telecom Billing");
   strcpy( Book2.author, "Zara Ali");
   strcpy( Book2.subject, "Telecom Billing Tutorial");
   Book2.book_id = 6495700;
 
   /* 通过传 Book1 的地址来输出 Book1 信息 */
   printBook( &Book1 );
 
   /* 通过传 Book2 的地址来输出 Book2 信息 */
   printBook( &Book2 );
 
   return 0;
}
void printBook( struct Books *book )
{
   printf( "Book title : %s\n", book->title);//结构体指针使用->访问成员
   printf( "Book author : %s\n", book->author);
   printf( "Book subject : %s\n", book->subject);
   printf( "Book book_id : %d\n", book->book_id);
}
当上面的代码被编译和执行时，它会产生下列结果：

Book title : C Programming
Book author : Nuha Ali
Book subject : C Programming Tutorial
Book book_id : 6495407
Book title : Telecom Billing
Book author : Zara Ali
Book subject : Telecom Billing Tutorial
Book book_id : 6495700
```

**注：**与数组不同结构体的名称并不是指向数据的地址，所以不能将结构体名称直接赋值给结构体指针，需要使用取址符`&`。

```c
struct Book book;
struct Book *ptb;
ptb = &book;
```



### 16 位域

"位域"是把一个字节中的二进位划分为几个不同的区域，并说明每个区域的位数。每个域有一个域名，允许在程序中按域名进行操作。这样就可以把几个不同的对象用一个字节的二进制位域来表示。

#### 位域的定义

位域定义与结构定义相仿

```c
struct 位域结构名 
{
 位域列表
 类型说明符 位域名: 位域长度;
} 位域变量;
例：
struct bs
{
 int a:8;
 int b:2;
 int c:6;
}data;

data为bs变量，共占两个字节。其中位域a占8位，位域b占2位，位域c占6位。
```

**对于位域的定义尚有以下几点说明：**

-   一个位域存储在同一个字节中，如一个字节所剩空间不够存放下一个位域时，则会从下一单元起存放该位域。也可以有意使某位域从下一单元开始。例如：

```c
struct bs{
    unsigned a:4;
    unsigned  :4;    /* 空域 */
    unsigned b:4;    /* 从下一单元开始存放 */
    unsigned c:4
}
在这个位域定义中，a占第一字节的4位，后4位填0表示不使用，b从第二字节开始，占用4位，c占用4位。
```

-   由于位域不允许跨两个字节，因此位域的长度不能大于一个字节的长度，也就是说不能超过8位二进位。如果整个位域结构长度大于计算机的整数字长，一些编译器可能会允许域的内存重叠，另外一些编译器可能会把大于一个字的部分存储在下一个字中。**所以单一位域长度不超过一字节，位域总长不超过一字**。
-   位域可以是无名位域，这时它只用来作填充或调整位置。无名的位域是不能使用的。如上面的空域。

**位于本质上是一种结构类型，不过其成员是按位分配的。**

#### 位域的使用

位域的使用同结构的使用相同。使用`.或->`来访问成员。

```c
main(){
    struct bs{
        unsigned a:1;
        unsigned b:3;
        unsigned c:4;
    } bit,*pbit;
    bit.a=1;    /* 给位域赋值（应注意赋值不能超过该位域的允许范围） */
    bit.b=7;    /* 给位域赋值（应注意赋值不能超过该位域的允许范围） */
    bit.c=15;    /* 给位域赋值（应注意赋值不能超过该位域的允许范围） */
    printf("%d,%d,%d\n",bit.a,bit.b,bit.c);    /* 以整型量格式输出三个域的内容 */
    pbit=&bit;    /* 把位域变量 bit 的地址送给指针变量 pbit */
    pbit->a=0;    /* 用指针方式给位域 a 重新赋值，赋为 0 */
    pbit->b&=3;    /* 使用了复合的位运算符 "&="，相当于：pbit->b=pbit->b&3，位域 b 中原有值为 7，与 3 作按位与运算的结果为 3（111&011=011，十进制值为 3） */
    pbit->c|=1;    /* 使用了复合位运算符"|="，相当于：pbit->c=pbit->c|1，其结果为 15 */
    printf("%d,%d,%d\n",pbit->a,pbit->b,pbit->c);    /* 用指针方式输出了这三个域的值 */
}
```

### 17 共用体

共用体是一种在同一个内存段存储多种类型数据的数据类型，和结构体的区别是，在一个时刻，共用体只能存储一种类型的数据，而结构体可以同时开辟多个存储空间，同时存储多种类型的数据。

#### 定义共用体

```c
union [union tag]
{
   member definition;
   member definition;
   ...
   member definition;
} [one or more union variables];
如：
union Data
{
   int i;
   float f;
   char  str[20];
} data;

//Data 类型的变量可以存储一个整数、一个浮点数，或者一个字符串。这意味着一个变量（相同的内存位置）可以存储多种类型的数据。
```

共用体占用的内存应足够存储共用体中最大的成员。例如，在上面的实例中，Data 将占用 20 个字节的内存空间，因为在各个成员中，字符串所占用的空间是最大的。

#### 访问共用体成员

和结构体类似，使用`.`访问成员

```c
#include <stdio.h>
#include <string.h>
 
union Data
{
   int i;
   float f;
   char  str[20];
};
 
int main( )
{
   union Data data;        
 // 一个时刻只能存储一种类型的数据
   data.i = 10;
   printf( "data.i : %d\n", data.i);
   // 此时data.i里的数据将被覆盖
   data.f = 220.5;
   printf( "data.f : %f\n", data.f);
   
   strcpy( data.str, "C Programming");
   printf( "data.str : %s\n", data.str);
 
   return 0;
}
```

### 18 typedef

`typedef`关键字可以为数据类型取一个新的名字，如：

```c
typedef unsigned char BYTE;
```

按照惯例，定义时会大写字母，以便提醒用户类型名称是一个象征性的缩写，在这里标识符`BYTE`可作为类型`unsigned char`的缩写。

`typedef`也可以为自定义的数据类型取一个新的名字，如自定义的结构体：

```c
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} Book;
```

将结构体`Books`定义了新名字`Book`，实际应用中`Books`可以省略，可以作为一种定义结构体的语法。

### 19 输入&输出

输入不仅仅是命令行输入，也包括从文件读取输入。

输出不只是屏幕输出，也包括输出到其他输出设备和写入文件中。

C语言把所有的设备都当作文件，所以设备被处理的方式和文件相同。

输入输出常用的有4组函数：

```c
单字符读取/输出：
getchar();// 可以通过循环读取多个字符
putchar();

字符串读取/输出(仅windows可用，所以不常用)：
gets(); // 读取字符串以\0或EOF结束
puts();

fgets(数组指针, 数组大小, 输入位置); //如：fgets(*s, sizeof(s), stdin);
fputs();

格式化读取/输出：
printf();
scanf();
```

### 20 文件读写

```c
#include <stdio.h>
 
int main()
{
   FILE *fp = NULL; //定义文件指针
 
   fp = fopen("/tmp/test.txt", "w+"); //打开文件，fopen返回打开的文件指针
   fprintf(fp, "This is testing for fprintf...\n");// 两种写入方式
   fputs("This is testing for fputs...\n", fp);
   fclose(fp);//关闭文件
}
```

流程：定义文件指针->打开文件->读取、写入->关闭

#### 定义文件指针

C语言通过指针操作文件，所以需要先定义一个空文件指针，文件指针是`FILE`类型的。

```c
FILE *fp = NULL;
```

#### 打开文件

打开文件使用的函数是`fopen()`，但更推荐`fopen_s()`，更安全。

函数原型：

```c
FILE *fopen( const char * filename, const char * mode );
//返回值为`FILE`类型文件指针，第一个参数是文件名，第二个是访问模式

errno_t fopen_s( FILE** pFile, const char *filename, const char *mode );
//fopen_s有三个参数，第一个参数是二级指针，是文件指针的地址，这就不需要返回文件指针了，其他的同 fopen 一致。
例：
FILE* fp=NULL;
fp=fopen("\\123.txt","w+");
fopen_s(&fp,"\\123.txt","w+");
```

##### 访问模式

打开文件的时候需要制定访问模式，C语言中有以下几种文件访问模式：

| 模式 | 二进制模式 | 描述                                                         |
| ---- | ---------- | ------------------------------------------------------------ |
| r    | rb         | 读取，打开**已有**的文件读取内容。                           |
| w    | wb         | 写入，创建新文件，如果已存在，会覆盖原文件内容。             |
| a    | ab         | 追加，可创建新文件。如果文件存在，则会在已有的文件内容后面追加内容。 |
| r+   | rb+        | 可读可写，打开已有文件。                                     |
| w+   | wb+        | 可读可写，创建新文件，如果已存在，会覆盖原文件内容。         |
| a+   | ab+        | 可读，可追加写入，可创建新文件。                             |

#### 读取\写入

有这些函数可以读写文件：

```c
/*读取*/
int fgetc( FILE * fp );//逐字符读取
char *fgets( char *buf, int n, FILE *fp );//字符串读取，指定缓存空间，指定读取长度（字节）。
int fscanf(FILE *fp, const char *format, ...);//按指定格式读取，如果读取字符串，一个%s只能读一个单词

/*写入*/
int fputc( int c, FILE *fp );//逐字符写入，c为char字符，会返回写入的字符
int fputs( const char *s, FILE *fp );//字符串写入
int fprintf(FILE *fp,const char *format, ...);//格式化写入
```

#### 关闭

文件操作过后一定记得关闭文件，释放文件指针。

```c
int fclose( FILE *fp );
```

如果成功关闭文件，**fclose( )** 函数返回零，如果关闭文件时发生错误，函数返回 **EOF**。这个函数实际上，会清空缓冲区中的数据，关闭文件，并释放用于该文件的所有内存。EOF 是一个定义在头文件 **stdio.h** 中的常量。

### 21 预处理器

| 指令     | 描述                                                        |
| :------- | :---------------------------------------------------------- |
| #define  | 定义宏                                                      |
| #include | 包含一个源代码文件                                          |
| #undef   | 取消已定义的宏                                              |
| #ifdef   | 如果宏已经定义，则返回真                                    |
| #ifndef  | 如果宏没有定义，则返回真                                    |
| #if      | 如果给定条件为真，则编译下面代码                            |
| #else    | #if 的替代方案                                              |
| #elif    | 如果前面的 #if 给定条件不为真，当前条件为真，则编译下面代码 |
| #endif   | 结束一个 #if……#else 条件编译块                              |
| #error   | 当遇到标准错误时，输出错误消息                              |
| #pragma  | 使用标准化方法，向编译器发布特殊的命令到编译器中            |

```c
#include <stdio.h>
#include "myheader.h"
```

这些指令告诉 CPP 从**系统库**中获取 `stdio.h`，并添加文本到当前的源文件中。下一行告诉 CPP 从本地目录中获取 `myheader.h`，并添加内容到当前的源文件中。

#### 预定义宏

| 宏         | 描述                                                |
| :--------- | :-------------------------------------------------- |
| `__DATE__` | 当前日期，一个以 "MMM DD YYYY" 格式表示的字符常量。 |
| `__TIME__` | 当前时间，一个以 "HH:MM:SS" 格式表示的字符常量。    |
| `__FILE__` | 这会包含当前文件名，一个字符串常量。                |
| `__LINE__` | 这会包含当前行号，一个十进制常量。                  |
| `__STDC__` | 当编译器以 ANSI 标准编译时，则定义为 1。            |

例：

```c
#include <stdio.h>

int main(void)
{
   printf("File :%s\n", __FILE__ );
   printf("Date :%s\n", __DATE__ );
   printf("Time :%s\n", __TIME__ );
   printf("Line :%d\n", __LINE__ );
   printf("ANSI :%d\n", __STDC__ );
   return 0;
}
输出：
File :test.c
Date :Jun 2 2012
Time :03:36:24
Line :8
ANSI :1
```

### 22 头文件

头文件是扩展名为 **.h** 的文件，包含了 C 函数声明和宏定义，被多个源文件中引用共享。有两种类型的头文件：程序员编写的头文件和编译器自带的头文件。

```c
//引用编译器自带的头文件，系统头文件
#include <file.h>

//引用自己编写的头文件，用户头文件，它会在包含当前文件的目录内搜索
#include "file.h"
```

引用头文件相当于复制头文件的内容。A simple practice in C 或 C++ 程序中，建议把所有的**常量、宏、系统全局变量和函数原型**写在头文件中，在需要的时候随时引用这些头文件。

#### 防止重复引用同一个头文件

```c
#ifndef HEADER_FILE
#define HEADER_FILE
//头文件内容

#endif
```

这种结构就是通常所说的包装器`#ifndef`。当再次引用头文件时，条件为假，因为`HEADER_FILE`已定义。此时，预处理器会跳过文件的整个内容，编译器会忽略它。

### 23 两个递归例子

#### 阶乘

```c
#include <stdio.h>
 
double factorial(unsigned int i)
{
   if(i <= 1)
   {
      return 1;
   }
   return i * factorial(i - 1);
}
int  main()
{
    int i = 15;
    printf("%d 的阶乘为 %f\n", i, factorial(i));
    return 0;
}
//输出：
15 的阶乘为 1307674368000.000000
```

#### 斐波那契数列

```c
#include <stdio.h>
 
int fibonaci(int i)
{
   if(i == 0)
   {
      return 0;
   }
   if(i == 1)
   {
      return 1;
   }
   return fibonaci(i-1) + fibonaci(i-2);
}
 
int  main()
{
    int i;
    for (i = 0; i < 10; i++)
    {
       printf("%d\t\n", fibonaci(i));
    }
    return 0;
}
//输出：
0
1
1
2
3
5
8
13
21
34
```

### 24 可变参数

一个函数的参数数量不确定，该如何定义函数？

```c
#include <stdio.h>
#include <stdarg.h>

/*定义一个函数，最后一个参数为省略号，
省略号前面可以设置自定义参数。*/
double average(int num,...)//num为参数数量
{
 /*在函数定义中创建一个 va_list 类型变量，
 该类型是在 stdarg.h 头文件中定义的。*/
    va_list valist;
    double sum = 0.0;
    int i;
 
    /* 使用 va_start 为 num 个参数初始化 valist */
    va_start(valist, num);
 
    /* 使用 va_arg 访问所有赋给 valist 的参数 */
    for (i = 0; i < num; i++)
    {
       sum += va_arg(valist, int);
    }
    /* 使用 va_end 清理为 valist 保留的内存 */
    va_end(valist);
 
    return sum/num;
}
 
int main()
{
   printf("Average of 2, 3, 4, 5 = %f\n", average(4, 2,3,4,5));
   printf("Average of 5, 10, 15 = %f\n", average(3, 5,10,15));
}
//输出
Average of 2, 3, 4, 5 = 3.500000
Average of 5, 10, 15 = 10.000000
```

### 25 内存管理

C 语言为内存的分配和管理提供了几个函数。这些函数可以在 **<stdlib.h>** 头文件中找到。

| 序号 | 函数和描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | `void *calloc(int num, int size);`在内存中动态地分配`num`个长度为`size`的连续空间，并将每一个字节都初始化为 0。所以它的结果是分配了`num*size`个字节长度的内存空间，并且每个字节的值都是0。 |
| 2    | `void free(void *address);` 该函数释放`address`所指向的内存块,释放的是动态分配的内存空间。 |
| 3    | `void *malloc(int num);` 在堆区分配一块指定大小的内存空间，用来存放数据。这块内存空间在函数执行完成后不会被初始化，它们的值是未知的。 |
| 4    | `void *realloc(void *address, int newsize);` 该函数重新分配内存，把内存扩展到`newsize`。如果指针为NULL，相当于calloc()；如果newsize为0，相当于free()；如果分配内存失败，需要手动释放原内存空间。 |

**注意：**`void *` 类型表示未确定类型的指针。C、C++ 规定 `void *` 类型可以通过类型转换强制转换为任何其它类型的指针。

#### 动态分配内存

数组分配内存大小后就不可以改变了，但是使用内存管理的函数就可以动态的分配和管理/释放内存。

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
 
int main()
{
   char name[100];//数组内存分配后就不可变了
   char *description;
 
   strcpy(name, "Zara Ali");
 
   /* 动态分配内存 */
   description = (char *)malloc( 30 * sizeof(char) );
   // description = (cahr *)calloc(30, sizeof(char));//等效
   if( description == NULL )
   {
      fprintf(stderr, "Error - unable to allocate required memory\n");
   }
   else
   {
      strcpy( description, "Zara ali a DPS student.");
   }
   /* 假设您想要存储更大的描述信息 */
   char *new_description = (char *) realloc( description, 100 * sizeof(char) );
   if( new_description == NULL )
   {
      fprintf(stderr, "Error - unable to allocate required memory\n");
   }
   else
   {
      description = new_description;
      strcat( description, "She is in class 10th");
   }
   
   printf("Name = %s\n", name );
   printf("Description: %s\n", description );
 
   /* 使用 free() 函数释放内存 */
   free(description);
}
//输出
Name = Zara Ali
Description: Zara ali a DPS student.She is in class 10th
```

**注：**

- 每次分配内存后都要检查是否分配成功
- 使用内存管理函数分配的内存存在于堆中，使用完后必须手动释放，否则可能导致内存泄漏（无限申请空间，内存爆满，程序卡死）。
- 申请内存的指针在释放之前不能赋其他地址，否则会导致内存块丢失，无法释放，最终导致内存泄漏。

#### `realloc()`的一个bug

例如这个`realloc`函数的bug：

```c
void *ptr = realloc(ptr, new_size);
if (!ptr) {
 //错误处理
}
```


这里就引出了一个内存泄露的问题，当`realloc`分配失败的时候，会返回NULL。但是参数中的`ptr`的内存是没有被释放的。如果直接将`realloc`的返回值赋给`ptr`。那么当申请内存失败时，就会造成`ptr`原来指向的内存丢失，造成泄露。

正确的处理应该是这样：

```c
void *new_ptr = realloc(ptr, new_size);
if (!new_ptr) {
    //错误处理。
}
ptr = new_ptr;
```

#### 内存布局

典型的c语言内存空间划分：

![img](https://i.loli.net/2021/03/19/eYgjahSfXM5W2Cu.png)

根据内存地址从低到高分别划分为：

- 代码段（Text segment）

  - 代码段通常是指用来存放**程序执行代码**的一块内存区域。

    这部分区域的大小在程序运行前就已经确定，并且内存区域通常属于只读。

    在代码段中，也有可能包含一些只读的**常量**，例如字符串常量等。

- 数据段（Initialized data segment）

  - 数据段通常用来存放**已经初始化的**全局变量和局部静态变量。

- BSS段（Bss segment/Uninitialized data segment）

  - BSS 段通常是指用来存放程序中**未初始化的**全局变量的一块内存区域。

    BSS 是英文 Block Started by Symbol 的简称，这个区段中的数据在程序运行前将被自动初始化为数字 0。

- 栈（Stack）

  - 栈是函数执行的内存区域，通常和堆共享同一片区域。在函数执行时创建，函数结束后释放。局部变量存放在栈中。

- 堆（Heap）

  - 使用**动态内存管理**函数申请的内存空间就是分配在堆里边。

    所以，堆是用于存放进程运行中被动态分配的内存段，它的大小并不固定，可动态扩展或缩小。

    当进程调用 ``malloc`` 等函数分配内存时，新分配的内存就被动态添加到堆上；当利用 ``free`` 等函数释放内存时，被释放的内存从堆中被剔除。

**堆和栈的对比**

堆和栈是 C 语言运行时最重要的元素，下面我们将两者进行对比。

申请方式：
- 堆由程序员手动申请
- 栈由系统自动分配

释放方式：
- 堆由程序员手动释放
- 栈由系统自动释放

生存周期：
- 堆的生存周期由动态申请到程序员主动释放为止，不同函数之间均可自由访问
- 栈的生存周期由函数调用开始到函数返回时结束，函数之间的局部变量不能互相访问

发展方向：
- 堆和其它区段一样，都是从低地址向高地址发展
- 栈则相反，是由高地址向低地址发展

### 26 命令行参数

`main()`函数带有参数就可以实现命令行传入参数。

```c
#include <stdio.h>

int main( int argc, char *argv[] )  //argc表示参数个数，argv存储参数数组
{
   printf("Program name %s\n", argv[0]);//第一个参数是函数名称
 
   if( argc == 2 )
   {
      printf("The argument supplied is %s\n", argv[1]);
   }
   else if( argc > 2 )
   {
      printf("Too many arguments supplied.\n");
   }
   else
   {
      printf("One argument expected.\n");
   }
}
//运行，一个字符串参数中有空格要用""包围，否则会被认为是两个参数。

$./a.out "testing1 testing2"

Progranm name ./a.out
The argument supplied is testing1 testing2
```

`argv[0]` 存储程序的名称，`argv[1]` 是一个指向第一个命令行参数的指针，`*argv[n]` 是最后一个参数。如果没有提供任何参数，`argc` 将为 1，如果传递了一个参数，`argc` 将被设置为 2，两个参数`argc`设置成 3。

### 27 浮点类型的存储方式（引用自[小甲鱼](https://fishc.com.cn/thread-67214-1-1.html)有删减）

IEEE 浮点标准用V =  (-1)^S^ * M * 2^E^ 的形式来表示一个数。

组成部分依次是“符号 * 尾数 * 阶码”：

- 符号（sign）决定这个数是正数（s = 0）还是负数（s = 1）；
- 尾数（significand）决定这个数的精度，这里是用定点法表示；
- 阶码（exponent）决定这个数的范围，它是一个加权值，权重自然是 2 的 E 次幂。

C 语言中对 float 和 double 的封装格式：

![img](https://i.loli.net/2021/03/10/F6emlGXYLPTqsb4.png)

![img](https://i.loli.net/2021/03/10/97ARhSmTo5gZslQ.png)

float 的三个字段分别是分配 1 位，8 位和 23 位；而 double 则是分配 1 位，11 位和 52 位。

根据阶码（exponent）的值，上述编码还需要被解释成 4 种不同的情况（下边以 float 举例）：

**1. 规格化的值**

![img](https://i.loli.net/2021/03/10/fVnYhy8U1L7QmFi.png)

**2. 非规格化的值**

![img](https://i.loli.net/2021/03/10/RWmB6uinN3kgKDo.png)

**3. 无穷大**

![img](https://i.loli.net/2021/03/10/nihkquPUHjs8zGF.png)

**4. NaN（Not a Number，不是一个数）**

![img](https://i.loli.net/2021/03/10/fIEFVMXjl2wOQ97.png)

**1：规格化的值**

这是最普遍的情况，当 exp 字段不全为 0，也不全为 1 的时候，属于这种情况。

在这种情况下，阶码字段被解释为以偏置（biased）形式表示的有符号整数。

公式：**E = e - Bias**

其中，e 是一个无符号数，其值为 exp 字段表示的值；Bias 是一个等于2^k-1^ - 1（k 即阶码字段的位数，这里是8）的偏置值。

由此不难推导出指数 E 的取值范围是 -126 ~ 127（float）和 -1022 ~ 1023（double）。

对于 frac 字段则被解释为描述小数的值，如果我们把该值称之为 f，则 0 <= f < 1，而尾数被定义为 **M = 1 + f**

因此，在这种情况下，尾数的第一位总是 1。

**2：非规格化的值**

当 exp 字段全为 0 的时候，所表示的数就是非规格化形式。

在这种情况下，阶码的值是 **E = 1 - Bias**

而尾数被定义为 **M = f**

因此，在这种情况下，尾数即小数的值（第一位不是 1）。

非规格化数用于表示那些非常接近 0.0 的数。
**3. 无穷大**

当 exp 字段全为 1，且 frac 字段全为 0 的情况下，该浮点数表示无穷。

即 s = 0，结果为正无穷；s = 1，结果为负无穷。

当我们把两个非常大的数相乘，或者除以 0 的时候，无穷能够表示溢出的结果。
**4. NaN**

在第 3 种情况的基础上，frac 即小数域为非 0 时，我们把结果称之为 NaN，即 Not a Number（不是一个数）。

一些运算的结果不能是实数或无穷来表示，就会返回这样的 NaN 值。

### 28 do ... while ...语句

```c
// 注意：do…while 语句在 while 后边一定要用分号（;）表示语句结束。
do
{
    //循环体
}while ();
```

### 29 左值lvalue

C 语言的术语 lvalue 指用于**识别或定位一个存储位置**的标识符。（注意：左值同时还必须是**可改变的**）

rvalue 的发明完全是为了搭配 lvalue，rvalue 可以理解为 readable value，即任何可读取的值都被认为是右值（非左值）。

### 30 goto语句

**开发中要尽量避免使用 goto 语句。其实就连 C 语言的作者也觉得 goto 语句非常容易被滥用，并且建议一定要谨慎使用，甚至根本不用它。**

但在一种情况下使用 goto 语句是情有可原的，那就是当面临要跳出多层循环的时候，使用 goto 语句要比多个 break 语句好使。

### 31 字符串

c语言没有字符串数据类型，只有字符串常量如`"hello!"`和字符数组。

#### 字符串处理函数

获取字符串的长度：[strlen](http://bbs.fishc.com/thread-68741-1-1.html) 函数

拷贝字符串：[strcpy](http://bbs.fishc.com/thread-70518-1-1.html) 函数和 [strncpy](http://bbs.fishc.com/thread-70568-1-1.html) 函数

连接字符串：[strcat](http://bbs.fishc.com/thread-70566-1-1.html) 函数和 [strncat](http://bbs.fishc.com/thread-70569-1-1.html) 函数

比较字符串：[strcmp](http://bbs.fishc.com/thread-70567-1-1.html) 函数和 [strncmp](http://bbs.fishc.com/thread-70575-1-1.html) 函数

```c
int l;
char a[] = "hello world";
l = strlen(a);
char b[] = "Nice to meet you";
strcpy(a,b);
// 将b中的字符拷贝到a，这里会发生溢出
strncpy(a,b,5);
// 从b拷贝5个字符到a，这里不会发生溢出，但是a[5]不是\0所以会输出到word末尾
a[5] = '\0';
strcat(a,b);
strncat(a,b,5);
// 将b中的5个字符连接到a末尾，扩大了a的大小并在新字符串末尾添加\0
strcmp(a,b);
strncmp(a,b,5);
// 比较字符串a和b每个字符的ASCII码，a == b则返回0，a<b则返回小于0的值，a>b则返回大于0的值。
```

### 32 单链表

#### 1、链表的数据结构

#### 2、链表的操作

#### 2.1 增加元素

#### 2.2 读取链表

#### 2.3 搜索链表

#### 2.4 插入元素

#### 2.5 修改元素

#### 2.6 删除元素