---
title: SPSS Tutorial
author: Jingwei Gao
pubDatetime: 2017-12-22T09:53:25+08
postSlug: spss-tutorial
featured: false
draft: false
tags:
  - spss
description: Class notes for the undergraduate course "Analytical Techniques of Data" & SPSS quick reference manual.
---

> Reference: 刘爱玉. (2017). SPSS数据分析教程. 北京: 北京大学出版社.

## Table of contents

## 〇、变量

- 定类尺度：Nominal Scale，对现象或状态进行**区分**，最低层次的尺度，只能进行等于或不等于的运算。
- 定序尺度：Ordinal Scale，能够决定之间的顺序，也就是不仅能进行等于或不等于的运算，还能比较，进行大于小于的运算。
- 定距尺度：Interval Scale，能够确定距离的大小，也就是能够进行加减运算。
- 定比尺度：Ratio Scale，最高尺度，有一个**绝对的零点**，能够进行乘除运算。

## 一、数据录入

**多项选择题的处理**：将其每个选项分别作为一个变量处理，编码0-1或1-2，代表是或不是（在1-2中1通常表示是，2表示不是）；
**缺失值**：年龄——99；六个栏位的年收入——999999；原则：不能跟其他编码相混淆；注意区别系统缺失值和用户缺失值，后者是用户自己设置的，前者大多是录入数据时空缺了某些数据，一般用`.`表示；
**不知道、不回答、不适用**：不知道——-1，不回答——-2，不适用——-3。

Lable——变量标签，也可以理解为变量的**名字**。
Values——值标签，用于对某个变量的各个取值添加标签。
Missing——用户缺失值，Discrete missing values，定义至多3个单一数为缺失值；Range plus one optional discrete missing value，定义指定范围为缺失值，同时还可以指定不在这一范围内的单一数为缺失值。
Measure——测量尺度，Scale表示**连续型变量**，Ordinal表示**定序型变量**，Nominal表示**定类变量**。

**使用命令录入数据：暂略**

**EpiData录入问卷**：

- Define Data：创建**数据描述文件**，扩展名为QES；
- Make Data File：建立**数据文件**：
  - 第一行一般都是id变量；
  - 每个变量必须有一个唯一的变量名，可以v1、v2……
  - 变量名后跟变量描述：`v1 您的性别`；
  - 变量类型：
    - 数值型：`#`表示录入变量值的长度，`.`表示小数点
    - 字符型：一个`_`可以录入一个字符，一个中文字需要占用2个字符
- Checks：检查
  - 设置合法值
  - 设置跳答：`1>v6`，若回答1则自动跳至变量v6
- Enter Data：
- Document：
- Export Data：

## 二、数据文件的编辑与管理

### 1、增加新的个案和变量

**增加新的变量**：先选中一行，然后在edit里点击insert variable，即可在选中行的前面添加一个新变量。

**增加新的个案**：同上，先选中一行，然后在edit里点击insert cases。

### 2、删除

选中后delete或clear

### 3、查找和替换

Find，在一列中寻找特定的数值；
Replace：进行替换。

### 4、排序

Data——Sort Cases

### 5、数据文件的合并

- 从外部数据文件中增加个案到当前数据文件：Data——Merge Files——Add Cases……
- 从外部数据文件中增加变量到当前数据文件：Data——Merge Files——Add Variables……需要注意，要选择`Match cases on key variables`，然后将`Excluded Variables`中的某个变量拖到`Key Variables`中。

### 6、分类汇总

含义：按照指定分类变量的各个取值对观察值进行**分组**，并对每组观察值的某个或几个变量求描述统计。

eg：每个省份居民的平均月工资

`Data`——`Aggregate`——将表示省份的变量拖进`Break Variable`中，将月工资拖到`Summaries of Variable`中，默认处理函数为平均值，可以自己修改。

### 7、选择个案

`Select Cases`

- `If condition is satisfied`：符合某一条件；
- `Random sample of cases`：从当前数据集随机抽取一个子样本。

### 8、加权

`Data`——`Weight cases by`，将频次变量拖到框中。

## 三、数据变换

### 1、Recode

#### 1）使用对话框

- **Transform**
- Recode into Different Variables（Same是会覆盖原来变量）
- 选择需要重新编码的变量
- Name：新变量的名字
- Lable：描述
- 点击change
- 单击Old and New Values
- 点击range，输入起始数值；在through后输入结束数值；
- 右侧点Value，输入一个数值（意思是把左边那个范围的数值都归为这一类）
- Add
- 重复上述步骤
- 最后（40以上的一组），点range value through HIGHEST，输入40；
- All other values，右侧System-missing，Add。

