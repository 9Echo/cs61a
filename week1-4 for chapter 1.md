## Python - CS 61a Fall 2020 

### 1.6 Higher-Order Functions

```python
from math import pi, sqrt

def area_square(r):
    assert r > 0, 'A length must be positive'
    return r * r

def area_circle(r):
    return r * r * pi

def area_hexagon(r):
    return r * r * 3 * sqrt(3) / 2
```

变形

```python
def area(r, shape_constant):
    assert r > 0, 'A length must be positive'
    return r * r * shape_constant

def area_square(r):
    return area(r, 1)

def area_circle(r):
    return area(r, pi)

def area_hexgan(r):
    return area(r, 3 * sqrt(3) / 2)
```

```python
def sum_naturals(n):
    """Sum the first N natural numbers"""
    total, k = 0, 1
    while k <= n:
        total, k = total + k, k + 1
    return total

def sum_cubes(n):
    total, k = 0, 1
    while k <= n:
        total, k = total + pow(k, 3), k + 1
    return total
```

变形

```python 
def identity(k):
    rturn k

def cube(k):
    return pow(k, 3)

def summation(n, term):
    total, k = 0, 1
    while k <= n:
        total, k = total + term(k), k + 1

def sum_naturals(n):
    """Sum the first N natural numbers"""
    return summation(n, identity)

def sum_cubes(n):
    return summation(n, cube)
```

---

#### Iterative Improvement

```python
def make_adder(n):
    """Return a function that takes one argument K and return K + N.
    >>> add_three = make_adder(3)
    >>> add_three(4)
    7
    """
    def adder(k):
        return k + N
    return adder

>>> make_adder(200)(2)
202
```
#### Currying

**Currying**: Transforming a multi-argument function into a single-argument, higher-order function.

```python
def make_adder(n):
    return lambda k: n + k

>>> make_adder(2)(3)
5
>>> add(2, 3)
5
```

```python
def curry2(f):
    def g(x):
        def h(y):
            return f(x, y)
        return h
    return g

>>> m = curry2(add)
>>> add_three = m(3)
>>> add_three(2)
5
>>> curry2 = lambda f: lambda x: lambda y: f(x, y)
>>> m = curry2(add)
>>> m(2)(3)
5
```

#### Lambda Expressions

最开始出现在 Lisp 语言中，一般用于表达简单重复的函数，如果过于复杂，代码可读性会很差，所以不采用。

```python
compose = lambda f,g: lambda x: f(g(x))
```

#### Function Decorators

装饰器

```python
def trace(fn):
    def wrapped(x):
        print('->', fn, '(', x, ')')
        return fn(x)
    return wrapped

@trace
def triple(x):
    return 3 * x
"""
@trace等同于
triple = trace(triple)
"""

triple(12)
-> <function triple at 0x102a39848> (12)
36
```

### 1.7 Recursive Function

递归和迭代的关系：

递归从结果到初始，迭代从初始到结果。

相互迭代，多个迭代函数

```python
def cascade(n):
    if n < 10:
        print(n)
    else:
        print(n)
        cascade(n//10)
        print(n)
>>> cascade(2013)
2013
201
20
2
20
201
2013
```
期望输出为：

```python
1
12
123
1234
123
12
1
```

对应代码为：


```python
def inverse_cascade(n):
    grow(n)
    print(n)
    shrink(n)
    
def f_then_g(f, g, n):
    if n:
        f(n)
        g(n)
grow = lambda n: f_then_g(grow, print, n//10)
shrink = lambda n: f_then_g(print, shrink, n//10)
```



---

### Chapter 2：Building Abstractions with Data

#### 2.1 基础介绍

> 所有的 value 都有他的 class，可以用 type 函数打印出结果.
>
> 其中最常使用的数字类型（numeric types）比如 int float complex
>
> 对于 float 类型这里需要注意精度问题
>
> 整数分割产生的是一个浮点近似，失去了整数的精确精度。

```python
>>> 1/3
0.3333333333333333

>>> 1/3 == 0.333333333333333312345  # Beware of float approximation
True
```

#### 2.2 抽象数据

> 之前提到的浮点数有精确缺失的问题，那计算过程中如何避免呢？可以将所有的浮点数写成有理数形式也就是分数（分子/分母）的形式，用分数的形式来进行四则运算。

```python
>>> def add_rationals(x, y):
    	nx, dx = numer(x), denom(x)
        ny, dy = numer(y), denom(y)
        return rational(nx * dy + ny * dx, dx * dy)

>>> def mul_rationals(x, y):
        return rational(numer(x) * numer(y), denom(x) * denom(y))

>>> def print_rational(x):
        print(numer(x), '/', denom(x))

>>> def rationals_are_equal(x, y):
        return numer(x) * denom(y) == numer(y) * denom(x)
```

> 通过上面设计的函数，就可以进行运算操作而不失精度，**下一问题是**如何从一个组合值中得到他的分子和分母。

这里介绍的 python 的 list（列表）

> 通过 list 可以获得分子分母的值

