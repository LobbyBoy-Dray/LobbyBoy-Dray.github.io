---
title: >
  Python | Pandas | Missing Data
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-4
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

## 1. NaN

- Not a Number，“不是一个数字”，数值类型的缺失值。
- NaN可以看做一个数据病毒，它会将与它接触过的数据同化为NaN。
- NaN是一种特殊的浮点数，不能完全等价于其他数据类型。

但对Pandas来说，None和NaN“基本”等价：

```python
data = pd.Series([1, None, np.nan])
data
```

    0    1.0
    1    NaN
    2    NaN
    dtype: float64

## 2. 发现

- `.isnull()`
- `notnull()`

返回布尔掩码。

```python
# 数据准备
np.random.seed(23)
data = pd.DataFrame(np.random.randint(0,100,(4,5)))
data[data<50] = np.nan
data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>83</td>
      <td>NaN</td>
      <td>73.0</td>
      <td>54.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>76</td>
      <td>91.0</td>
      <td>NaN</td>
      <td>90.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>51</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>66</td>
      <td>75.0</td>
      <td>85.0</td>
      <td>69.0</td>
      <td>64.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.isnull()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>

```python
data.notnull()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>

## 3. 剔除

`.dropna()`，对Series很简单，但对DataFrame需要考虑：

- 删行还是删列？
- 有一个缺失值就删整行（列），还是全部都是缺失值才删整行（列）？

默认情况：

- 剔除整行数据，即`axis='row'`是默认的；
- 含有一个缺失值就删，即`how='any'`是默认的；

可以自制：

- `axix=columns`，剔除整列数据；
- `how='all'`，所有数据都是缺失值才删除；
- `thresh=3`，设置允许的非缺失值的最小数量，如果非缺失值太少，才删——介于“有一个就删”和“全是才删”之间；

## 4. 填充

`.fillna()`，返回副本。

```python
# 数据准备
np.random.seed(0)
data = pd.DataFrame(np.random.randint(0,100,(4,5)))
data = data[data>50]
data
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>64.0</td>
      <td>67.0</td>
      <td>67</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>83.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>87</td>
    </tr>
    <tr>
      <th>2</th>
      <td>70.0</td>
      <td>88.0</td>
      <td>88.0</td>
      <td>NaN</td>
      <td>58</td>
    </tr>
    <tr>
      <th>3</th>
      <td>65.0</td>
      <td>NaN</td>
      <td>87.0</td>
      <td>NaN</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 用特定值填充缺失值
data.fillna(-1)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.0</td>
      <td>-1.0</td>
      <td>64.0</td>
      <td>67.0</td>
      <td>67</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-1.0</td>
      <td>83.0</td>
      <td>-1.0</td>
      <td>-1.0</td>
      <td>87</td>
    </tr>
    <tr>
      <th>2</th>
      <td>70.0</td>
      <td>88.0</td>
      <td>88.0</td>
      <td>-1.0</td>
      <td>58</td>
    </tr>
    <tr>
      <th>3</th>
      <td>65.0</td>
      <td>-1.0</td>
      <td>87.0</td>
      <td>-1.0</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 设置method参数为ffill，代表forword-fill
# 表示用该NaN"之前的"有效值填充，默认情况是按”列“，即“上面的前面”，此时默认参数axis=0
# 注意，从前往后填充，若前面没有值，则还为NaN；若有值但为NaN，再往前推一个，直到没有值或找到有效值
data.fillna(method='ffill')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>64.0</td>
      <td>67.0</td>
      <td>67</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>83.0</td>
      <td>64.0</td>
      <td>67.0</td>
      <td>87</td>
    </tr>
    <tr>
      <th>2</th>
      <td>70.0</td>
      <td>88.0</td>
      <td>88.0</td>
      <td>67.0</td>
      <td>58</td>
    </tr>
    <tr>
      <th>3</th>
      <td>65.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>67.0</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 设置axis=1按“行“：
data.fillna(method='ffill', axis=1)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>64.0</td>
      <td>67.0</td>
      <td>67.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>83.0</td>
      <td>83.0</td>
      <td>83.0</td>
      <td>87.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>70.0</td>
      <td>88.0</td>
      <td>88.0</td>
      <td>88.0</td>
      <td>58.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>65.0</td>
      <td>65.0</td>
      <td>87.0</td>
      <td>87.0</td>
      <td>88.0</td>
    </tr>
  </tbody>
</table>
</div>
