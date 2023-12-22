---
title: >
  Python | Pandas | Hierarchical Indexing
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-5
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

Series可以存储一维数据，DataFrame可以存储二维数据。多维数据可以利用Pandas的层级索引（多级索引）配合多个有着不同等级（level）的一级索引来表现。这样可以将高维数组转化为类似一维Series或二维DataFrame的形式。

## Table of contents

## 1. 多级索引的创建

### 1.1 直接将index参数设置为至少二维的索引数组

```python
# index数组内部包含另外两个数组
# 第一个数组：每行的第一层索引的顺序排列
# 第二个数组：每行的第二层索引的顺序排列
rng = np.random.RandomState(0)
df = pd.DataFrame(rng.randint(0,100,(4,2)),
                  index=[['a','a','b','b'],[1,2,1,2]],
                  columns=['data1','data2'])
df
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
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">a</th>
      <th>1</th>
      <td>44</td>
      <td>47</td>
    </tr>
    <tr>
      <th>2</th>
      <td>64</td>
      <td>67</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">b</th>
      <th>1</th>
      <td>67</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>83</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
</div>

### 1.2 将元组作为键的字典传递给Pandas

```python
data = {('California',2000):33871648,
        ('California',2010):37253956,
        ('Texas',2000):20851820,
        ('Texas',2010):25145561,
        ('New York',2000):18976457,
        ('New York',2010):19378102}
pd.Series(data)
```

    California  2000    33871648
                2010    37253956
    Texas       2000    20851820
                2010    25145561
    New York    2000    18976457
                2010    19378102
    dtype: int64

### 1.3 显式创建索引对象后传入index参数

#### 1) MultiIndex对象

以下3种创建方法等价，均产生同一个多级索引对象：

```python
# from_arrays方法
pd.MultiIndex.from_arrays([['a','a','b','b'],
                          [1,2,1,2]])
```

    MultiIndex([('a', 1),
                ('a', 2),
                ('b', 1),
                ('b', 2)],
               )

```python
# from_tuples方法
pd.MultiIndex.from_tuples([('a',1),('a',2),
                          ('b',1),('b',2)])
```

    MultiIndex([('a', 1),
                ('a', 2),
                ('b', 1),
                ('b', 2)],
               )

```python
# from_product方法
pd.MultiIndex.from_product([['a','b'],
                           [1,2]])
```

    MultiIndex([('a', 1),
                ('a', 2),
                ('b', 1),
                ('b', 2)],
               )

注意：新版本的pandas不再支持直接使用`MultiIndex(levels=xxx, labels=xxx)`的方式创建多级索引对象，仅支持使用3种from函数创建：\*A new MultiIndex is typically constructed using one of the helper methods `MultiIndex.from_arrays()`, `MultiIndex.from_product()` and `MultiIndex.from_tuples()`. 故以下方法报错：

```python
# 构造函数
pd.MultiIndex(levels=[['a','b'],[1,2]],
             labels=[[0,0,1,1],[0,1,0,1]])
```

    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    /var/folders/6b/77kzlkh11f959wbt2yf57jhm0000gn/T/ipykernel_70439/2062238608.py in <module>
          1 # 构造函数
    ----> 2 pd.MultiIndex(levels=[['a','b'],[1,2]],
          3              labels=[[0,0,1,1],[0,1,0,1]])


    TypeError: __new__() got an unexpected keyword argument 'labels'

#### 2) MultiIndex名称

```python
# 数据准备
index = pd.MultiIndex.from_product([['California', 'Texas', 'New York'],
                                   [2000,2010]])
