---
title: Test
author: Jingwei Gao
pubDatetime: 2023-03-25T15:20:35+08
postSlug: test
featured: false
draft: true
tags:
  - test
description: This is a test This is a test This is a test This is a test This is a test This is a test This is a test This is a test
---

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import random
import warnings
import time
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.model_selection import StratifiedKFold
from sklearn.model_selection import RepeatedStratifiedKFold
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import GridSearchCV
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import AdaBoostClassifier
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.metrics import f1_score
from sklearn.metrics import roc_auc_score
from imblearn.pipeline import Pipeline
from imblearn.over_sampling import SMOTE
```

```python
# Genral settings
warnings.filterwarnings('ignore')
seed = 123
# Read the dataset
df = pd.read_csv("IBM.csv")
```

## Model Selection

#### 数据处理 & 特征工程

```python
# Transform categorical variables into numerical variables
df["Attrition"]      = df["Attrition"].map({"No":0,"Yes":1})
df["BusinessTravel"] = df["BusinessTravel"].map({"Non-Travel":0,"Travel_Rarely":1,"Travel_Frequently":2})
df["Gender"]         = df["Gender"].map({"Male":0,"Female":1})
df["OverTime"]       = df["OverTime"].map({"No":0,"Yes":1})
# Transform categorical variables into dummy variables
df = pd.concat([df, pd.get_dummies(df["Department"], prefix="Department")], axis=1)
df = pd.concat([df, pd.get_dummies(df["EducationField"], prefix="EducationField")], axis=1)
df = pd.concat([df, pd.get_dummies(df["JobRole"], prefix="JobRole")], axis=1)
df = pd.concat([df, pd.get_dummies(df["MaritalStatus"], prefix="MaritalStatus")], axis=1)
# Feature Engineering - ZR
df["JSD"] = (df["JobSatisfaction"]+df["RelationshipSatisfaction"]+df["WorkLifeBalance"])/((df["StockOptionLevel"]+1)+(df["YearsAtCompany"]+1)+df["MaritalStatus"].map({"Single":1, "Divorced":2, "Married":3}))
df["PJH"] = (df["NumCompaniesWorked"]+1)/(df["TotalWorkingYears"]+1)
df["CTD"] = df["DistanceFromHome"]/df["MonthlyIncome"]*1000
# Feature Engineering - ZY
df["SalaryHikePerYear"] = df["PercentSalaryHike"]/(df["YearsAtCompany"]+1)
df["JobPayment"] = (df["MonthlyIncome"] * (df["StockOptionLevel"]+1))/(df["DistanceFromHome"]*(df["BusinessTravel"]+1))
df["WorkStress"] = (df["OverTime"]+1)/df["WorkLifeBalance"]
def f(x):
    j1 = (x["Department"]=="Sales") and (x["EducationField"]=="Marketing")
    j2 = (x["Department"]=="Research & Development") and ((x["EducationField"]=="Life Sciences") or (x["EducationField"]=="Medical") or (x["EducationField"]=="Technical Degree"))
    j3 = (x["Department"]=="Human Resources") and (x["EducationField"]=="Human Resources")
    return (j1 or j2 or j3)
df["JobMismatch"] = (-df.apply(f, axis=1)).astype("int")
df["GrowthOpportunity"] = (df["TrainingTimesLastYear"]+1)/(df["YearsSinceLastPromotion"]+1)
# Drop some columns
df.drop(["MaritalStatus"], axis=1, inplace=True)
df.drop(["EmployeeCount"], axis=1, inplace=True)
df.drop(["EmployeeNumber"], axis=1, inplace=True)
df.drop(["Over18"], axis=1, inplace=True)
df.drop(["StandardHours"], axis=1, inplace=True)
df.drop(["Department"], axis=1, inplace=True)
df.drop(["EducationField"], axis=1, inplace=True)
df.drop(["JobRole"], axis=1, inplace=True)
# Standardization
scaler = StandardScaler()
not_num_cols = ["Attrition","Gender","OverTime",
                "Department_Human Resources","Department_Research & Development","Department_Sales",
                "EducationField_Human Resources","EducationField_Life Sciences","EducationField_Marketing",
                "EducationField_Medical","EducationField_Other","EducationField_Technical Degree",
                "JobRole_Healthcare Representative","JobRole_Human Resources","JobRole_Laboratory Technician",
                "JobRole_Manager","JobRole_Manufacturing Director","JobRole_Research Director","JobRole_Research Scientist",
                "JobRole_Sales Executive","JobRole_Sales Representative","MaritalStatus_Divorced","MaritalStatus_Married","MaritalStatus_Single","JobMismatch"]
