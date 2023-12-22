---
title: >
  Python | Pandas | Concat & Merge
author: Jingwei Gao
pubDatetime: 2019-02-18T09:25:48+08
postSlug: h-python-pd-6
featured: false
draft: false
hidden: true
tags:
  - python
description: >
---

将不同数据源进行合并：

- `Concat`：将两个数据集非常简单地拼接在一起（默认vstack式拼接）。
- `Merge`：像数据库语言那样连接与合并具有重叠字段的数据集。

## Table of contents

## 1. Concat

```python
# 定义一个能够返回指定Dataframe的函数
# 参数分别为列名与行索引
def make_df(cols, ind):
    data = {c:[str(c) + str(i) for i in ind] for c in cols}
    return pd.DataFrame(data, ind)
# Test
make_df('ABC', range(3))
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>A</th>
      <th>B</th>
      <th>C</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A0</td>
      <td>B0</td>
      <td>C0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
      <td>C1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A2</td>
      <td>B2</td>
      <td>C2</td>
    </tr>
  </tbody>
</table>
</div>

### 1.1 简易合并

类似于`np.concatenate([...])`：

```python
# 合并两个Series
ser1 = pd.Series(['A','B','C'], index=[1,2,3])
ser2 = pd.Series(['D','E','F'], index=[4,5,6])
pd.concat([ser1,ser2])
```

    1    A
    2    B
    3    C
    4    D
    5    E
    6    F
    dtype: object

```python
# 合并时保留重复索引
ser1 = pd.Series(['A','B','C'], index=[1,2,3])
ser2 = pd.Series(['D','E','F'], index=[1,2,3])
pd.concat([ser1,ser2])
```

    1    A
    2    B
    3    C
    1    D
    2    E
    3    F
    dtype: object

```python
# 合并Dataframe，默认逐行进行，类似vstack
df1 = make_df('AB',[1,2])
df2 = make_df('AB',[3,4])
print(df1, end='\n============\n');
print(df2, end='\n============\n');
print(pd.concat([df1, df2]))
```

        A   B
    1  A1  B1
    2  A2  B2
    ============
        A   B
    3  A3  B3
    4  A4  B4
    ============
        A   B
    1  A1  B1
    2  A2  B2
    3  A3  B3
    4  A4  B4

```python
# 设置合并坐标轴
# axis=1等价于axis=‘col’
df1 = make_df('AB',[1,2])
df2 = make_df('CD',[1,2])
print(df1, end='\n============\n');
print(df2, end='\n============\n');
print(pd.concat([df1, df2], axis=1))
```

        A   B
    1  A1  B1
    2  A2  B2
    ============
        C   D
    1  C1  D1
    2  C2  D2
    ============
        A   B   C   D
    1  A1  B1  C1  D1
    2  A2  B2  C2  D2

### 1.2 索引重复

```python
# 重复的行索引被保留
x = make_df('AB',[0,1])
y = make_df('AB',[0,1])
pd.concat([x,y])
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
      <td>A0</td>
      <td>B0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
    </tr>
    <tr>
      <th>0</th>
      <td>A0</td>
      <td>B0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
    </tr>
  </tbody>
</table>
</div>

#### 1) verify_inyegrity：抛出异常

```python
# 设置verify_inyegrity为True，合并时若有索引重复会触发异常
x = make_df('AB',[0,1])
y = make_df('AB',[0,1])
try:
    pd.concat([x,y], verify_integrity=True)
except Exception as e:
    print(e)
```

    Indexes have overlapping values: Int64Index([0, 1], dtype='int64')

#### 2) 忽略索引

```python
# 设置ignore_index为True，合并时创建新的整数索引
x = make_df('AB',[0,1])
y = make_df('AB',[0,1])
pd.concat([x,y], ignore_index=True)
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
      <td>A0</td>
      <td>B0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A0</td>
      <td>B0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A1</td>
      <td>B1</td>
    </tr>
  </tbody>
</table>
</div>

#### 3) 增加多级索引

```python
# 设置keys参数为数据源设置多级索引标签
x = make_df('AB',[0,1])
y = make_df('AB',[0,1])
pd.concat([x,y], keys=['x','y'])
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th></th>
      <th>A</th>
      <th>B</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">x</th>
      <th>0</th>
      <td>A0</td>
      <td>B0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">y</th>
      <th>0</th>
      <td>A0</td>
      <td>B0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
    </tr>
  </tbody>
</table>
</div>

### 1.3 交并集

