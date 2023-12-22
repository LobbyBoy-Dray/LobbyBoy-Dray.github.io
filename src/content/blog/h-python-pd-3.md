---
title: >
  Python | Pandas | Operating on Data
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-3
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

- 对于一元运算，通用函数将在输出结果中保留行索引和列标签；
- 对于二元运算，通用函数将会在自动对齐索引后进行运算。

## 1. 一元运算：保留索引

```python
df = pd.DataFrame(np.random.randint(0,100,(3,4)), columns=['A','B','C','D'])
df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>A</th>
      <th>B</th>
      <th>C</th>
      <th>D</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>25</td>
      <td>73</td>
      <td>54</td>
      <td>82</td>
    </tr>
    <tr>
      <th>1</th>
      <td>74</td>
      <td>23</td>
      <td>96</td>
      <td>47</td>
    </tr>
    <tr>
      <th>2</th>
      <td>32</td>
      <td>23</td>
      <td>87</td>
      <td>39</td>
    </tr>
  </tbody>
</table>
</div>

```python
np.sin(df * np.pi / 4)
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>A</th>
      <th>B</th>
      <th>C</th>
      <th>D</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7.071068e-01</td>
      <td>0.707107</td>
      <td>-1.000000e+00</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.000000e+00</td>
      <td>-0.707107</td>
      <td>-2.939152e-15</td>
      <td>-0.707107</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-9.797174e-16</td>
      <td>-0.707107</td>
      <td>-7.071068e-01</td>
      <td>-0.707107</td>
    </tr>
  </tbody>
</table>
</div>

## 2. 二元运算：索引对齐

```python
# Series
# 先进行索引合并操作，求索引的并集，再根据索引进行对齐，对齐后进行运算
area = pd.Series({'Alaska':1723337, 'Texas':695662, 'California':423967}, name = 'area')
population = pd.Series({'Alaska':38332521, 'Texas':26448193, 'New York':19651127}, name = 'population')
population / area
```

    Alaska        22.243195
    California          NaN
    New York            NaN
    Texas         38.018740
    dtype: float64

```python
# DataFrame
# 合并，对齐，运算
A = pd.DataFrame(np.random.randint(0,10,(2,3)), columns=list('ABD'))
B = pd.DataFrame(np.random.randint(0,10,(3,3)), columns=list('BCA'))
print(A)
print("="*20)
print(B)
print("="*20)
print(A+B)
```

       A  B  D
    0  3  8  3
    1  5  7  5
    ====================
       B  C  A
    0  0  6  0
    1  2  6  3
    2  5  6  7
    ====================
         A    B   C   D
    0  3.0  8.0 NaN NaN
    1  8.0  9.0 NaN NaN
    2  NaN  NaN NaN NaN

```python
# 可以使用运算符的通用函数形式，设置fill_value参数：一方有值就不会NaN
A.add(B, fill_value=0)
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>A</th>
      <th>B</th>
      <th>C</th>
      <th>D</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3.0</td>
      <td>8.0</td>
      <td>6.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8.0</td>
      <td>9.0</td>
      <td>6.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7.0</td>
      <td>5.0</td>
      <td>6.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

## 3. Series和DataFrame的混合运算

- DataFrame和Series的运算规则，与二维数组和一维数组的运算规则相同。
- 默认按行计算。需要按列计算，则需要利用运算符方法，设置axis参数，`axis=0`。
