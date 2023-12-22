---
title: >
  Python | Pandas
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: python-pd
featured: false
draft: false
tags:
  - python
  - programming
description: >
  ...
---

## Table of contents

## 1. Pandas Objects

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

### 1.1 Series对象

#### 1) 简介

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

#### 2) 创建

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

### 1.2 DataFrame对象

#### 1) 简介

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

#### 2) 创建

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

#### 3) 索引筛选

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

### 1.3 Index对象

- index对象类似**不可变数组**，可以通过python标准方法取值、切片，但不能就地修改；
- index对象类似**允许重复元素的有序集合**，遵循python标准库的集合数据结构的许多习惯用法，如交集、并集、差集等。

## 2. Selection and Slicing

取值(select)和切片(slice)都涉及索引(index)。Series与DataFrame的行和列都可能具有双重索引，即**显式索引**和**隐式索引**。显式索引指通过创建对象时设置index/columns参数，或创建后修改对象的index/columns属性，显式指定的索引；隐式索引指从0到len-1的天然索引。当显式设置索引时，对象具有不同的双重索引；当未显式设置特定的索引时，对象具有相同的从0到len-1的双重索引。

双重索引可能使情况变得复杂。例如，当显式索引是不同于隐式索引的整数序列时，data\[1\]究竟是按显式索引取值还是按照隐式索引取值？为了避免上述情况，pandas提供了强大的索引器(indexer)进行取值与切片操作。因此，每部分首先介绍索引器版本的取值与切片，再介绍普通的取值与切片方法。

### 2.1 Series的取值与切片

#### 1) Selection：取值

**①索引器属性**

整数索引容易造成混淆，因此pandas提供了“规则分明”的索引器属性作为取值（或切片）的方法：

- loc属性：表示取值和切片都遵照显式索引；
- iloc属性：表示取值和切片都遵照隐式索引，integer-location；

```python
# 数据准备
data = pd.Series(list('abcde'), index = [1,2,3,4,5])
data
```

    1    a
    2    b
    3    c
    4    d
    5    e
    dtype: object

```python
# loc：显式索引
data.loc[1]
```

    'a'

```python
# iloc：隐式索引
data.iloc[1]
```

    'b'

**②常规方法**

```python
# 数据准备
data2 = pd.Series([3.13,9.43,7.63,2.18], index = ['a','b','c','d'])
data2
```

    a    3.13
    b    9.43
    c    7.63
    d    2.18
    dtype: float64

```python
# 用显式索引取值
print(data2['a'])
print(data2['b'])
print(data2['c'])
```

    3.13
    9.43
    7.63

```python
# 用隐式索引取值
print(data2[0])
print(data2[1])
print(data2[-1])
```

    3.13
    9.43
    2.18

上述例子中，因为显式索引的元素类型是字符串，而不是整数，所以不会产生混淆——方括号中是字符串，pandas知道你是在用显式索引；方括号中是整数，pandas知道你是在用隐式索引。但下例：

```python
# 数据准备
data3 = pd.Series([11,22,33,44], index = [1,2,3,4])
data3
```

    1    11
    2    22
    3    33
    4    44
    dtype: int64

`data3[0]`，此时pandas知道你是在用隐式索引吗？不，当显式索引的元素全部为整数类型时，pandas无法区分隐式索引与显式索引。此时显式将会把隐式覆盖，因此此例中`data3[0]`将报错。

```python
# 报错 - KeyError: 0
# data3[0]
```

总之，使用常规方法取值时，pandas会首先判断一下你的Series的显式索引的类型：

- 如果全都是整数类型，则只能用显式索引，隐式索引失效；
- 否则，显式索引和隐式索引都能使用。

#### 2) Slicing：切片

**①索引器属性**

- loc：遵照显式索引切片，前闭后闭；
- iloc：遵照隐式索引切片，前闭后开。

```python
# 数据准备
data1 = pd.Series([3.13,9.43,7.63,2.18], index = ['a','b','c','d'])
data1
```

    a    3.13
    b    9.43
    c    7.63
    d    2.18
    dtype: float64

```python
data2 = pd.Series([11,22,33,44], index = [1,2,3,4])
data2
```

    1    11
    2    22
    3    33
    4    44
    dtype: int64

```python
print(data1.loc['a':'c'])
print("="*20)
print(data1.iloc[0:2])
```

    a    3.13
    b    9.43
    c    7.63
    dtype: float64
    ====================
    a    3.13
    b    9.43
    dtype: float64

```python
print(data2.loc[1:3])
print("="*20)
print(data2.iloc[1:3])
```

    1    11
    2    22
    3    33
    dtype: int64
    ====================
    2    22
    3    33
    dtype: int64

**②常规方法**

使用常规方法切片时，pandas会首先判断一下你的Series的显式索引的类型：