### 2）运用命令

`RECODE var_old (old_value=new value) ... (old_value=new value) INTO var_new`

其中`(old_value=new value)`：

- `(16 thru 34=1)`
- `(66 thru Highest=3)`
- `(ELSE=SYSMIS)`

### 2、Compute

- **Transform**
- Compute Variables
- 在Target Variable下面输入新变量的名字
- 在Numeric Expression中输入计算函数
- OK

**常用函数介绍（Function group）**

- Abs
- Arsin：反正弦
- Artan：反正切
- Cos：余弦
- Exp：自然对数指数函数
- Lg10：以10为底对数
- Ln：自然对数
- Mod：除以10后的余数
- Rnd：取自变量最接近的整数
- Sin：正弦函数
- Sqrt：平方根
- Trunc：截断

对于**缺失值**：

- 原变量某个个案有缺失值，该个案自动被分配为系统缺失值；
- 若某个变量的值为非法值或无效值，自动定为系统缺失值。
  ——总之，不合法的、缺失的，最后都变成**系统缺失值**

**代码形式**

```
COMPUTE total = h1 + h2 + h3
EXECUTE
```

### 3、Count

统计若干变量出现某个值或同时出现一组值的频次。

eg：

- 计算受访者父亲的文化程度和母亲的文化程度均在初中及以下的人数；
- 计算受访者父亲文化程度是大学本科及以上而母亲文化程度是初中及以下的受访者人数——需要Paste出来修改。

**步骤**：

- Tansform
- Count
- Target Variable，新变量的名字
- 选择需要计次的变量到Numeric Variables
- 单击Define Values
- range（输入两个，代表两个分别的范围）

对于第二个eg，paste得到：
`Count fmedu = feduc meduc(1 thru 6) feduc meduc(13 thru 16)`
修改：
`Count fmedu = meduc(1 thru 6) feduc(13 thru 16)`

## 四、频次表与描述统计分析

### 1、生成频次统计表

`Analyze`——`Descriptive Statistics`——`Frequencies`

`Format`中可以选择输出的顺序，按变量值升降序或按频次值升降序。

### 2、完成描述统计

`Statistic`选项

![spss-t-1.png](/assets/spss-t-1.png)

- **Percentile：分位数计算**
  - 四分位数，Quartiles，Q1，Q2，Q3；
  - 数据等分，Cut Points for Equal Groups，默认十等分，D1到D9；
  - 百分位数，Percentiles，等分为100份；
- **Central Tendency：集中趋势度量**
  - 均值，Mean——定距变量最好，一般不用于测量定类和定序变量，受极端值变化影响大；
  - 中位数，Median——定序变量最好，受极端值影响小；
  - 众数，Mode——定类变量最好，有时对个别值的变动也很敏感；
  - 求和，Sum
- **Dispersion：离散趋势度量**
  - 标准差，Std. deviation，越大表示变量离散的程度越大，均值的代表性越小；
  - 方差，Variance，标准差的平方；
  - 极差，Range，最大最小值的差距
  - 最小值，Minimum
  - 最大值，Maximum
  - 均值标准误，S.E. mean = `s/根号n`，绝对值小于等于2，可以认为观察值均值与假定均值相等。
  - 异众比，Variation Ratio，反映不同于众值的数在全体数值中所占的比例，用于测量定类变量的离散趋势，`(n-f)/n`，n为样本量，f是众值的频次。
  - 四分互差，Interquartile Range，Q = Q3-Q1。
- **Distribution：数据分布描述**
  - 描述数据的分布与正态分布的差异程度；
  - 偏态系数（偏度），Skewness，正态分布为0；正数，正向偏态，样本有较长的右尾；负数，负向偏态，较长的左尾；
  - 峰态系数（峰度），Kurtosis，正态分布为0；正数，高峰态，分布的峰高，瘦高；负数，低峰态，分布的峰低，扁平。

### 3、简单作图

条形图、饼图、直方图

`Chart`

- Bar charts，条形图
- Pie charts，饼图
- Histograms，直方图

### 4、标准分

`Analyze`——`Descriptives Statistics`——`Descriptives`

`Save standardized values as variables`，计算变量的标准分数。

### 5、探索性分析

暂略

## 五、交互表与关联分析

Crosstabs命令，主要用于研究两个变量之间是相互独立还是存在某种关系，如果有关系，关系强弱如何。

**交互表**，contingency table，也叫列联表，就是同时根据两个变量的值，将所研究的对象分类。

**一般将自变量设为列（放在上面），将因变量设为行（放在左侧）**。

### 1、交互表的生成与解读