data = pd.Series([33871648,37253956,20851820,25145561,18976457,19378102], index=index)
data
```

    California  2000    33871648
                2010    37253956
    Texas       2000    20851820
                2010    25145561
    New York    2000    18976457
                2010    19378102
    dtype: int64

```python
# 设置多级索引中各层索引的名称
data.index.names=['state','year']
data
```

    state       year
    California  2000    33871648
                2010    37253956
    Texas       2000    20851820
                2010    25145561
    New York    2000    18976457
                2010    19378102
    dtype: int64

#### 3) 多级列索引

```python
# 数据准备
# 设置多级行索引
index = pd.MultiIndex.from_product([[2013,2014],[1,2]])
# 设置多级列索引
columns = pd.MultiIndex.from_product([['Bob','Guido','Sue'],['HR','Temp']])
# 数据模拟
rng=np.random.RandomState(0)
data = np.round(rng.randn(4,6),1)
data[:,::2]*=10
data += 37
# 创建df
health_data = pd.DataFrame(data, index=index, columns=columns)
health_data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th colspan="2" halign="left">Bob</th>
      <th colspan="2" halign="left">Guido</th>
      <th colspan="2" halign="left">Sue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>55.0</td>
      <td>37.4</td>
      <td>47.0</td>
      <td>39.2</td>
      <td>56.0</td>
      <td>36.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>47.0</td>
      <td>36.8</td>
      <td>36.0</td>
      <td>37.4</td>
      <td>38.0</td>
      <td>38.5</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2014</th>
      <th>1</th>
      <td>45.0</td>
      <td>37.1</td>
      <td>41.0</td>
      <td>37.3</td>
      <td>52.0</td>
      <td>36.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.0</td>
      <td>36.1</td>
      <td>11.0</td>
      <td>37.7</td>
      <td>46.0</td>
      <td>36.3</td>
    </tr>
  </tbody>
</table>
</div>

## 2. 取值和切片

### 2.1 Series的多级索引

#### 1) 取值

```python
# 数据准备
index = pd.MultiIndex.from_product([['California', 'Texas', 'New York','Washington'],
                                    [2000,2010,2015]], names=['state','year'])
data = pd.Series([33871648,37253956,20851820,25145561,18976457,19378102,
                  2678192,37281963,12783921,20987648,37628192,10296478], index=index)
data
```

    state       year
    California  2000    33871648
                2010    37253956
                2015    20851820
    Texas       2000    25145561
                2010    18976457
                2015    19378102
    New York    2000     2678192
                2010    37281963
                2015    12783921
    Washington  2000    20987648
                2010    37628192
                2015    10296478
    dtype: int64

```python
# California州2010年数据
data['California',2010]
```

    37253956

```python
# California州所有年份数据
data['California']
```

    year
    2000    33871648
    2010    37253956
    2015    20851820
    dtype: int64

```python
# 2000年所有州的数据
# 用较低层级的索引取值时，高层索引可以用空切片，但要注意此时索引应该排序
data[:,2000]
```

    state
    California    33871648
    Texas         25145561
    New York       2678192
    Washington    20987648
    dtype: int64

但还是强烈建议直接使用索引器indexer中的loc或iloc！

```python
# 花哨索引
data.loc[:,[2000,2010]]
```

    state       year
    California  2000    33871648
    Texas       2000    25145561
    New York    2000     2678192
    Washington  2000    20987648
    California  2010    37253956
    Texas       2010    18976457
    New York    2010    37281963
    Washington  2010    37628192
    dtype: int64

#### 2) 切片

```python
data
```

    state       year
    California  2000    33871648
                2010    37253956
                2015    20851820
    Texas       2000    25145561
                2010    18976457
                2015    19378102
    New York    2000     2678192
                2010    37281963
                2015    12783921
    Washington  2000    20987648
                2010    37628192
                2015    10296478
    dtype: int64

```python
# 直接切片会报错(UnsortedIndexError)，必须首先对索引排序
# data.loc["California":"Texas"]
```

```python
data_sorted = data.sort_index()
data_sorted
```

    state       year
    California  2000    33871648
                2010    37253956
                2015    20851820
    New York    2000     2678192
                2010    37281963
                2015    12783921
    Texas       2000    25145561
                2010    18976457
                2015    19378102
    Washington  2000    20987648
                2010    37628192
                2015    10296478
    dtype: int64

```python
data_sorted.loc["California":"Texas"]
```

    state       year
    California  2000    33871648
                2010    37253956
                2015    20851820
    New York    2000     2678192
                2010    37281963
                2015    12783921
    Texas       2000    25145561
                2010    18976457
                2015    19378102
    dtype: int64

```python
data_sorted.loc[:, 2010:2015]
```

    state       year
    California  2010    37253956
                2015    20851820
    New York    2010    37281963
                2015    12783921
    Texas       2010    18976457
                2015    19378102
    Washington  2010    37628192
                2015    10296478
    dtype: int64

### 2.2 DataFrame的多级索引

