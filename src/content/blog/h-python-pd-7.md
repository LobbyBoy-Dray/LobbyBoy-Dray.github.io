---
title: >
  Python | Pandas | Aggregation & Grouping
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-7
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

- Aggregation：分析大数据时，常常需要进行有效的数据累计(summarization)，得到相应的累计指标(aggregation)，例如`sum()`，`mean()`，`min()`等；
- Grouping：基于`groupby`函数实现对数据集的按需分组。

注意，虽然累计也有单独使用的场景（前几节介绍的内容），但更重要的用法是配合分组进行各组内部的累计——可以说aggregation和grouping是一对孪生兄弟。

使用Seaborn库中的一份行星数据：

```python
# 导入seaborn库，获取该数据集，打印形状
import seaborn as sns
planets = sns.load_dataset('planets')
planets.shape
```

    (1035, 6)

```python
# 查看前5行数据
planets.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>method</th>
      <th>number</th>
      <th>orbital_period</th>
      <th>mass</th>
      <th>distance</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Radial Velocity</td>
      <td>1</td>
      <td>269.300</td>
      <td>7.10</td>
      <td>77.40</td>
      <td>2006</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Radial Velocity</td>
      <td>1</td>
      <td>874.774</td>
      <td>2.21</td>
      <td>56.95</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Radial Velocity</td>
      <td>1</td>
      <td>763.000</td>
      <td>2.60</td>
      <td>19.84</td>
      <td>2011</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Radial Velocity</td>
      <td>1</td>
      <td>326.030</td>
      <td>19.40</td>
      <td>110.62</td>
      <td>2007</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Radial Velocity</td>
      <td>1</td>
      <td>516.220</td>
      <td>10.50</td>
      <td>119.47</td>
      <td>2009</td>
    </tr>
  </tbody>
</table>
</div>

## Table of contents

## 1. 回顾：pandas的简单Aggregate功能

### 1.1 Series累计

```python
ser = pd.Series(np.arange(10,16))
ser
```

    0    10
    1    11
    2    12
    3    13
    4    14
    5    15
    dtype: int64

```python
# 求和
ser.sum()
```

    75

```python
# 求均值
ser.mean()
```

    12.5

### 1.2 Dataframe累计

Dataframe默认对**每列**进行累计，即默认`axis=0`；可以修改`axis=1`使累计在**每行**进行。

```python
df = pd.DataFrame({'A':np.arange(5,10),
                  'B':np.arange(10,15)})
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>A</th>
      <th>B</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6</td>
      <td>11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7</td>
      <td>12</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8</td>
      <td>13</td>
    </tr>
    <tr>
      <th>4</th>
      <td>9</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 默认对列
df.sum()
```

    A    35
    B    60
    dtype: int64

```python
# 修改为行
df.sum(axis=1)
```

    0    15
    1    17
    2    19
    3    21
    4    23
    dtype: int64

### 1.3 describe常用统计值

_describe()_ 函数可以计算每一列的若干常用统计值。

```python
planets.describe()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>number</th>
      <th>orbital_period</th>
      <th>mass</th>
      <th>distance</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1035.000000</td>
      <td>992.000000</td>
      <td>513.000000</td>
      <td>808.000000</td>
      <td>1035.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1.785507</td>
      <td>2002.917596</td>
      <td>2.638161</td>
      <td>264.069282</td>
      <td>2009.070531</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.240976</td>
      <td>26014.728304</td>
      <td>3.818617</td>
      <td>733.116493</td>
      <td>3.972567</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.090706</td>
      <td>0.003600</td>
      <td>1.350000</td>
      <td>1989.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.000000</td>
      <td>5.442540</td>
      <td>0.229000</td>
      <td>32.560000</td>
      <td>2007.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.000000</td>
      <td>39.979500</td>
      <td>1.260000</td>
      <td>55.250000</td>
      <td>2010.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>526.005000</td>
      <td>3.040000</td>
      <td>178.500000</td>
      <td>2012.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>7.000000</td>
      <td>730000.000000</td>
      <td>25.000000</td>
      <td>8500.000000</td>
      <td>2014.000000</td>
    </tr>
  </tbody>
</table>
</div>

## 2. Groupby：分割, 应用, 组合

有时我们需要对某些标签或索引的局部进行累计分析。`groupby`函数可以实现lazy分割，得到groupby对象；再对groupby对象进行函数操作时，就等价于将函数操作分别作用于分割出的每一个组，并输出合并的结果。此过程称为*split-apply-combine*：

- split：将df按照指定的key分割成若干组；
- apply：对每个组应用函数（通常是累计、转换或过滤函数）；
- combine：将每一组的结果合并成一个输出数组。
  虽然可以使用此前掩码、累计、合并操作实现上述过程，但`groupby`常常只需要一行代码就可以完成，完美隐藏了中间的分割过程。

### 2.1 Split-Apply-Combine

