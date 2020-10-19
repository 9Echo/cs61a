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