```python
health_data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th colspan="2" halign="left">Bob</th>
      <th colspan="2" halign="left">Guido</th>
      <th colspan="2" halign="left">Sue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>55.0</td>
      <td>37.4</td>
      <td>47.0</td>
      <td>39.2</td>
      <td>56.0</td>
      <td>36.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>47.0</td>
      <td>36.8</td>
      <td>36.0</td>
      <td>37.4</td>
      <td>38.0</td>
      <td>38.5</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2014</th>
      <th>1</th>
      <td>45.0</td>
      <td>37.1</td>
      <td>41.0</td>
      <td>37.3</td>
      <td>52.0</td>
      <td>36.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.0</td>
      <td>36.1</td>
      <td>11.0</td>
      <td>37.7</td>
      <td>46.0</td>
      <td>36.3</td>
    </tr>
  </tbody>
</table>
</div>

```python
health_data['Guido','HR']
```

    2013  1    47.0
          2    36.0
    2014  1    41.0
          2    11.0
    Name: (Guido, HR), dtype: float64

```python
health_data.iloc[:3, :3]
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th colspan="2" halign="left">Bob</th>
      <th>Guido</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>55.0</td>
      <td>37.4</td>
      <td>47.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>47.0</td>
      <td>36.8</td>
      <td>36.0</td>
    </tr>
    <tr>
      <th>2014</th>
      <th>1</th>
      <td>45.0</td>
      <td>37.1</td>
      <td>41.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 多层级索引取值或切片
idx = pd.IndexSlice
health_data.loc[idx[:,1], idx[:,'HR']]
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th>Bob</th>
      <th>Guido</th>
      <th>Sue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>HR</th>
      <th>HR</th>
      <th>HR</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013</th>
      <th>1</th>
      <td>55.0</td>
      <td>47.0</td>
      <td>56.0</td>
    </tr>
    <tr>
      <th>2014</th>
      <th>1</th>
      <td>45.0</td>
      <td>41.0</td>
      <td>52.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
health_data.loc[idx[2013],idx['Guido']]
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>47.0</td>
      <td>39.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>36.0</td>
      <td>37.4</td>
    </tr>
  </tbody>
</table>
</div>

## 3. 行列转换

### 3.1. 有序索引和无序索引

如果MultiIndex不是有序的索引——不按字典顺序的（lexographically），大多数切片操作都会失败。

```python
rng = np.random.RandomState(0)
# data的第一层索引无序，切片操作会出错
index = pd.MultiIndex.from_arrays([['a','a','c','c','b','b'],
                                   [1,2,1,2,1,2]])
data = pd.Series(rng.randn(6), index=index)
data
```

    a  1    1.764052
       2    0.400157
    c  1    0.978738
       2    2.240893
    b  1    1.867558
       2   -0.977278
    dtype: float64

```python
# 索引排序
data = data.sort_index()
data
```

    a  1    1.764052
       2    0.400157
    b  1    1.867558
       2   -0.977278
    c  1    0.978738
       2    2.240893
    dtype: float64

### 3.2 stack与unstack

```python
data.unstack()
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
      <th>a</th>
      <td>1.764052</td>
      <td>0.400157</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1.867558</td>
      <td>-0.977278</td>
    </tr>
    <tr>
      <th>c</th>
      <td>0.978738</td>
      <td>2.240893</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.unstack(level=0)
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
      <th>1</th>
      <td>1.764052</td>
      <td>1.867558</td>
      <td>0.978738</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.400157</td>
      <td>-0.977278</td>
      <td>2.240893</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.unstack(level=1)
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
      <th>a</th>
      <td>1.764052</td>
      <td>0.400157</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1.867558</td>
      <td>-0.977278</td>
    </tr>
    <tr>
      <th>c</th>
      <td>0.978738</td>
      <td>2.240893</td>
    </tr>
  </tbody>
</table>
</div>

`stack`是`unstack`的逆操作。

### 3.3 索引的设置与重置

`reset_index()`：还原索引，即：将索引（即使是多层索引）还原为默认的整型索引；返回DataFrame：

```python
# 例1-1
index = pd.MultiIndex.from_product([['California', 'Texas', 'New York','Washington'],
                                    [2000,2010,2015]], names=['state','year'])
data = pd.Series([33871648,37253956,20851820,25145561,18976457,19378102,
                  2678192,37281963,12783921,20987648,37628192,10296478], index=index)