```python
df = pd.DataFrame({'key': ['A', 'B', 'C', 'A', 'B', 'C'],
                   'data': range(6)}, columns=['key', 'data'])
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>key</th>
      <th>data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>

```python
df.groupby('key')
```

    <pandas.core.groupby.generic.DataFrameGroupBy object at 0x7fae291ef400>

_groupby_ 返回一个DataFrameGroupBy对象，其中“隐藏”着若干组数据，但在没有应用累计函数之前是不会计算的——延迟计算（lazy evalution）：

```python
df.groupby('key').sum()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>3</td>
    </tr>
    <tr>
      <th>B</th>
      <td>5</td>
    </tr>
    <tr>
      <th>C</th>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>

### 2.2 Groupby对象的基本操作方法

_Groupby_ 对象可以被看做是分割得到的Dataframe的集合。

#### 1）按列取值

依据method列进行分组，只输出orbital_period的中位数：

```python
planets.groupby('method')['orbital_period'].median()
```

    method
    Astrometry                         631.180000
    Eclipse Timing Variations         4343.500000
    Imaging                          27500.000000
    Microlensing                      3300.000000
    Orbital Brightness Modulation        0.342887
    Pulsar Timing                       66.541900
    Pulsation Timing Variations       1170.000000
    Radial Velocity                    360.200000
    Transit                              5.714932
    Transit Timing Variations           57.011000
    Name: orbital_period, dtype: float64

#### 2）按组迭代

_Groupby_ 对象支持直接**按组进行迭代**：

```python
for (method, group) in planets.groupby('method'):
    print("{0:30s} shape={1}".format(method, group.shape))
```

    Astrometry                     shape=(2, 6)
    Eclipse Timing Variations      shape=(9, 6)
    Imaging                        shape=(38, 6)
    Microlensing                   shape=(23, 6)
    Orbital Brightness Modulation  shape=(3, 6)
    Pulsar Timing                  shape=(5, 6)
    Pulsation Timing Variations    shape=(1, 6)
    Radial Velocity                shape=(553, 6)
    Transit                        shape=(397, 6)
    Transit Timing Variations      shape=(4, 6)

#### 3）调用方法

```python
planets.groupby('method')['year'].describe()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>method</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Astrometry</th>
      <td>2.0</td>
      <td>2011.500000</td>
      <td>2.121320</td>
      <td>2010.0</td>
      <td>2010.75</td>
      <td>2011.5</td>
      <td>2012.25</td>
      <td>2013.0</td>
    </tr>
    <tr>
      <th>Eclipse Timing Variations</th>
      <td>9.0</td>
      <td>2010.000000</td>
      <td>1.414214</td>
      <td>2008.0</td>
      <td>2009.00</td>
      <td>2010.0</td>
      <td>2011.00</td>
      <td>2012.0</td>
    </tr>
    <tr>
      <th>Imaging</th>
      <td>38.0</td>
      <td>2009.131579</td>
      <td>2.781901</td>
      <td>2004.0</td>
      <td>2008.00</td>
      <td>2009.0</td>
      <td>2011.00</td>
      <td>2013.0</td>
    </tr>
    <tr>
      <th>Microlensing</th>
      <td>23.0</td>
      <td>2009.782609</td>
      <td>2.859697</td>
      <td>2004.0</td>
      <td>2008.00</td>
      <td>2010.0</td>
      <td>2012.00</td>
      <td>2013.0</td>
    </tr>
    <tr>
      <th>Orbital Brightness Modulation</th>
      <td>3.0</td>
      <td>2011.666667</td>
      <td>1.154701</td>
      <td>2011.0</td>
      <td>2011.00</td>
      <td>2011.0</td>
      <td>2012.00</td>
      <td>2013.0</td>
    </tr>
    <tr>
      <th>Pulsar Timing</th>
      <td>5.0</td>
      <td>1998.400000</td>
      <td>8.384510</td>
      <td>1992.0</td>
      <td>1992.00</td>
      <td>1994.0</td>
      <td>2003.00</td>
      <td>2011.0</td>
    </tr>
    <tr>
      <th>Pulsation Timing Variations</th>
      <td>1.0</td>
      <td>2007.000000</td>
      <td>NaN</td>
      <td>2007.0</td>
      <td>2007.00</td>
      <td>2007.0</td>
      <td>2007.00</td>
      <td>2007.0</td>
    </tr>
    <tr>
      <th>Radial Velocity</th>
      <td>553.0</td>
      <td>2007.518987</td>
      <td>4.249052</td>
      <td>1989.0</td>
      <td>2005.00</td>
      <td>2009.0</td>
      <td>2011.00</td>
      <td>2014.0</td>
    </tr>
    <tr>
      <th>Transit</th>
      <td>397.0</td>
      <td>2011.236776</td>
      <td>2.077867</td>
      <td>2002.0</td>
      <td>2010.00</td>
      <td>2012.0</td>
      <td>2013.00</td>
      <td>2014.0</td>
    </tr>
    <tr>
      <th>Transit Timing Variations</th>
      <td>4.0</td>
      <td>2012.500000</td>
      <td>1.290994</td>
      <td>2011.0</td>
      <td>2011.75</td>
      <td>2012.5</td>
      <td>2013.25</td>
      <td>2014.0</td>
    </tr>
  </tbody>
</table>
</div>

### 2.3 累计(aggregate), 过滤(filter), 转换(transform), 应用(apply)

_Groupby_ 对象的一些重要方法：

- `aggregate`
- `filter`
- `transform`
- `apply`

```python
# 数据准备
rng = np.random.RandomState(0)
df = pd.DataFrame({'key': ['A', 'B', 'C', 'A', 'B', 'C'],
                   'data1': range(6),
                   'data2': rng.randint(0, 10, 6)},
                   columns = ['key', 'data1', 'data2'])
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>key</th>
      <th>data1</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>5</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