合并数据时，数据不一定具有一模一样相同的列名，只会有一些列名相同：

```python
df1 = make_df('ABC', [1,2])
df2 = make_df('BCD', [3,4])
pd.concat([df1,df2])
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
      <th>1</th>
      <td>A1</td>
      <td>B1</td>
      <td>C1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A2</td>
      <td>B2</td>
      <td>C2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>B3</td>
      <td>C3</td>
      <td>D3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>B4</td>
      <td>C4</td>
      <td>D4</td>
    </tr>
  </tbody>
</table>
</div>

默认的合并方式是对所有输入列进行**并集合并**，即`join='outer'`；修改`join='inner'`实现对所有输入列的**交集合并**：

```python
pd.concat([df1,df2], join='inner')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>B</th>
      <th>C</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>B1</td>
      <td>C1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>B2</td>
      <td>C2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>B3</td>
      <td>C3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B4</td>
      <td>C4</td>
    </tr>
  </tbody>
</table>
</div>

### 1.4 append方法

- Series对象和DataFrame对象都支持`append`方法，如`df1.append(df2)`，效果和`pd.concat([df1,df2])`一样。
- 但Pandas的`append`方法不直接更新原有对象的值，而是为合并后的数据创建一个新的对象——有点浪费空间。

## 2. Merge

### 2.1 合并类型

三种合并类型：_one-to-one_, _many-to-one_, _many-to-many_

#### 1) one-to-one: 一对一

“左边的”和“右边的”key都是各异的：

```python
df1 = pd.DataFrame({'employee': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'group': ['Accounting', 'Engineering', 'Engineering', 'HR']})
df2 = pd.DataFrame({'employee': ['Lisa', 'Bob', 'Jake', 'Sue'],
                    'hire_date': [2004, 2008, 2012, 2014]})
display('df1', df1, 'df2', df2)
```

    'df1'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
    </tr>
  </tbody>
</table>
</div>

    'df2'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>hire_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Lisa</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bob</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Jake</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

```python
df3 = pd.merge(df1, df2)
display('df3', df3)
```

    'df3'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
      <th>hire_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

`pd.merge`函数识别出，df1与df2都含有一个名为`employee`的列，因此pandas自动将其作为连接的key。

#### 2) many-to-one: 多对一

“左边的”key列有重复值，“右边的”key列是各异的。

```python
df4 = pd.DataFrame({'group': ['Accounting', 'Engineering', 'HR'],
                    'supervisor': ['Carly', 'Guido', 'Steve']})
display('df3', df3, 'df4', df4)
```

    'df3'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
      <th>hire_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

    'df4'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>group</th>
      <th>supervisor</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Accounting</td>
      <td>Carly</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Engineering</td>
      <td>Guido</td>
    </tr>
    <tr>
      <th>2</th>
      <td>HR</td>
      <td>Steve</td>
    </tr>
  </tbody>
</table>
</div>

```python
display('pd.merge(df3, df4)', pd.merge(df3, df4))
```

    'pd.merge(df3, df4)'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
      <th>hire_date</th>
      <th>supervisor</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>2008</td>
      <td>Carly</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>2012</td>
      <td>Guido</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>2004</td>
      <td>Guido</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
      <td>2014</td>
      <td>Steve</td>
    </tr>
  </tbody>
</table>
</div>

#### 3) many-to-many: 多对多

“左边的”key列有重复值，“右边的”key列也有重复值。

```python
df5 = pd.DataFrame({'group': ['Accounting', 'Accounting',
                              'Engineering', 'Engineering', 'HR', 'HR'],
                    'skills': ['math', 'spreadsheets', 'coding', 'linux',
                               'spreadsheets', 'organization']})
display("df1",df1,"df5",df5)
```

    'df1'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
    </tr>
  </tbody>
</table>
</div>

    'df5'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>group</th>
      <th>skills</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Accounting</td>
      <td>math</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Accounting</td>
      <td>spreadsheets</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Engineering</td>
      <td>coding</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Engineering</td>
      <td>linux</td>
    </tr>
    <tr>
      <th>4</th>
      <td>HR</td>
      <td>spreadsheets</td>
    </tr>
    <tr>
      <th>5</th>
      <td>HR</td>
      <td>organization</td>
    </tr>
  </tbody>
</table>
</div>

```python
display("pd.merge(df1,df5)", pd.merge(df1,df5))
```

    'pd.merge(df1,df5)'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
      <th>skills</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>math</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>spreadsheets</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>coding</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>linux</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>coding</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>linux</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Sue</td>
      <td>HR</td>
      <td>spreadsheets</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Sue</td>
      <td>HR</td>
      <td>organization</td>
    </tr>
  </tbody>
