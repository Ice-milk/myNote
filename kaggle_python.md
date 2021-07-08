# Kaggle学习笔记1：Python

## 0 Kaggle学习的基本操作

- 课程页面的代码可以点击Output运行查看结果
- 练习页面的代码通过ctrl+enter运行，a键在代码块上方添加块，b键在代码块下方添加块
- 执行练习之前需要导入练习所需的库函数
- 对于第0题，通过`q0.check()`检查答案，`q0.hint()`获得提示，`q0.solution()`获得答案
- 第1题相应使用`q1.check()` ,`q1.hint`,`q1.solution()`

## 1 Hello, Python

### 1.1 例子引入

1 变量赋值

- Python变量不需要提前声明，可以直接赋值
- Python变量不需要指定数据类型，赋值时变量会自动适应数据类型。赋值后的变量甚至可以再赋值其他的数据类型的值。

2 函数调用

3 注释#

4 通过`:`和缩进区分代码块

5 使用`""`或`''`包围字符串，二者相同，但通常只使用其中一个以免混乱

6 运算符重载

- 在使用`*`、`+`等运算符过程中，对于数字是执行数学运算，对于字符串是复制和连接，这就叫运算符重载。

### 1.2 数字和算术运算符

1 使用`type()`查看数据类型

- int， float

2 算术运算符

- `/`与`//`

### 1.3 算术运算符的优先级

