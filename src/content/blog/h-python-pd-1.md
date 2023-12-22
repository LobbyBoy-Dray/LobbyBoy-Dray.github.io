---
title: >
  Python | Pandas | Pandas Objects
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-1
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

NumPy擅长：

- 处理干净整洁、组织良好的数据；
- 对上述数据进行数值计算。

NumPy不擅长：

- 灵活的数据任务，如为数据添加标签、处理缺失值；
- 不是对每个元素进行广播映射的计算，如分组、透视表。

Pandas，建立在NumPy数组结构上，十分擅长**数据清理(data munging)**。标准导入：

```python
import numpy as np
import pandas as pd
```

## Table of contents

## 1. Series对象

### 1.1 简介

简单来看，Series就是带有“索引数据”的“一维数组”(类似于字典)：

```python
# 可以直接传入列表来创建Seires对象
# 默认索引（隐式索引）从0开始，到len-1
pd.Series([13,42,89,57])
```

    0    13
    1    42
    2    89
    3    57
    dtype: int64

Series对象有两个重要的属性：

- `.values`：值，类型是“数组(ndarray)”；
- `.index`：索引，类型是“索引对象(Index)”。

```python
data = pd.Series([13,42,89,57])
print(type(data.values))
print(data.values)
print(type(data.index))
print(data.index)
```

    <class 'numpy.ndarray'>
    [13 42 89 57]
    <class 'pandas.core.indexes.range.RangeIndex'>
    RangeIndex(start=0, stop=4, step=1)

Series的索引不仅可是整数，还可是任意想要的类型——在创建Series时指定index为特定的参数即可：

```python
# 显式索引
pd.Series([0.24,1.32,4.31,8.64], index=['a','b','c','d'])
```

    a    0.24
    b    1.32
    c    4.31
    d    8.64
    dtype: float64

可以从Series与数组、字典的对比中理解其特点：

- **Series和数组的区别**：本质差异是“索引”。数组的index是被动、默认定义的（即我们自己并不用也不能写index=...来指定特定的索引），从0开始到len-1；Series的index则可以主动定义为任意类型；如果没有主动定义，则默认和数组一样（被动定义），从0开始直到len-1。
- **Series和字典的区别**：可以将Series看做特殊的字典，其index相当于key，其value相当于字典的value，“key-value对”就相当于“index-value对”；但字典是无序的，Series是有序的，这一点Series又像是数组，因此可以切片。

### 1.2 创建

创建Series的方法：

- 完整语法：`pd.Series(data, index=index)`
- 关于data:
  - 可以是列表、数组等序列；
  - 可以是标量，这个标量会重复填充到每个索引上；
  - 可以是字典，此时index默认是排序的字典key；
- index是可选参数。如果不填，这时index默认是从0开始的整数序列；
- 每一种创建形式都可以通过显式指定索引，筛选需要的结果。

```python
# 传入一个标量
pd.Series(5, index=['a','b','c','d'])
```

    a    5
    b    5
    c    5
    d    5
    dtype: int64

```python
# 传入一个字典
pd.Series({'a':1,'b':2,'c':3,'d':4})
```

    a    1
    b    2
    c    3
    d    4
    dtype: int64

```python
# 根据主动传入的index进行筛选
pd.Series({'a':1,'b':2,'c':3,'d':4}, index=['b','d','f'])
```

    b    2.0
    d    4.0
    f    NaN
    dtype: float64

## 2. DataFrame对象

### 2.1 简介

第一，DataFrame可被看做有序排列的若干具有相同索引的Series——下例中的df由两个Series构成：