data
```

    state       year
    California  2000    33871648
                2010    37253956
                2015    20851820
    Texas       2000    25145561
                2010    18976457
                2015    19378102
    New York    2000     2678192
                2010    37281963
                2015    12783921
    Washington  2000    20987648
                2010    37628192
                2015    10296478
    dtype: int64

```python
# 例1-2, name参数只对Series对象的reset_index方法
data.reset_index(name='pop')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>state</th>
      <th>year</th>
      <th>pop</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>California</td>
      <td>2000</td>
      <td>33871648</td>
    </tr>
    <tr>
      <th>1</th>
      <td>California</td>
      <td>2010</td>
      <td>37253956</td>
    </tr>
    <tr>
      <th>2</th>
      <td>California</td>
      <td>2015</td>
      <td>20851820</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Texas</td>
      <td>2000</td>
      <td>25145561</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Texas</td>
      <td>2010</td>
      <td>18976457</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Texas</td>
      <td>2015</td>
      <td>19378102</td>
    </tr>
    <tr>
      <th>6</th>
      <td>New York</td>
      <td>2000</td>
      <td>2678192</td>
    </tr>
    <tr>
      <th>7</th>
      <td>New York</td>
      <td>2010</td>
      <td>37281963</td>
    </tr>
    <tr>
      <th>8</th>
      <td>New York</td>
      <td>2015</td>
      <td>12783921</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Washington</td>
      <td>2000</td>
      <td>20987648</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Washington</td>
      <td>2010</td>
      <td>37628192</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Washington</td>
      <td>2015</td>
      <td>10296478</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 例2-1
df = pd.DataFrame([('bird',    389.0),
                   ('bird',     24.0),
                   ('mammal',   80.5),
                   ('mammal', np.nan)],
                  index=['falcon', 'parrot', 'lion', 'monkey'],
                  columns=('class', 'max_speed'))
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>class</th>
      <th>max_speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>falcon</th>
      <td>bird</td>
      <td>389.0</td>
    </tr>
    <tr>
      <th>parrot</th>
      <td>bird</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>lion</th>
      <td>mammal</td>
      <td>80.5</td>
    </tr>
    <tr>
      <th>monkey</th>
      <td>mammal</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

```python
df.reset_index()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>index</th>
      <th>class</th>
      <th>max_speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>falcon</td>
      <td>bird</td>
      <td>389.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>parrot</td>
      <td>bird</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>lion</td>
      <td>mammal</td>
      <td>80.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>monkey</td>
      <td>mammal</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 丢弃原索引列
df.reset_index(drop=True)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>class</th>
      <th>max_speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>bird</td>
      <td>389.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bird</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>mammal</td>
      <td>80.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>mammal</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

`set_index()`：设置索引

```python
data = pd.DataFrame({'a':['bar','bar','foo','foo'],
                     'b':['one','two','one','two'],
                     'c':['z','y','x','w'],'d':[1.0,2,3,4]})
data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>bar</td>
      <td>one</td>
      <td>z</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bar</td>
      <td>two</td>
      <td>y</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>foo</td>
      <td>one</td>
      <td>x</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>foo</td>
      <td>two</td>
      <td>w</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.set_index('a')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
    <tr>
      <th>a</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>bar</th>
      <td>one</td>
      <td>z</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>bar</th>
      <td>two</td>
      <td>y</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>foo</th>
      <td>one</td>
      <td>x</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>foo</th>
      <td>two</td>
      <td>w</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.set_index(['a','b'])
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th></th>
      <th>c</th>
      <th>d</th>
    </tr>
    <tr>
      <th>a</th>
      <th>b</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">bar</th>
      <th>one</th>
      <td>z</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>two</th>
      <td>y</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">foo</th>
      <th>one</th>
      <td>x</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>two</th>
      <td>w</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.set_index(['b','a'])
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th></th>
      <th>c</th>
      <th>d</th>
    </tr>
    <tr>
      <th>b</th>
      <th>a</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>one</th>
      <th>bar</th>
      <td>z</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>two</th>
      <th>bar</th>
      <td>y</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>one</th>
      <th>foo</th>
      <td>x</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>two</th>
      <th>foo</th>
      <td>w</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>

## 4. 数据累计方法

设置参数`level`实现对子集的累积操作；配合`axis`可以对列索引进行类似的累积操作。

```python
data = health_data.copy()
data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th colspan="2" halign="left">Bob</th>
      <th colspan="2" halign="left">Guido</th>
      <th colspan="2" halign="left">Sue</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>55.0</td>
      <td>37.4</td>
      <td>47.0</td>
      <td>39.2</td>
      <td>56.0</td>
      <td>36.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>47.0</td>
      <td>36.8</td>
      <td>36.0</td>
      <td>37.4</td>
      <td>38.0</td>
      <td>38.5</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2014</th>
      <th>1</th>
      <td>45.0</td>
      <td>37.1</td>
      <td>41.0</td>
      <td>37.3</td>
      <td>52.0</td>
      <td>36.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.0</td>
      <td>36.1</td>
      <td>11.0</td>
      <td>37.7</td>
      <td>46.0</td>
      <td>36.3</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.index.names=['year','visit']
data.columns.names=['subject','type']
```