</table>
</div>

But in practice, datasets are rarely as clean as the one we're working with here.

### 2.2 合并的键

`pd.merge()` looks for one or more matching column names between the two inputs, and uses this as the key.

该函数自动寻找两个输入间相同的列名，并将其作为key列。但有时候，列名的匹配并不是那么完美，例如：有多个相互匹配的key列，理想的两个key列名称不同等。

需要使用merge的更多参数进行设置。

#### 1) on

Most simply, you can explicitly specify the name of the key column using the on keyword, which takes a column name or a list of column names.

可以使用`on`参数，指定特定的列作为key。

```python
display('df1', df1, 'df2', df2)
display("pd.merge(df1, df2, on='employee')", pd.merge(df1, df2, on='employee'))
```

    'df1'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
    </tr>
  </tbody>
</table>
</div>

    'df2'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>hire_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Lisa</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bob</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Jake</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

    "pd.merge(df1, df2, on='employee')"

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
      <th>hire_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

#### 2) left_on & right_on

At times you may wish to merge two datasets with **different column names**. In this case, we can use the _left_on_ and _right_on_ keywords to specify the two column names.

两个key列的名称不同，使用*left_on*和*right_on*指明列名。

```python
df3 = pd.DataFrame({'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'salary': [70000, 80000, 120000, 90000]})
result = pd.merge(df1, df3, left_on='employee', right_on='name')
display('df1', df1, 'df3', df3,
        "pd.merge(df1, df3, left_on='employee', right_on='name')", result)
```

    'df1'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
    </tr>
  </tbody>
</table>
</div>

    'df3'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>70000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>80000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>120000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>90000</td>
    </tr>
  </tbody>
</table>
</div>

    "pd.merge(df1, df3, left_on='employee', right_on='name')"

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>employee</th>
      <th>group</th>
      <th>name</th>
      <th>salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>Accounting</td>
      <td>Bob</td>
      <td>70000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>Engineering</td>
      <td>Jake</td>
      <td>80000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>Engineering</td>
      <td>Lisa</td>
      <td>120000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>HR</td>
      <td>Sue</td>
      <td>90000</td>
    </tr>
  </tbody>
</table>
</div>

#### 3) left_index & right_index

Sometimes you would instead like to merge on an index.

有时候会直接使用索引进行合并。

```python
df1a = df1.set_index('employee')
df2a = df2.set_index('employee')
display('df1a', df1a, 'df2a', df2a)
```

    'df1a'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>group</th>
    </tr>
    <tr>
      <th>employee</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bob</th>
      <td>Accounting</td>
    </tr>
    <tr>
      <th>Jake</th>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>Lisa</th>
      <td>Engineering</td>
    </tr>
    <tr>
      <th>Sue</th>
      <td>HR</td>
    </tr>
  </tbody>
</table>
</div>

    'df2a'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>hire_date</th>
    </tr>
    <tr>
      <th>employee</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Lisa</th>
      <td>2004</td>
    </tr>
    <tr>
      <th>Bob</th>
      <td>2008</td>
    </tr>
    <tr>
      <th>Jake</th>
      <td>2012</td>
    </tr>
    <tr>
      <th>Sue</th>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

```python
display(pd.merge(df1a, df2a, left_index=True, right_index=True))
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>group</th>
      <th>hire_date</th>
    </tr>
    <tr>
      <th>employee</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bob</th>
      <td>Accounting</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>Jake</th>
      <td>Engineering</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>Lisa</th>
      <td>Engineering</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>Sue</th>
      <td>HR</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 或者直接调用dataframe对象的join方法，默认按照索引进行合并
df1a.join(df2a)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>group</th>
      <th>hire_date</th>
    </tr>
    <tr>
      <th>employee</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bob</th>
      <td>Accounting</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>Jake</th>
      <td>Engineering</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>Lisa</th>
      <td>Engineering</td>
      <td>2004</td>
    </tr>
    <tr>
      <th>Sue</th>
      <td>HR</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>

If you'd like to mix indices and columns, you can combine _left_index_ with _right_on_ or _left_on_ with _right_index_ to get the desired behavior. 可以混合使用索引和列名。

```python
pd.merge(df1a, df3, left_index=True, right_on='name')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>group</th>
      <th>name</th>
      <th>salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Accounting</td>
      <td>Bob</td>
      <td>70000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Engineering</td>
      <td>Jake</td>
      <td>80000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Engineering</td>
      <td>Lisa</td>
      <td>120000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HR</td>
      <td>Sue</td>
      <td>90000</td>
    </tr>
  </tbody>
