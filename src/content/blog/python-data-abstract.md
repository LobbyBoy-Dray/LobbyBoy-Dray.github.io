---
title: Python | Abstractions with Data
author: Jingwei Gao
pubDatetime: 2019-02-21T09:25:48+08
postSlug: python-data-abstract
featured: false
draft: false
tags:
  - python
  - programming
description: >
  This is the 3rd note in the general Python tutorial series. It covers tree structure, iterators & generators, and OOP.
---

## Table of contents

## 1. Tree

### 1.1 Tree Representation

递归描述：

- tree = root label + branches
- branch = tree
- 没有branch的tree称为leaf

```python
# tree的本质[value, list, list]
# 第一个list是左枝，第二个list是右枝
# 例: [3, [1], [2, [1], [1]]]

# tree的constructor
def tree(label, branches=[]):
    for branch in branches:
        assert is_tree(branch)
    return [label] + list(branches)

# tree的selector: 获取tree的root label
def label(tree):
    return tree[0]

# tree的selector: 获取tree的branches
def branches(tree):
    return tree[1:]

def is_tree(tree):
    if type(tree) != list or len(tree) < 1:
        return False
    for branch in branches(tree):
        if not is_tree(branch):
            return False
    return True

def is_leaf(tree):
    return not branches(tree)
```

### 1.2 Tree Processing

```python
def leaves(tree):
    """Return a list containing the leaf labels of tree.

    >>> leaves(fib_tree(5)
    [1,0,1,0,1,1,0,1]
    """
    if is_leaf(tree):
        return [label(tree)]
    else:
        return sum([leaves(b) for b in branches(tress)], [])
```

```python
def count_paths(t, total):
    """
    Return the number of paths from the root to any node in tree t \
    for which the labels along the path sum to total.
    """
    if label(t) == total:
        found = 1
    else:
        found = 0
    return found + sum([count_paths(b, total-label(t)) for b in branches(t)])
```

## 2. Iterators

### 2.1 Iterable & Iterator

- **Def (iterable)**: Any value that can be passed to `iter` to produce an iterator.
- **Def (iterator)**: Returned from `iter` and can be passed to `next`. It is mutable.
  - iterator = iterable value + cursor
  - `next(iterator)`: return the next element in an iterator (i.e. 当前位置元素+光标右移)
  - iterator是“消耗型”的：末尾的时候使用next会抛出StopIteration异常。
- 对于字典，其keys、values、items均为iterable values，不是iterators。
- 许多built-in function返回iterators that compute results lazily:
  - `map(func, iterable)`: Iterate over func(x) for x in iterable.
  - `filter(func, iterable)`: Iterate over x in iterable if func(x).
  - `zip(first_iter, second_iter)`: Iterate over co-indexed (x,y) pairs.
  - `reversed(sequence)`: Iterate over x in a sequence in reverse order.

### 2.2 Generators

> The generator is a special kind of iterator.

**生成器函数：Generator Function**

- `yield` values rather than `return` values.
- A generator is an iterator created automatically by calling a generator function.

```python
>>> def plus_minus(x):
...	    yield x
...	    yield -x

>>> t = plus_minus(3)
>>> next(t)
3
>>> next(t)
-3
>>> t
<generator object plus_minus ...>
```

↑ 使用generator function生成generator的时候并没有执行generator function；直到next函数执行时，generator function才开始执行；并且运行直到遇见yield，然后「暂停(pause)」。

`yield from`: yields all values from an iterator or iterable value; 相当于for循环+yield:

```python
def countdown(k):
    if k > 0:
        yield k
        yield from countdown(k-1)

>>> list(countdown(5))
[5,4,3,2,1]

# 等价形式↓
def countdown(k):
    if k > 0:
        yield k
        for x in countdown(k-1):
            yield x
```

Generator Functions with Return Statements ↓ (Returned value is not yielded!)

```python
def f(x):
    yield x
    yield x+1
    return
    # 永远不会执行下面这句
    yield x+3
```

## 3. Class & Object

### 3.1 Methods

```python
class Account:

    # print(2+3)

    interest = 0.02    # A class attribute: Shared across all instances of a class.

    # self = an instance
    def __init__(self, account_holder):
        self.balance = 0
        self.holder = account_holder

    def deposit(self, amount):
        self.balance = self.balance + amount
        return self.balance

    def withdraw(self, amount):
        if amount > self.balance:
            return 'Insufficient funds'
        self.balance = self.balance - amount
        return self.balance

a = Account('Jim')
```

The suite(i.e. class body) is executed when the class statement is executed.

<div align="middle"><img src="/assets/py2-5_3.png"></div>

When a **class** is called:

- A new instance of that class is created;
- The `__init__` method of the class is called with the new object as its first argument (name `self`), along with any additional arguments provided in the call expression - `self`即为刚刚创建的new instance, `__init__`称为构造函数(constructor).

**Methods** are defined in the suite of a class statement:

- method的name没有bind在某个frame里 - bind as attributes of a class.
- method在调用时不用给self传对应的参数 - 调用该method的instance自动作为self的参数。
- Methods和Functions是有区别的：method全称为bound method, 包含一个function+会call它的object, 这样也就保证了该object在调用该function时不需要显性传入第一个argument:
  - `tom_account.deposit(1000)`: 这里的`tom_account.deposit`是一个method.
  - `Account.deposit(tom_account, 1000)`: 这里的`Account.deposit`是一个function.

### 3.2 Inheritance

搞懂这个例子就行了：特别注意在执行`C(1)`时调用构造函数后，其中的`self.f`指向哪个方法。

<div align="middle"><img src="/assets/py2-5_4.png"></div>