`Analyze` → `Descriptive Statistics` → `Crosstabs`

- **实际观察值**：Count，样本中有多少人；
- **行百分比**：固定行，列的这一类占总列类的百分比；
- **列百分比**：固定列，行的这一类占总行类的百分比；
- **总百分比**：占总样本的百分比；

### 2、期望值、百分比和残差

在Crossbats主对话框中的Cells选项中设置。

#### 1）期望值

行和列在统计上相互独立或不相关时，在单元格中期望的观察值的数量。

**计算方法**：两个相应边缘次数的乘积除以样本的大小。

Cell → Expected

#### 2）百分比

- 录入交互表；
- 对数据进行加权——Data → Weight Cases → 选中频次进入Weight cases by；
- Crosstabs；
- Cell → Column；

#### 3）残差

Residuals

**残差 = 观察值 - 期望值**

三种残差：

- Unstandardized：非标准化；
- Standardized：残差除以标准差，均值是0，标准差等于1；
- Adj standardized：调整后的残差。

### 3、定类—定类变量的交互分析

#### 1）是否相关

**卡方检验（Pearson Chi-Square）**：原假设两个变量之间没有关系，备择假设是两个变量之间有关系。

`Sig. <= 0.05`则拒绝原假设，两个变量相关；
`Sig. >= 0.05`则接受原假设，两个变量不相关；

2\*2列联表，需要对卡方进行修正。

**注意**：

- 卡方是随机变量；
- 一般要求样本量大于30；
- 不相关的两个变量，随着样本量的增大，卡方检验的结果可能相关；
- 辅助方法：使用列联系数**C值**，C值大于0.16，最好大于0.25才能拒绝原假设；
- 列联表中期望值小于5的单元格不能太多；
- 卡方检验最适合检验定类变量之间的关系。

Crosstabs → Statistics → Chi-Square

#### 2）相关强度——各种相关系数

**无消减误差，且假定两个变量是对称的**：

- Phi系数，常用于2\*2；
- 列联系数，C值，超过2\*2；
- Cramer's V系数；

- **Lambda系数**：计算以一个定类变量的值来预测另一个定类变量的值时，如果以众值作为预测的准则，可以消减多少误差——消减的误差在全部误差中所占的比例越大，两个变量间的相关程度越大；
  - 对称形式：不分自变量因变量；
  - 非对称形式：一个是自变量，另一个是因变量；
- **Tau-y系数：只适合分析不对称的关系**。

简单总结：

- 对称关系：Lambda系数；
- 不对称关系：Tau-y系数。

### 4、定序—定序变量的交互分析

#### 1、相关系数

- Gamma系数（最常用）：**对称**，具有消减误差比例的意义，-1到1；
- Somers'd 系数：**不对称**；

### 5、Kappa选项

两个变量之间的Cohen's Kappa系数——检验两个评估人对于同一对象的评估是否具有一致性。

0到1之间——越接近1，说明两个人的评估越一致；越接近0，说明两个人的评估差异越大。

### 6、Risk选项

检验某个事件的发生与某个因素之间的关系。

Odd Ratio：等于1，说明不同情况下，风险率相同。

**最后注意**：*定类－定序*或者*定序－定类*，在分析时一般按照**定类－定类分析**。

## 六、引入其他变量的交互分析

三种方法：

- 因果分析
- 阐明分析
- 条件分析

### 1、因果分析：排除虚假相关

自变量X与因变量Y之间是否真的有因果关系。

例如：结婚年数与发病率之间虽然存在正比关系，但其实两者并没有因果关系，而年龄可能是因果链条中对于发病率更好的解释因素。

做法：**引入其他变量**——**前置变量**或**控制变量**，T

**分表法**：依据前置变量T的值，将样本个案分组，然后在每组中分析自变量X与因变量Y之间的关系。

控制前置变量后，再探寻X与Y之间的关系，可能出现三种情况：

- 各分表中，X与Y都没有关系——X与Y之间的关系虚假；
- 关系保持不变，即各分表中的相关程度和原表的大致相同——因果关系未被否定，至少不是由控制变量引起的；
- 仍有关系，但减弱——X与Y之间的关系可能是真实的，但有控制变量的影响。

### 2、阐明分析：发现中介变量

自变量X通过某些因素T，对因变量Y产生影响。

三种情况：

- 完全阐明：自变量X完全通过引入的第三类变量T影响因变量Y——分表的相关系数接近0；
- 不能阐明：自变量X完全不是通过引入的第三类变量T影响因变量Y；
- 部分阐明：自变量X部分通过引入的第三类变量T影响因变量Y；

### 3、条件分析：排除压抑变量