```python
>>> def rational(n, d):
        return [n, d]
>>> def numer(x):
        return x[0]
>>> def denom(x):
        return x[1]
```

**抽象障碍的问题**

为了使程序**更加 robust** ，也就是说当外部条件发生改变，可以尽可能少的改动程序内部结构，在设计函数的时候，注意**层次感**

比如这里想要**实现有理数的平方**有以下三种方法，其中第一种方法是更 robust 的设计。

```python
# 通过mul_rational实现
>>> def square_rational(x):
    	return mul_rational(x, x)

# 间接通过numer()和denom()实现
>>> def square_rational_violating_once(x):
    	return rational(numer(x) * numer(x), denom(x) * denom(x))

# 直接通过列表x实现    
>>> def square_rational_violating_twice(x):
    	return [x[0] * x[0], x[1] * x[1]]
```

**数据的特性**

从数据的性质上来考虑，我们需要做的是从一个分数上取出分子和分母这一过程，将这一过程使用高阶函数的方法来实现的话，可以设计如下的函数，其中设计一个pair，即表示分子和分母，select(pair, 0) 和 select(pair, 1)即表示分子和分母。

```python
def pair(x, y):
    def get(index):
        if index == 0:
            return x
        elif index == 1:
            return y
     return get

def select(p, i):
    return p(i)
```

利用设计好的函数，生成并实现pair

```python
>>> p = pair(20, 14)
>>> select(p, 0)
20
>>> select(p, 1)
14
```

> 从这个角度来讲利用高阶函数的性质可以更好的理解数据的性质，但是却比列表形式的实现方式更难理解。



#### 序列

 序列并不是某一种特定的类别的实例，而是不同种类数据的集合，序列有很多种，但是基本操作大致两种：**求长度和选元素**。

**Lists**

python中自带的序列

```python
>>> digits = [1, 8, 2, 8]
>>> len(digits)
4
>>> digits(3)
8

>>> [2, 7] + digits * 2
[2, 7, 1, 8, 2, 8, 1, 8, 2, 8]

>>> pairs = [[10, 20], [30, 40]]
>>> pairs[1]
[30, 40]
>>> pairs[1][0]
30
```

**Tree**

把数组抽象为 **树** 的结构。

这一部分讲 树 的结构没有很懂，叶子节点和子树（分支）的定义也没有很好的理解。

http://composingprograms.com/pages/23-sequences.html

**Linked list**

列表同树的结构，这里使用数组的形式展示链表的基本结构，是很原生态的形式（和python内部实现的链表结构/类，不同，这里的结构等于是自己手写的结构实现）

在手动实现的链表结构的同时，同样可以（手动）实现返回链表长度的函数和根据下标即可定位链表对应数值的函数（查找）





**爬楼梯问题**：

一共有 $n$ 层楼梯，每一次可以上一层或者两层，问爬完 $n$ 层有多少种爬法。

> 典型的递归问题。递归可以有两种写法，第一种写法更简洁。

```python
def count_stair_ways(n):
    if n == 0:
        return 1
    elif n < 0:
        return 0
    return count_stair_ways(n-1) + count_stair_ways(n-2)
```

```python
def count_stair_way(n):
    total = 0
    if n == 2:
        reutrn 2
    elif n == 1:
        return 1
    elif n > 2:
        total = count_stair_way(n-1) + count_stair_way(n-2)
    	return total
```

**升级版爬楼梯问题：**

一共有 $n$ 层楼梯，每一次可以爬不超过 $k$ 步，问有多少种爬法。

> 依然是递归问题，但是递推的公式发生了变化，这里每一次爬的层数从一个固定的值变成了一个从1到 $k$ 的不确定的整数，其实这个整数并不影响递推。

以（3， 3）为例，（3，3）=（2，3）+（1，3）+（0，3）因为是 $k=3$ ，也就是一次爬上的层数有 $3$ 种可能，即递推公式为 $(n, k) = (n-1, k) + (n-2, k) + \cdots + (n-k, k)$. 递归外部条件和基础版爬楼梯问题一样。

```python
def count_k(n, k):
    if n == 0:
        return 1
    elif n < 0:
        return 0
    else:
        total = 0
        i = 1
        while i <= k:
            total += count_k(n-i, k)
            i += 1
        return total
```



在一个数组中，找出不相邻的数相乘可得的最大积.

```python
def max_product(s):
    """Return the maximum product that can be formed using non-consecutiveelements of s.
    >>> max_product([10,3,1,9,2]) # 10 * 9
    90
    >>> max_product([5,10,5,10,5]) # 5 * 5 * 5
    125
    >>> max_product([])
    1
    """
    if s == []:
        return 1
    elif len(s) == 1: # Base case optional
        return s[0]
    else:
        return max(max_product(s[1:]), s[0] * max_product(s[2:]))
```



### 2.4 Mutable Data

#### 1. 何为面向对象

对象（object） 包含了数据（data）和数据的行为（behavior），对象包含属性、方法

All values in Python are objects. That is, all values have behavior and attributes. They act like the values they represent.