num_cols = list(set(df.columns) - set(not_num_cols))
df[num_cols] = scaler.fit_transform(df[num_cols])
# Get X and y
X = df.drop(["Attrition"], axis=1).values
y = df["Attrition"].values
X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, random_state=seed, test_size=0.2)
```

#### 交叉验证

```python
time_start = time.time()
#################### CV & Oversampling ####################
# RepeatedStratifiedKFold
n_splits = 10
n_repeats = 5
rskf = RepeatedStratifiedKFold(n_splits=n_splits, n_repeats=n_repeats, random_state=seed)
# Oversampling setting
smote         = SMOTE(random_state=seed)
# A1: logistic regression
lr            = LogisticRegression()
lr_pipe       = Pipeline([("smote",smote),("lr",lr)])
lr_param_grid = {"lr__penalty":["l1", "l2"],
                 "lr__C":[0.01, 0.1, 1, 10, 100]}
lr_gs         = GridSearchCV(lr_pipe, param_grid=lr_param_grid, scoring="roc_auc" , cv=rskf, n_jobs=-1)
lr_gs.fit(X_train, y_train)
# A2: SVM
sv            = SVC(kernel="rbf")
sv_pipe       = Pipeline([("smote",smote),("sv",sv)])
sv_param_grid = {"sv__gamma":[0.00001, 0.0001, 0.001],
                 "sv__C":[0.01,0.1,1]}
sv_gs         = GridSearchCV(sv_pipe, param_grid=sv_param_grid, scoring="roc_auc" , cv=rskf, n_jobs=-1)
sv_gs.fit(X_train, y_train)
# A3: randomforest
rf            = RandomForestClassifier()
rf_pipe       = Pipeline([("smote",smote),("rf",rf)])
rf_param_grid = {"rf__n_estimators":[50,100,500,1000],
                 "rf__max_depth":[3,6,9]}
rf_gs         = GridSearchCV(rf_pipe, param_grid=rf_param_grid, scoring="roc_auc" , cv=rskf, n_jobs=-1)
rf_gs.fit(X_train, y_train)
# A4: AdaBoost
ad            = AdaBoostClassifier()
ad_pipe       = Pipeline([("smote",smote),("ad",ad)])
ad_param_grid = {"ad__n_estimators":[25,50,100,500],
                 "ad__learning_rate":[0.1,1,10]}
ad_gs         = GridSearchCV(ad_pipe, param_grid=ad_param_grid, scoring="roc_auc" , cv=rskf, n_jobs=-1)
ad_gs.fit(X_train, y_train)
# A5: GBDT
gb            = GradientBoostingClassifier()
gb_pipe       = Pipeline([("smote",smote),("gb",gb)])
gb_param_grid = {"gb__n_estimators":[50,100,500],
                   "gb__learning_rate":[0.01,0.1,1],
                   "gb__max_depth":[2,4,6]}