不同的情况下，自变量X与因变量Y会有不同的关系吗？这里不同的情况——**条件变量**

原表相关系数等于0或不显著，但引入条件变量吼，分表相关系数不等于0或统计显著——条件变量为**压抑变量**，抑制了原变量间在某些条件下会出现的相关。

## 七、子总体均值比较与检验

`Analyze`——`Compare Means`

其下有**五个过程**：

- Means：子总体均值比较
- One-Sample T Test：单样本T检验
- Independent-Samples T Test：独立样本T检验
- Paired-Samples T Test：配对样本T检验
- One-Way ANOVA：单因数方差分析

### 1、子总体均值比较（Means）

- 不同群体的基本情况：

  - 子群体的个案有多少？
  - 各个 子群体的汇总特征？

- Analyze——Compare Means——Means
- 主要功能：
  - Dependent：因变量，定距变量
  - Independent：自变量，定类或定序变量
  - Layer 1 of 1：控制变量，定类或定序
  - Options：对统计量进行选择

### 2、单样本T检验

检验单个变量的均值与假设检验值之间是否存在差异。

补充知识（关于统计学）：

- 样本是总体的一个子集；
- 总体参数；
- 样本统计量；
- 样本统计量 → 总体参数，统计推断；
- 抽样分布：对总体进行重复抽样，每次用同样的公式计算样本统计量，构成一个分布；
- 点估计，区间估计。

- Analyze——Compare Means——One-Sample T Test（置信区间的默认值是95%）；
- 在Test Variables中拖入需要检验的变量；
- 在Test Value中输入假设检验值；
- 选择默认的置信区间和缺失值处理方式；

关于检验结果：

- 显著度：1-置信区间，如置信区间是95%，则显著度是0.05，表示否定域占整个抽样分布区间的比例；显著度越小，越难否定原假设——其实是抽样分布一端或两端的小尾巴，若样本的统计值落在这个范围，则否定原假设；
- 在看t值是否显著，需要将`Sig.`与`α`比较：
  - 若`Sig. ≤ α`，则否定原假设，接受研究假设；
  - 若`Sig. ≥ α`，则不否定原假设。

关于单尾和双尾：

- `Sig.`后有个括号，单尾是`1-tailed`，双尾是`2-tailed`；
- 如果理论能够确定方向，则用单尾，否则用双尾；

### 3、独立样本T检验

独立样本：所有的观察值都独立的样本。

独立样本T检验主要用来检验两个子总体的均值是否相等；在研究中，主要用于检验两个变量之间的关系。

- Analyze——Compare Means——Independent-Samples T Test；
- 选择变量及对分组进行界定：
  - 首先将Test Variable选择好；
  - 将分组变量选入Grouping Variable中；
  - 单击Define Group，配置Group1和Group2；
- OK。

关于检验结果：

- Levene's Test for Equality of Variances，检验子总体之间的方差是否相等：
  - 若`Sig. ≤ α`，则子总体方差不相等；
  - 若`Sig. ≥ α`，则子总体方差相等。
- 若子总体方差不相等，看`Equal variance not assumed`的T检验结果：
  - 再看Sig，过小，则否定原假设。

**对超过两个组的，可以使用Cut Point对自变量进行重新处理**，在Cut Point后输入a，则分成：

- 大于等于a；
- 小于a。

### 4、配对样本T检验

成对样本实际上只有一个样本，但样本的每一个个体都被研究了两次。

eg：

- 参加减肥训练班的10个学员的体重，**训练前**和**训练后**；
- 某个地区居民的生活水平，**改革前**和**改革后**；

- Analyze——Compare Means——Paired-Samples T Test；
- 选择Paired Variables；
- OK

关于检验结果：

- 看Sig

## 八、方差分析（One-Way ANOVA）

目的：检验因变量各个组的均值是否有所不同（而不是检验方差是不是相同）

### 1、原理

**方差分解**：

- 总方差 = 组内方差 + 组间方差；
- 总方差 = 随机因素引起的方差 + 处理因素引起的方差；
- 组内方差只反映随机因素引起的方差，组间方差同时反映随机因素和处理因素的影响。

**方差齐性**：

- 各组的方差相等；

### 2、单因素方差分析

`Analyze`——`Compare Means`——`One-Way ANOVA`

在`Dependent List`中防止因变量，在`Factor`中防止自变量，`Options`选择统计量：

- `Homogeneity of variance test`，同方差检验，看显著性是否大于0.05，大于则方差相等；
- `Post Hoc`，方差相等或不相等时候的检验选择：
  - 相等：`LSD`
  - 不相等：`Dunnett's T3`