```python
data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>subject</th>
      <th colspan="2" halign="left">Bob</th>
      <th colspan="2" halign="left">Guido</th>
      <th colspan="2" halign="left">Sue</th>
    </tr>
    <tr>
      <th></th>
      <th>type</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
    <tr>
      <th>year</th>
      <th>visit</th>
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
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>55.0</td>
      <td>37.4</td>
      <td>47.0</td>
      <td>39.2</td>
      <td>56.0</td>
      <td>36.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>47.0</td>
      <td>36.8</td>
      <td>36.0</td>
      <td>37.4</td>
      <td>38.0</td>
      <td>38.5</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2014</th>
      <th>1</th>
      <td>45.0</td>
      <td>37.1</td>
      <td>41.0</td>
      <td>37.3</td>
      <td>52.0</td>
      <td>36.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.0</td>
      <td>36.1</td>
      <td>11.0</td>
      <td>37.7</td>
      <td>46.0</td>
      <td>36.3</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 此方法即将被废弃
data.mean(level='year')
```

    /var/folders/6b/77kzlkh11f959wbt2yf57jhm0000gn/T/ipykernel_70439/1113942518.py:2: FutureWarning: Using the level keyword in DataFrame and Series aggregations is deprecated and will be removed in a future version. Use groupby instead. df.median(level=1) should use df.groupby(level=1).median().
      data.mean(level='year')

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th>subject</th>
      <th colspan="2" halign="left">Bob</th>
      <th colspan="2" halign="left">Guido</th>
      <th colspan="2" halign="left">Sue</th>
    </tr>
    <tr>
      <th>type</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
    <tr>
      <th>year</th>
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
      <th>2013</th>
      <td>51.0</td>
      <td>37.1</td>
      <td>41.5</td>
      <td>38.3</td>
      <td>47.0</td>
      <td>37.25</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>42.5</td>
      <td>36.6</td>
      <td>26.0</td>
      <td>37.5</td>
      <td>49.0</td>
      <td>36.55</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 等价方法
data.groupby(level=0).mean()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th>subject</th>
      <th colspan="2" halign="left">Bob</th>
      <th colspan="2" halign="left">Guido</th>
      <th colspan="2" halign="left">Sue</th>
    </tr>
    <tr>
      <th>type</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
    <tr>
      <th>year</th>
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
      <th>2013</th>
      <td>51.0</td>
      <td>37.1</td>
      <td>41.5</td>
      <td>38.3</td>
      <td>47.0</td>
      <td>37.25</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>42.5</td>
      <td>36.6</td>
      <td>26.0</td>
      <td>37.5</td>
      <td>49.0</td>
      <td>36.55</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 此方法即将被废弃
data.mean(level='type',axis=1)
```

    /var/folders/6b/77kzlkh11f959wbt2yf57jhm0000gn/T/ipykernel_70439/3859318972.py:2: FutureWarning: Using the level keyword in DataFrame and Series aggregations is deprecated and will be removed in a future version. Use groupby instead. df.median(level=1) should use df.groupby(level=1).median().
      data.mean(level='type',axis=1)

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>type</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
    <tr>
      <th>year</th>
      <th>visit</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>52.666667</td>
      <td>37.533333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.333333</td>
      <td>37.566667</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2014</th>
      <th>1</th>
      <td>46.000000</td>
      <td>37.066667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>32.333333</td>
      <td>36.700000</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 等价方法
# axis=1表示聚合维度在「列」
# level=1表示聚合的字段是「type」
data.groupby(level=1, axis=1).mean()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>type</th>
      <th>HR</th>
      <th>Temp</th>
    </tr>
    <tr>
      <th>year</th>
      <th>visit</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">2013</th>
      <th>1</th>
      <td>52.666667</td>
      <td>37.533333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.333333</td>
      <td>37.566667</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2014</th>
      <th>1</th>
      <td>46.000000</td>
      <td>37.066667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>32.333333</td>
      <td>36.700000</td>
    </tr>
  </tbody>
</table>
</div>
