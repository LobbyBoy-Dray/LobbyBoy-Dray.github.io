---
title: >
  Python | Pandas | Selection and Slicing
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-2
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

取值(select)和切片(slice)都涉及索引(index)。Series与DataFrame的行和列都可能具有双重索引，即**显式索引**和**隐式索引**。显式索引指通过创建对象时设置index/columns参数，或创建后修改对象的index/columns属性，显式指定的索引；隐式索引指从0到len-1的天然索引。当显式设置索引时，对象具有不同的双重索引；当未显式设置特定的索引时，对象具有相同的从0到len-1的双重索引。

双重索引可能使情况变得复杂。例如，当显式索引是不同于隐式索引的整数序列时，data\[1\]究竟是按显式索引取值还是按照隐式索引取值？为了避免上述情况，pandas提供了强大的索引器(indexer)进行取值与切片操作。因此，每部分首先介绍索引器版本的取值与切片，再介绍普通的取值与切片方法。

## Table of contents

## 1. Series的取值与切片

### 1.1 Selection：取值

#### 1) 索引器属性

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

#### 2) 常规方法

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

### 1.2 Slicing：切片

#### 1) 索引器属性

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

#### 2) 常规方法

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

## 2. DataFrame的取值与切片

### 2.1 Selection：取值

#### 1) 索引器属性

给索引器传入单个整数，取的是一行，而非一列：

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

#### 2) 常规方法

DataFrame的常规方法取值，只能用显式索引，隐式索引不起效果；且取的是一列，得到Series——即此时DataFrame被当做字典看待：

```python
# 数据准备
np.random.seed(0)
df2 = pd.DataFrame(np.random.randint(0,99,(3,4)),columns=[1,2,3,4], index=[1,2,3])
df2
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
df2[1]
```

    1    44
    2    67
    3    36
    Name: 1, dtype: int64

### 2.2 Slicing：切片

#### 1) 索引器属性

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
df1.loc[1:2]
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
  </tbody>
</table>
</div>

```python
df1.iloc[1:2]
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
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
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
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
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

#### 2) 常规方法

- 索引全是整数：按隐式切，显式失灵；
- 不全都是整数：显式和隐式都可以切。

```python
# 数据准备
df2 = df1.copy()
df2
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
df2[1:3]
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

## 3. 小结：关于取值和切片的常规方法

- **取值**：对于单个数据，我们一般会通过叫名字来获取，例如我们一般会说“我要height数据“、“我要weight数据”等等，而不会说”我要第3个数据“，”我要第5个数据“等等，所以索引在隐式和显式冲突的时候，**显式覆盖隐式**；
- **切片**：对于成批数据，我们一般需要连续的许多，例如我们一般会说“给我100个数据”、“我要第50个到第60个数据”等等，所以切片在隐式和显式冲突的时候，**隐式覆盖显式**；
- 但还是建议在代码中使用索引器(loc/iloc)进行取值和切片操作，明确地告诉程序自己使用显式索引还是隐式索引。