```python
weight = pd.Series([64,73,66,81], index=['Sam','Bill','Lucy','Eric'])
height = pd.Series([178,182,175,187], index=['Sam','Bill','Lucy','Eric'])
df = pd.DataFrame({'weight':weight, 'height':height})
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>weight</th>
      <th>height</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Sam</th>
      <td>64</td>
      <td>178</td>
    </tr>
    <tr>
      <th>Bill</th>
      <td>73</td>
      <td>182</td>
    </tr>
    <tr>
      <th>Lucy</th>
      <td>66</td>
      <td>175</td>
    </tr>
    <tr>
      <th>Eric</th>
      <td>81</td>
      <td>187</td>
    </tr>
  </tbody>
</table>
</div>

第二，DataFrame可以看做既有行索引（index）也有列索引（columns）的二维数组：

```python
df.index
```

    Index(['Sam', 'Bill', 'Lucy', 'Eric'], dtype='object')

```python
df.columns
```

    Index(['weight', 'height'], dtype='object')

第三，DataFrame还可以看做字典，字典的key是“列名”，字典的value是“那一列的Series”：

```python
df['weight']
```

    Sam     64
    Bill    73
    Lucy    66
    Eric    81
    Name: weight, dtype: int64

```python
df['height']
```

    Sam     178
    Bill    182
    Lucy    175
    Eric    187
    Name: height, dtype: int64

一些操作：

- 变为二位数组；
- 转置；

```python
# 转换为二维数组
df.values
```

    array([[ 64, 178],
           [ 73, 182],
           [ 66, 175],
           [ 81, 187]])

```python
# 转置
df.T
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>Sam</th>
      <th>Bill</th>
      <th>Lucy</th>
      <th>Eric</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>weight</th>
      <td>64</td>
      <td>73</td>
      <td>66</td>
      <td>81</td>
    </tr>
    <tr>
      <th>height</th>
      <td>178</td>
      <td>182</td>
      <td>175</td>
      <td>187</td>
    </tr>
  </tbody>
</table>
</div>

### 2.2 创建

**①字典形式**: 每一列

```python
# 传入一个字典，每个键值对的key是列名，value是Series
weight = pd.Series([64,73,66,81], index=['Sam','Bill','Lucy','Eric'])
height = pd.Series([178,182,175,187], index=['Sam','Bill','Lucy','Eric'])
df = pd.DataFrame({'weight':weight, 'height':height})
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>weight</th>
      <th>height</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Sam</th>
      <td>64</td>
      <td>178</td>
    </tr>
    <tr>
      <th>Bill</th>
      <td>73</td>
      <td>182</td>
    </tr>
    <tr>
      <th>Lucy</th>
      <td>66</td>
      <td>175</td>
    </tr>
    <tr>
      <th>Eric</th>
      <td>81</td>
      <td>187</td>
    </tr>
  </tbody>
</table>
</div>

**②列表形式**: 每一行

```python
# 一个字典是一行数据
df = pd.DataFrame([{'a':1, 'b':2}, {'b':2, 'c':3}])
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>2</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
</div>

**③二维数组形式**: 整个矩阵

```python
pd.DataFrame(np.random.randint(0,99,(3,4)), index=[1,2,3], columns=['one','two', 'three', 'four'])
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>one</th>
      <th>two</th>
      <th>three</th>
      <th>four</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>47</td>
      <td>86</td>
      <td>83</td>
      <td>92</td>
    </tr>
    <tr>
      <th>2</th>
      <td>77</td>
      <td>24</td>
      <td>61</td>
      <td>28</td>
    </tr>
    <tr>
      <th>3</th>
      <td>63</td>
      <td>88</td>
      <td>79</td>
      <td>46</td>
    </tr>
  </tbody>
</table>
</div>

index或columns若不显式指定，都从0开始标注（隐式索引）；可以使用index和columns参数指定。

### 2.3 索引筛选

```python
weight = pd.Series([64,73,66,81], index=['Sam','Bill','Lucy','Eric'])
height = pd.Series([178,182,175,187], index=['Sam','Bill','Lucy','Eric'])
df = pd.DataFrame({'weight':weight, 'height':height}, columns=['weight','age'])
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>weight</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Sam</th>
      <td>64</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Bill</th>
      <td>73</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Lucy</th>
      <td>66</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Eric</th>
      <td>81</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

## 3. Index对象

- index对象类似**不可变数组**，可以通过python标准方法取值、切片，但不能就地修改；
- index对象类似**允许重复元素的有序集合**，遵循python标准库的集合数据结构的许多习惯用法，如交集、并集、差集等。