gb_gs         = GridSearchCV(gb_pipe, param_grid=gb_param_grid, scoring="roc_auc" , cv=rskf, n_jobs=-1)
gb_gs.fit(X_train, y_train)
# Result
print("##### Logistic Regression #####")
print(lr_gs.best_params_)
print(round(lr_gs.best_score_,4))
print("##### SVM #####")
print(sv_gs.best_params_)
print(round(sv_gs.best_score_,4))
print("##### Randomforest #####")
print(rf_gs.best_params_)
print(round(rf_gs.best_score_,4))
print("##### AdaBoost #####")
print(ad_gs.best_params_)
print(round(ad_gs.best_score_,4))
print("##### GBDT #####")
print(gb_gs.best_params_)
print(round(gb_gs.best_score_,4))
time_end = time.time()
# Time cost
print("Total time: %s min" % (time_end-time_start)//60)
```

    ##### Logistic Regression #####
    {'lr__C': 0.01, 'lr__penalty': 'l2'}
    0.8304
    ##### SVM #####
    {'sv__C': 1, 'sv__gamma': 0.001}
    0.8227
    ##### Randomforest #####
    {'rf__max_depth': 9, 'rf__n_estimators': 500}
    0.8102
    ##### AdaBoost #####
    {'ad__learning_rate': 0.1, 'ad__n_estimators': 500}
    0.8259
    ##### GBDT #####
    {'gb__learning_rate': 0.1, 'gb__max_depth': 2, 'gb__n_estimators': 100}
    0.8161



    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-4-c0aa3421dc4b> in <module>
         61 time_end = time.time()
         62 # Time cost
    ---> 63 print("Total time: %s min" % (time_end-time_start)//60)


    TypeError: unsupported operand type(s) for //: 'str' and 'int'

```python
print("Total time: %s min" % str((time_end-time_start)//60))
```

    Total time: 22.0 min

因此，最好的【模型&参数】组合是：

- Logistic Regression
- penalty=l2, C=0.01
- 平均的CV score是0.8304

## Feature Selection

```python
class GeneticSelector:
    def __init__(self, estimator, n_gen, size, n_best, n_rand, n_children, mutation_rate, scoring, cv):
        self.estimator = estimator
        # Number of genrations
        self.n_gen = n_gen
        # 总体(population)中染色体条数
        self.size = size
        # Number of best chromosomes to select in each generation
        self.n_best = n_best
        # Number of random chromosomes to select in each generation
        self.n_rand = n_rand
        # Number of children created for each pair
        self.n_children = n_children
        # Probablity of chromosome mutation
        self.mutation_rate = mutation_rate
        # Performance measure
        self.scoring = scoring
        # StratifiedKFold
        self.cv = StratifiedKFold(n_splits=cv, random_state=123)
        if  int((self.n_best + self.n_rand) / 2) * self.n_children != self.size:
            raise ValueError("The population size is not stable.")

    def initilize(self):
        """
        构造初始总体
        """
        population = []
        for i in range(self.size):
            # 首先将全部特征都选上(设为1)
            chromosome = np.ones(self.n_features_, dtype=np.bool)
            # 然后用抛有偏硬币的方式将一部分特征排除掉
            # 为什么是0.3？因为我们不希望大多数的特征都一开始就被排除掉了
            mask = np.random.rand(len(chromosome)) < 0.3
            chromosome[mask] = False
            population.append(chromosome)
        return population

    def fitness(self, population):
        """
        计算传入总体中各个染色体的适应度，并返回排序后的适应度值与总体染色体
        """
        X, y = self.dataset_
        scores = []
        # 计算每个染色体的CV-performance
        for chromosome in population:
            smote         = SMOTE(random_state=123)
            est_pipe      = Pipeline([("smote",smote),("est",self.estimator)])
            all_scores    = cross_val_score(est_pipe, X[:,chromosome], y, cv=self.cv, scoring=self.scoring)
            average_score = np.mean(all_scores)
            scores.append(average_score)
        scores, population = np.array(scores), np.array(population)
        # 从大到小
        inds = np.argsort(scores)[::-1]
        # 返回①：排序后的fitness的值；返回②：排序后的总体中的染色体
        return list(scores[inds]), list(population[inds,:])

    def select(self, population_sorted):
        """
        传入按照适应度由大到小排序过的总体染色体，返回被选择的染色体构成新的总体
        """
        population_next = []
        # n_best个适应度最高的染色体被选择
        for i in range(self.n_best):
            population_next.append(population_sorted[i])
        # 随机选择n_rand个染色体
        for i in range(self.n_rand):
            population_next.append(random.choice(population_sorted))
        # 打乱它们
        random.shuffle(population_next)
        # 可能有重复
        return population_next

    def crossover(self, population):
        """
        传入总体，返回它们交叉互换得到的子代；每一对（2个）染色体得到n_children个子代
        """
        population_next = []
        # 第一个和最后一个、第二个和倒数第二个……的交叉互换
        for i in range(int(len(population)/2)):
            # 每一对染色体生出n_children个子代
            for j in range(self.n_children):
                chromosome1, chromosome2 = population[i], population[len(population)-1-i]
                child = chromosome1
                # 子代每个位置50%的概率是来自父亲，50%的概率是来自母亲
                mask = np.random.rand(len(child)) > 0.5
                child[mask] = chromosome2[mask]
                population_next.append(child)
        # 注意，父母没有进入下一代，是它们的孩子进入了下一
        return population_next

    def mutate(self, population):
        """
        基因突变
        """
        population_next = []
        for i in range(len(population)):
            chromosome = population[i]
            # 决定该染色体是否发生基因突变
            if random.random() < self.mutation_rate:
                # 发生基因突变，则每个基因突变的概率为0.05，且突变是指基因反转
                mask = np.random.rand(len(chromosome)) < 0.05
                chromosome[mask] = ~chromosome[mask]
            population_next.append(chromosome)
        return population_next

    def generate(self, population):
        """
        一轮进化过程：计算适应度→选择→交叉互换→基因突变
        """
        scores_sorted, population_sorted = self.fitness(population)
        population = self.select(population_sorted)
        population = self.crossover(population)
        population = self.mutate(population)
        self.chromosomes_best_.append(population_sorted[0])
        self.scores_best_.append(scores_sorted[0])
        self.scores_avg_.append(np.mean(scores_sorted))
        return population

    def fit(self, X, y):
        self.chromosomes_best_ = []
        self.scores_best_      = []
        self.scores_avg_       = []
        self.dataset_          = X, y
        self.n_features_       = X.shape[1]
        population             = self.initilize()
        for i in range(self.n_gen):
            population = self.generate(population)
        return self
```

```python
estimator = LogisticRegression(penalty="l2", C=0.01)
geneticS = GeneticSelector(estimator=estimator,
                           n_gen=20,
                           size=200,
                           n_best=40,
                           n_rand=40,
                           n_children=5,
                           mutation_rate=0.1,
                           scoring="roc_auc",
                           cv=5)
geneticS.fit(X_train, y_train)
```

    <__main__.GeneticSelector at 0x2735fbf7e80>

```python
geneticS.scores_best_
```

    [0.8352416767191013,
     0.8352416767191013,
     0.8352416767191013,
     0.8371052226782926,
     0.8383866719560013,
     0.8383866719560013,
     0.8378264342597765,
     0.8382538988603642,
     0.8382538988603642,
     0.8395322447018947,
     0.839611314858443,
     0.840999495354291,
     0.840999495354291,
     0.840999495354291,
     0.8421457427602231,
     0.840999495354291,
     0.840999495354291,
     0.840999495354291,
     0.8415582487984304,
     0.842546490823274]

```python
plt.figure(figsize=(15,8))
plt.plot(geneticS.scores_best_, color="red", marker="o")
plt.xlabel("Generation")
plt.ylabel("Best score")
plt.xticks(range(len(geneticS.scores_best_)));
```

![png](/assets/modeling_13_0.png)

```python
scores_best = np.array(geneticS.scores_best_)
chromosomes_best = np.array(geneticS.chromosomes_best_)
best_features = chromosomes_best[scores_best.argsort()[::-1]][0]
print("Drop these features: \n%s" % ",\n".join(df.drop(["Attrition"], axis=1).columns[~best_features]))
```

    Drop these features:
    Age,
    Education,
    HourlyRate,
    MonthlyRate,
    OverTime,
    TotalWorkingYears,
    WorkLifeBalance,
    Department_Human Resources,
    JobRole_Healthcare Representative,
    JobRole_Human Resources,
    JobRole_Laboratory Technician,
    JobRole_Manager,
    GrowthOpportunity

## Test

```python
smote = SMOTE(random_state=123)
lr = LogisticRegression(penalty="l2", C=0.01)
lr_pipe      = Pipeline([("smote",smote),("lr",lr)])
lr_pipe.fit(X_test[:, best_features], y_test)
roc_auc_score(y_test, lr_pipe.predict_proba(X_test[:, best_features])[:,1])
```

    0.8604530967352916