- 如果全是整数，则只能用隐式索引，显式索引失效；
- 否则，显式索引和隐式索引都能用，但是有区别：
  - 使用显示索引切片，前闭后闭；
  - 使用隐式索引切片，前闭后开。

```python
# 数据准备
data3 = pd.Series([3.13,9.43,7.63,2.18], index = ['a','b','c','d'])
data3
```

    a    3.13
    b    9.43
    c    7.63
    d    2.18
    dtype: float64

```python
# 隐式索引，前闭后开
data3[1:3]
```

    b    9.43
    c    7.63
    dtype: float64

```python
# 显示索引，前闭后闭
data3['a':'c']
```

    a    3.13
    b    9.43
    c    7.63
    dtype: float64

```python
# 数据准备——索引为全整数
data4 = pd.Series([11,22,33,44], index = [1,2,3,4])
data4
```

    1    11
    2    22
    3    33
    4    44
    dtype: int64

```python
# 只能是隐式索引
print(data4[1:3])
print("="*20)
print(data4[0:3])
```

    2    22
    3    33
    dtype: int64
    ====================
    1    11
    2    22
    3    33
    dtype: int64

### 2.2 DataFrame的取值与切片

#### 1) Selection：取值

**①索引器属性**——给索引器传入单个整数，取的是一行，而非一列：

```python
# 数据准备
np.random.seed(0)
df1 = pd.DataFrame(np.random.randint(0,99,(3,4)),columns=[1,2,3,4], index = [1,2,3])
df1
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>44</td>
      <td>47</td>
      <td>64</td>
      <td>67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>36</td>
      <td>87</td>
      <td>70</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
df1.loc[1]
```

    1    44
    2    47
    3    64
    4    67
    Name: 1, dtype: int64

```python
df1.iloc[1]
```

    1    67
    2     9
    3    83
    4    21
    Name: 2, dtype: int64

```python
# 取第2列
df1.loc[:, 2]
```

    1    47
    2     9
    3    87
    Name: 2, dtype: int64

```python
# 取第2列
df1.iloc[:, 1]
```

    1    47
    2     9
    3    87
    Name: 2, dtype: int64

②常规方法

DataFrame的常规方法取值，只能用显式索引，隐式索引不起效果；且取的是一列，得到Series——即此时DataFrame被当做字典看待：

```python
# 数据准备
np.random.seed(0)
df2 = pd.DataFrame(np.random.randint(0,99,(3,4)),columns=[1,2,3,4], index=[1,2,3])
df2
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>44</td>
      <td>47</td>
      <td>64</td>
      <td>67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>36</td>
      <td>87</td>
      <td>70</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
df2[1]
```

    1    44
    2    67
    3    36
    Name: 1, dtype: int64

#### 2) Slicing：切片

1）索引器属性

```python
# 数据准备
np.random.seed(0)
df1 = pd.DataFrame(np.random.randint(0,99,(3,4)),columns=[1,2,3,4], index = [1,2,3])
df1
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>44</td>
      <td>47</td>
      <td>64</td>
      <td>67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>36</td>
      <td>87</td>
      <td>70</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
df1.loc[1:2]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>44</td>
      <td>47</td>
      <td>64</td>
      <td>67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
</div>

```python
df1.iloc[1:2]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
</div>

多维度切片：

```python
df1.loc[1:2,1:2]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>44</td>
      <td>47</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

```python
df1.iloc[1:2,1:2]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

`ix`索引器属性可以实现混合效果，即在一次切片中既使用显式也使用隐式索引，但正因为这样也容易让人混淆。

#### 2）常规方法

- 索引全是整数：按隐式切，显式失灵；
- 不全都是整数：显式和隐式都可以切。

```python
# 数据准备
df2 = df1.copy()
df2
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>44</td>
      <td>47</td>
      <td>64</td>
      <td>67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>36</td>
      <td>87</td>
      <td>70</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
df2[1:3]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>67</td>
      <td>9</td>
      <td>83</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>36</td>
      <td>87</td>
      <td>70</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

## 三. 小结：关于取值和切片的常规方法

- **取值**：对于单个数据，我们一般会通过叫名字来获取，例如我们一般会说“我要height数据“、“我要weight数据”等等，而不会说”我要第3个数据“，”我要第5个数据“等等，所以索引在隐式和显式冲突的时候，**显式覆盖隐式**；
- **切片**：对于成批数据，我们一般需要连续的许多，例如我们一般会说“给我100个数据”、“我要第50个到第60个数据”等等，所以切片在隐式和显式冲突的时候，**隐式覆盖显式**；
- 但还是建议在代码中使用索引器(loc/iloc)进行取值和切片操作，明确地告诉程序自己使用显式索引还是隐式索引。

## 3. Operating on Data

## 4. Missing Data

## 5. Hierarchical Indexing

## 6. Concat & Merge

## 7. Aggregation & Grouping