</table>
</div>

### 2.3 Specifying Set Arithmetic for Joins: 合并规则

The type of set arithmetic used in the _join_: this comes up when a value appears in one key column but not the other. 一个值在一侧的key列中存在，但却不存在于另一侧的key列中。

```python
df6 = pd.DataFrame({'name': ['Peter', 'Paul', 'Mary'],
                    'food': ['fish', 'beans', 'bread']},
                   columns=['name', 'food'])
df7 = pd.DataFrame({'name': ['Mary', 'Joseph'],
                    'drink': ['wine', 'beer']},
                   columns=['name', 'drink'])
display('df6',df6)
display('df7',df7)
```

    'df6'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>food</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peter</td>
      <td>fish</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Paul</td>
      <td>beans</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mary</td>
      <td>bread</td>
    </tr>
  </tbody>
</table>
</div>

    'df7'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>drink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mary</td>
      <td>wine</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Joseph</td>
      <td>beer</td>
    </tr>
  </tbody>
</table>
</div>

```python
pd.merge(df6, df7)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>food</th>
      <th>drink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mary</td>
      <td>bread</td>
      <td>wine</td>
    </tr>
  </tbody>
</table>
</div>

#### 1) inner join: 取交集

上述合并的例子中，df6和df7以各自的name列为key，但是它们的key列中只有一个元素是相同的。

By default, the result contains the _intersection_ of the two sets of inputs, which is known as an _inner join_.

默认，取**交集**，即**inner join**。

We can specify this explicitly using the _how_ keyword, which defaults to _"inner"_.

设置**how**参数处理这种情况，默认是**inner**。

```python
pd.merge(df6, df7, how='inner')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>food</th>
      <th>drink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mary</td>
      <td>bread</td>
      <td>wine</td>
    </tr>
  </tbody>
</table>
</div>

#### 2) outer join: 取并集

An **outer join** returns a join over the _union_ of the input columns, and fills in all missing values with NAs.

```python
pd.merge(df6, df7, how='outer')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>food</th>
      <th>drink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peter</td>
      <td>fish</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Paul</td>
      <td>beans</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mary</td>
      <td>bread</td>
      <td>wine</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Joseph</td>
      <td>NaN</td>
      <td>beer</td>
    </tr>
  </tbody>
</table>
</div>

#### 3) left (outer) join

The _left join_ return joins over the _left_ entries: The output rows now correspond to the entries in the left input.

```python
pd.merge(df6, df7, how='left')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>food</th>
      <th>drink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peter</td>
      <td>fish</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Paul</td>
      <td>beans</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mary</td>
      <td>bread</td>
      <td>wine</td>
    </tr>
  </tbody>
</table>
</div>

```python
pd.merge(df7, df6, how='left')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>drink</th>
      <th>food</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mary</td>
      <td>wine</td>
      <td>bread</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Joseph</td>
      <td>beer</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

#### 4) right (outer) join

```python
pd.merge(df6, df7, how='right')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>food</th>
      <th>drink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mary</td>
      <td>bread</td>
      <td>wine</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Joseph</td>
      <td>NaN</td>
      <td>beer</td>
    </tr>
  </tbody>
</table>
</div>

### 2.4 Overlapping Column Names: suffixes参数

Maybe your two input DataFrames have conflicting column names.

两个数据有重复的列名（除了key列外）。

```python
df8 = pd.DataFrame({'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'rank': [1, 2, 3, 4]})
df9 = pd.DataFrame({'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'rank': [3, 1, 4, 2]})
display('df8', df8)
display('df9', df9)
```

    'df8'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>

    'df9'

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>

```python
pd.merge(df8, df9, on='name')
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>rank_x</th>
      <th>rank_y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>1</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>3</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>4</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>

The merge function automatically appends a suffix _\_x_ or _\_y_ to make the output columns unique.

If these defaults are inappropriate, it is possible to specify a custom suffix using the suffixes keyword:

```python
# 自定义后缀名
pd.merge(df8, df9, on='name', suffixes=['_1','_2'])
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: middle;">
      <th></th>
      <th>name</th>
      <th>rank_1</th>
      <th>rank_2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bob</td>
      <td>1</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jake</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lisa</td>
      <td>3</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sue</td>
      <td>4</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>