**PEMDAS** - **P**arentheses, **E**xponents, **M**ultiplication/**D**ivision, **A**ddition/**S**ubtraction.

括号，指数，乘除，加减

### 1.4 处理数字的内置函数

- `min()`与`max()`
- `abs()`
- 数据类型名称可以进行强制数据类型转换，如：`float(10)`

### 练习1

- 置换变量`a,b = b,a`

## 2 函数和帮助信息

### 1.1 帮助信息

- 使用`help(abs)`可以获得内置函数`abs()`的帮助信息
- 对于自定义函数，通过在函数名后面添加注释设置帮助信息

```shell
>>> help(round)
Help on built-in function round in module builtins:

round(number, ndigits=None)
    Round a number to a given precision in decimal digits.
    
    The return value is an integer if ndigits is omitted or None.  Otherwise
    the return value has the same type as the number.  ndigits may be negative.
```

```python
def maxof2(a, b):
    """
    返回a,b中的最大值
    
    >>> maxof2(1,3)
    3
    """
    if a<b :
        a = b
    return a

>>> help(maxof2)
Help on function maxof2 in module __main__:

maxof2(a, b)
    返回a,b中的最大值
    
    >>> maxof2(1,3)
    3

```

### 1.2 自定义函数

```python
def 函数名(参数表, ...,...):
    """
    帮助信息
    """
    函数体
    return 返回值
```

函数可以没有返回值，例如`print()`函数就没有返回值，它只打印内容。

### 1.3 默认参数值

```python
def greet(who="Colin"):
    print("Hello,", who)
# 默认参数为Colin    
>>> greet()
Hello, Colin
>>> greet(who="Kaggle")
Hello, Kaggle
>>> greet("world")
Hello, world
```

### 1.4 函数作为参数

```python
def mult_by_five(x):
    return 5 * x

def call(fn, arg):
    """Call fn on arg"""
    return fn(arg)

def squared_call(fn, arg):
    """Call fn on the result of calling fn on arg"""
    return fn(fn(arg))

>>>
print(
    call(mult_by_five, 1),
    squared_call(mult_by_five, 1), 
    sep='\n',
)
5
25
```

应用

```python
def mod_5(x):
    """Return the remainder of x after dividing by 5"""
    return x % 5

print(
    'Which number is biggest?',
    max(100, 51, 14),
    'Which number is the biggest modulo 5?',
    max(100, 51, 14, key=mod_5),
    sep='\n',
)

```

```bash
Which number is biggest?
100
Which number is the biggest modulo 5?
14
```

### 练习2

- `pass`是一个没有意义的语句，只作占位用
- `round(3.1415, 2)`四舍五入，保留小数点后两位
- `round(1234.1, -2)`输出`1200.0`四舍五入，精确到小数点前第三位

## 3 布尔类型和条件语句

### 3.1 布尔类型

- 逻辑运算符和比较运算符的返回值为布尔类型`true`or`false`
- 整数和浮点数可以比较`3==3.0`，返回true
- 但是字符不可以，字符事实上也是存储的整数，对应的是ascII码，所以`3=='3'`返回false

- Python中的逻辑运算符是`and,or,not`，优先级是not>and>or
- 比较运算符优先级高于逻辑运算符
- 建议多使用**括号**来使含有较多运算符的表达式更有可读性

```python
bad:
prepared_for_weather = have_umbrella or rain_level < 5 and have_hood or not rain_level > 0 and is_workday

good:
prepared_for_weather = (
    have_umbrella 
    or ((rain_level < 5) and have_hood) 
    or (not (rain_level > 0 and is_workday))
)
```

### 3.2 条件语句

注意冒号与缩进

### 练习3

- 条件语句中可以有多个return语句

  - ```python
    def sign(a):
        if a>0:
            return 1
        elif a<0:
            return -1
        else:
            return 0
    ```

- ```python
  print("Splitting", total_candies, "candy" if total_candies == 1 else "candies")
  ```

## 4 列表Lists

- 列表的特点
  - 有序，能够索引
  - 元素不需要相同数据类型
  - 元素可以是任何数据类型
  - 元素不必唯一
  - 元素可以修改，增删
- 列表的索引
- 切片
  - 左闭右开
  - 步长为负表示从末尾往前取
- 函数
  - len()
  - sorted()
  - sum()
  - max()
- 方法
  - list.append()
  - list.pop()
  - list.index()

- 元组tuple
  - 元素不可改变
  - 常用作函数返回值

### 练习4

`math.ceil()`向上取整

## 5 循环和列表推导式

- for ... in ...:
- while ...:

- 列表推导式
  - 生成0-9的列表
  - list1 = [i for i in range(10)]

### 习题5

`any([num % 7 == 0 for num in nums])`

any()当list中存在值，返回True，否则返回False

## 6 字符串和字典

### 6.1 字符串strings

### 6.2 字典dictionary

字典也有类似列表的推导式

`{key:dic1[key] for key in dict1}`复制dict1

```python
for a,b in dict.items():
    print("{}:{}".format(a,b))
```

### 练习6

```python
def word_search(doc_list, keyword):
    """
    Takes a list of documents (each document is a string) and a keyword. 
    Returns list of the index values into the original list for all documents 
    containing the keyword.

    Example:
    doc_list = ["The Learn Python Challenge Casino.", "They bought a car", "Casinoville"]
    >>> word_search(doc_list, 'casino')
    >>> [0]
    """
    search_result = []
    for i,article in enumerate(doc_list): # 同时遍历索引号和列表内容
        words = article.split(' ')
        words1 = [word.strip(',.').lower() for word in words]
        if keyword.lower() in words1:
            search_result.append(i) 
    return search_result
```

```python
def multi_word_search(doc_list, keywords):
    """
    Takes list of documents (each document is a string) and a list of keywords.  
    Returns a dictionary where each key is a keyword, and the value is a list of indices
    (from doc_list) of the documents containing that keyword

    >>> doc_list = ["The Learn Python Challenge Casino.", "They bought a car and a casino", "Casinoville"]
    >>> keywords = ['casino', 'they']
    >>> multi_word_search(doc_list, keywords)
    {'casino': [0, 1], 'they': [1]}
    """
    return {keywords[i]: word_search(doc_list, keywords[i]) for i in range(len(keywords))}
```

## 7 使用外部库

### 7.1 import

查看一个库内的命名空间用`dir()`例如：
```python
print(dir(math))

['__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp', 'fsum', 'gamma', 'gcd', 'hypot', 'inf', 'isclose', 'isfinite', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'log2', 'modf', 'nan', 'pi', 'pow', 'radians', 'remainder', 'sin', 'sinh', 'sqrt', 'tan', 'tanh', 'tau', 'trunc']
```

**python标准库：**

`os`(执行系统命令)、`shutil`(复制、移动文件)、`glob`(文件通配符)、`sys`(读取命令等)、`re`(正则表达式)、`math`、`random`、`urllib.request`(读取网页)、`datetime`、`zlib`(数据压缩)、`timeit.Timer`(性能度量)等等。

### 7.2 如何快速熟悉一个陌生的库

理解陌生库的三大法宝：

1. type()（帮助你知道是什么）
2. dir()（能干什么）
3. help()（详细了解）

### 7.3 关于运算符重载

同一个运算符，根据不同的对象，会表示不同的意义，好处是使语言变得更简洁，技术实现是类似`__contain__`这样的函数去替换`in`运算符实现列表的包含判断功能。

### 练习7

1. 探索`matplotlib`
2. 改错，提醒注意变量命名尽量意义明确，尽量不重复用，以免互相干扰。
3. 统计计分常用的思路不是直接转换列表元素为具体分值，而是用total，scorebord计分板这样的单独计分，然后计算。例如深度学习在计算准确率时使用计分板来记录预测正确与否，然后可以直接求和得到预测正确的数量。