#### 1）累计

`aggregate()`支持更复杂的操作，如字符串、函数等，并且能一次性计算所有累计值；

```python
df.groupby('key').aggregate(['min',max,np.median])
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="3" halign="left">data1</th>
      <th colspan="3" halign="left">data2</th>
    </tr>
    <tr>
      <th></th>
      <th>min</th>
      <th>max</th>
      <th>median</th>
      <th>min</th>
      <th>max</th>
      <th>median</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>0</td>
      <td>3</td>
      <td>1.5</td>
      <td>3</td>
      <td>5</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>B</th>
      <td>1</td>
      <td>4</td>
      <td>2.5</td>
      <td>0</td>
      <td>7</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>C</th>
      <td>2</td>
      <td>5</td>
      <td>3.5</td>
      <td>3</td>
      <td>9</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
df.groupby('key').aggregate({'data1':['min', np.median],
                            'data2':'max'})
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th></th>
      <th>min</th>
      <th>median</th>
      <th>max</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>0</td>
      <td>1.5</td>
      <td>5</td>
    </tr>
    <tr>
      <th>B</th>
      <td>1</td>
      <td>2.5</td>
      <td>7</td>
    </tr>
    <tr>
      <th>C</th>
      <td>2</td>
      <td>3.5</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

#### 2）过滤

```python
# 判断函数
# 参数x是一个组，即dataframe
def filter_func(x):
    return x['data2'].std() > 4
```

```python
df.groupby('key').std()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>2.12132</td>
      <td>1.414214</td>
    </tr>
    <tr>
      <th>B</th>
      <td>2.12132</td>
      <td>4.949747</td>
    </tr>
    <tr>
      <th>C</th>
      <td>2.12132</td>
      <td>4.242641</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 将过滤函数应用于每个lazy组中
# 不符合条件（False）的lazy组被筛掉。
df.groupby('key').filter(filter_func)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>key</th>
      <th>data1</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>5</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

#### 3）转换

转换，形状与原来的输入是一样的。

```python
df.groupby('key').transform(lambda x:x-x.mean())
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.5</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-1.5</td>
      <td>-3.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-1.5</td>
      <td>-3.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.5</td>
      <td>-1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.5</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.5</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
</div>

#### 4）apply方法

`apply()`方法让你可以在每个组上应用任意方法：

```python
# 定义函数
# 参数x是一个组，即dataframe
def norm_by_data2(x):
    # x是一个分组数据的Dataframe
    x['data1'] /= x['data2'].sum()
    return x

df.groupby('key').apply(norm_by_data2)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>key</th>
      <th>data1</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>0.000000</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>0.142857</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>0.166667</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>0.375000</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>0.571429</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>0.416667</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

### 2.4 设置分割的键（不太会用得到）

```python
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>key</th>
      <th>data1</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>5</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

#### 1）将列表、数组、Series或索引作为分组键

分组键的长度需要与df匹配。

```python
# L作为新的一列，成为key列
L = [0,1,0,1,2,0]
df.groupby(L).sum()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>17</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>

#### 2）用字典或Series将索引映射到分组名称

```python
df2 = df.set_index('key')
df2
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>B</th>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>C</th>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>A</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>B</th>
      <td>4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>C</th>
      <td>5</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>

```python
mapping = {'A':'vowel', 'B':'consonant', 'C':'consonant'}
df2.groupby(mapping).sum()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>consonant</th>
      <td>12</td>
      <td>19</td>
    </tr>
    <tr>
      <th>vowel</th>
      <td>3</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>

#### 3）任意Python函数

```python
df2.groupby(str.lower).mean()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th>key</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>1.5</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>2.5</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.5</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>

#### 4）多个有效键组成的列表

```python
df2.groupby([str.lower, mapping]).mean()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th>key</th>
      <th>key</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <th>vowel</th>
      <td>1.5</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>b</th>
      <th>consonant</th>
      <td>2.5</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>c</th>
      <th>consonant</th>
      <td>3.5</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
df2.groupby([mapping, str.lower]).mean()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th></th>
      <th>data1</th>
      <th>data2</th>
    </tr>
    <tr>
      <th>key</th>
      <th>key</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">consonant</th>
      <th>b</th>
      <td>2.5</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.5</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>vowel</th>
      <th>a</th>
      <td>1.5</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>