步骤：

1. 先看满不满足方差齐性；
2. 再看方差分析表，看看是不是**至少两组的均值存在差异**；
3. 检验到底是哪些组的均值存在差异（显著的类别差异会在其均值差值右上角标注星号）；

### 3、双因素方差分析

双因素方差分析所关注的问题：**究竟是一个因素在起作用，还是两个因素都起作用，或是两个因素的影响都不显著？**

**两种类型**：

- 无交互作用的双因素方差分析：A因素和B因素的效应相互独立；
- 有交互作用的双因素方差分析：A因素和B因素相互作用，产生新的作用；

**p212，例子**

`Analyze`——`General Linear Model`——`Univariate`

1. 将因变量选到`Dependent Variable`中，将自变量选到`Fixed Factors`中；
2. 然后还是进行同方差检验；
3. 选择模型，点击`Model`，选择饱和模型`Full factorial`;
4. 读结果，看A，看B，再看A\*B——得到初步结果；
5. 在`Univariate`中点击`Post Hoc`，选择A因素和B因素到框中，选择`Bonferroni`统计量，输出组间比较结果——进一步的结果。

## 九、相关分析（Correlate）

### 1、双变量相关分析（Bivariate Correlation）

`Analyze`——`Correlate`——`Bivariate`

- 将需要进行相关分析的变量放到Variable里面，`Option`选择描述统计和缺失值处理方式；
- 选择相关系数，一般用Pearson即可；
- 一般用双尾检验；
- `Flag significant correlations`：标记显著的相关系数，一个星号表示0.05，两个表示0.01。

Sig小于0.05，相关系数在0.05显著度上具有统计学意义，一个星号；小于0.01，在0.01显著度上具有统计学意义，两个星号。

### 2、偏相关分析（Partial Correlations）

分析任意两个变量的相关时，控制第三个变量。

原理，先算三个变量两两间的简单相关系数，然后计算偏相关系数。

`Analyze`——`Correlate`——`Partial`

将结果与简单相关分析的结果**作比较**。

### 3、距离分析（Distances）

计算变量之间或观察单位之间的**相似性**。

`Analyze`——`Correlate`——`Distances`

暂略

## 十、线性回归分析（Linear Regression）

### 1、一元线性回归模型

`Y = A + BX + e`

拟合直线是：`y_hat = a + bx`

标准回归方程：`y_hat = βx`，其中`β`为**标准回归系数**，表示其他变量不变时，自变量变化一个标准差单位，因变量的标准差的平均变化。

**假定条件**：

- 独立：随机变量Yi在统计上相互独立，模型的误差项相互独立，误差项与自变量之间相互独立；
- 线性：因变量Y的所有子总体的均值都在一条直线上；
- 正态：误差项e服从正态分布；
- 等方差对于自变量的每一个值，y的所有子总体方差相等。

### 2、实例

做**散点图**：
`Graphs`——`Legacy Dialogs`——`Scatter/Dot`——`Simple Scatter`——`Define`

做**线性回归**：
`Analyze`——`Regression`——`Linear`：

- Dependent框，放置因变量；
- Independent框，放置自变量；
- 变量进入方式，一般用Enter；
- Statistic，选择统计量；
  - estimates：回归系数，主要看B值和Beta值
  - confidence intervals：求回归系数的置信区间
  - 模型拟合度：
    - `model fit`是默认有的；
    - 相关系数`R`：预测效果的好坏程度；
    - 判定系数`R Square`：计算方法`(TSS-ESS)/TSS`。总平方和，TSS，总误差；回归平方和，RSS，通过回归直线被解释掉的误差；余差平方和，ESS，回归直线未能解释的误差。R方越大，拟合度越好；
    - 修正的R2`Adj R Square`：样本规模对R2有影响，样本越大R2会越大，要修正。
- Plots，输出图形；
- Save，保存回归分析结果

**回归分析假定条件的检验**：
正态分布检验
线性回归选项框——`Plots`——`ZRESID`标准化误差频数到Y，`ZPRED`标准化误差到X——选择`Histogram`，输出**标准化误差直方图**，选择`Normal probability plot`，输出**标准化误差正态概率散点图**。

方差相等检验
**标准化误差和标准化预测值的散点图**，看横轴上各散点的纵向分布宽度是否相等

是否存在特异值
看**标准化误差和标准化预测值的散点图**，如果存在超出正负3区间的标准化误差值，便可认为存在特异值。

是否存在误差序列相关
`Statistic`中的`Durbin-Waston`，不存在序列相关的时候，值在2左右；偏向0，则正自相关；偏向4，则负自相关。
