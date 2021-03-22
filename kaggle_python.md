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

### 练习

- 置换变量`a,b = b,a`

## 2 函数和帮助信息

### 1.1 帮助信息

- 使用`help(abs)`可以获得内置函数`abs()`的帮助信息
- 对于自定义函数，通过在函数名后面添加注释设置帮助信息

```
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

>>> print(
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

### 练习

- `pass`是一个没有意义的语句，只作占位用
- `round(3.1415, 2)`四舍五入，保留小数点后两位
- `round(1234.1, -2)`输出`1200.0`四舍五入，精确到小数点前第三位

## 3 布尔类型和条件语句

### 3.1 布尔类型

- 逻辑运算符和比较运算符的值为布尔类型`true`or`false`
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

### 练习

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