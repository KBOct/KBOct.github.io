---
title: "Porto Seguro Kaggle challenge (French) - Part 1"
date: 2019-02-01
categories: [data science, python]
tags: [machine learning, data science, eda, exploratory data analysis, kaggle, porto seguro, python]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Porto Seguro Kaggle challenge"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

Les consignes originales du défi Kaggle :

**1. Data Description**

In this competition, you will predict the probability that an auto insurance policy holder files a claim.

In the train and test data, features that belong to similar groupings are tagged as such in the feature names (e.g., `ind` , `reg`, `car`, `calc`). In addition, feature names include the postfix bin to indicate binary features and cat to indicate categorical features. Features without these designations are either continuous or ordinal. Values of -1 indicate that the feature was missing from the . The target columns signifies whether or not a claim was filed for that policy holder.

**2. File descriptions**

- `train.csv` contains the training data, where each row corresponds to a policy holder, and the target columns signifies that a claim was filed.
- `test.csv` contains the test data.

**3. Aim**

- Build a classifier using the training dataset that leads to a good ROC and Precision / Recall curve on the testing set
- The notebook should describe your steps, explain what you do and should run entirely without bugs. It should contain some descriptive statistics and quick study, to understand some things about the data...
- It must end with plots of the ROC and precision/recall curves obtained on the testing dataset


# Analyse exploratoire des données


## 1)	Etude des données brutes


### a) Importation des données


```python
import os
import random
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
from collections import Counter
from sklearn.impute import SimpleImputer
from sklearn.base import BaseEstimator, TransformerMixin
from sklearn.ensemble import ExtraTreesClassifier
from sklearn.ensemble import AdaBoostClassifier, GradientBoostingClassifier
from sklearn.tree import DecisionTreeClassifier, ExtraTreeClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.model_selection import train_test_split, GridSearchCV, cross_val_score, StratifiedKFold, RandomizedSearchCV
from sklearn.metrics import roc_curve, roc_auc_score, auc, precision_recall_curve, f1_score
from sklearn.metrics.scorer import make_scorer
from sklearn.svm import SVC
from sklearn.linear_model import LogisticRegression, LogisticRegressionCV
from xgboost.sklearn import XGBClassifier
from sklearn.decomposition import PCA
import warnings
warnings.filterwarnings('ignore')

df = pd.read_csv("../input/data/train.csv")
df_test = pd.read_csv("../input/data/test.csv")
```

<a id='add'></a>
### b) Analyse descriptive des données


```python
df.head(5)
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
      <th>id</th>
      <th>target</th>
      <th>ps_ind_01</th>
      <th>ps_ind_02_cat</th>
      <th>ps_ind_03</th>
      <th>ps_ind_04_cat</th>
      <th>ps_ind_05_cat</th>
      <th>ps_ind_06_bin</th>
      <th>ps_ind_07_bin</th>
      <th>ps_ind_08_bin</th>
      <th>ps_ind_09_bin</th>
      <th>ps_ind_10_bin</th>
      <th>ps_ind_11_bin</th>
      <th>ps_ind_12_bin</th>
      <th>ps_ind_13_bin</th>
      <th>ps_ind_14</th>
      <th>ps_ind_15</th>
      <th>ps_ind_16_bin</th>
      <th>ps_ind_17_bin</th>
      <th>ps_ind_18_bin</th>
      <th>ps_reg_01</th>
      <th>ps_reg_02</th>
      <th>ps_reg_03</th>
      <th>ps_car_01_cat</th>
      <th>ps_car_02_cat</th>
      <th>ps_car_03_cat</th>
      <th>ps_car_04_cat</th>
      <th>ps_car_05_cat</th>
      <th>ps_car_06_cat</th>
      <th>ps_car_07_cat</th>
      <th>ps_car_08_cat</th>
      <th>ps_car_09_cat</th>
      <th>ps_car_10_cat</th>
      <th>ps_car_11_cat</th>
      <th>ps_car_11</th>
      <th>ps_car_12</th>
      <th>ps_car_13</th>
      <th>ps_car_14</th>
      <th>ps_car_15</th>
      <th>ps_calc_01</th>
      <th>ps_calc_02</th>
      <th>ps_calc_03</th>
      <th>ps_calc_04</th>
      <th>ps_calc_05</th>
      <th>ps_calc_06</th>
      <th>ps_calc_07</th>
      <th>ps_calc_08</th>
      <th>ps_calc_09</th>
      <th>ps_calc_10</th>
      <th>ps_calc_11</th>
      <th>ps_calc_12</th>
      <th>ps_calc_13</th>
      <th>ps_calc_14</th>
      <th>ps_calc_15_bin</th>
      <th>ps_calc_16_bin</th>
      <th>ps_calc_17_bin</th>
      <th>ps_calc_18_bin</th>
      <th>ps_calc_19_bin</th>
      <th>ps_calc_20_bin</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>5</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>11</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.7</td>
      <td>0.2</td>
      <td>0.718070</td>
      <td>10</td>
      <td>1</td>
      <td>-1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>12</td>
      <td>2</td>
      <td>0.400000</td>
      <td>0.883679</td>
      <td>0.370810</td>
      <td>3.605551</td>
      <td>0.6</td>
      <td>0.5</td>
      <td>0.2</td>
      <td>3</td>
      <td>1</td>
      <td>10</td>
      <td>1</td>
      <td>10</td>
      <td>1</td>
      <td>5</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>8</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0.8</td>
      <td>0.4</td>
      <td>0.766078</td>
      <td>11</td>
      <td>1</td>
      <td>-1</td>
      <td>0</td>
      <td>-1</td>
      <td>11</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>19</td>
      <td>3</td>
      <td>0.316228</td>
      <td>0.618817</td>
      <td>0.388716</td>
      <td>2.449490</td>
      <td>0.3</td>
      <td>0.1</td>
      <td>0.3</td>
      <td>2</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>8</td>
      <td>1</td>
      <td>7</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13</td>
      <td>0</td>
      <td>5</td>
      <td>4</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>12</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.000000</td>
      <td>7</td>
      <td>1</td>
      <td>-1</td>
      <td>0</td>
      <td>-1</td>
      <td>14</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>60</td>
      <td>1</td>
      <td>0.316228</td>
      <td>0.641586</td>
      <td>0.347275</td>
      <td>3.316625</td>
      <td>0.5</td>
      <td>0.7</td>
      <td>0.1</td>
      <td>2</td>
      <td>2</td>
      <td>9</td>
      <td>1</td>
      <td>8</td>
      <td>2</td>
      <td>7</td>
      <td>4</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>16</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>8</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.9</td>
      <td>0.2</td>
      <td>0.580948</td>
      <td>7</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>11</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>1</td>
      <td>104</td>
      <td>1</td>
      <td>0.374166</td>
      <td>0.542949</td>
      <td>0.294958</td>
      <td>2.000000</td>
      <td>0.6</td>
      <td>0.9</td>
      <td>0.1</td>
      <td>2</td>
      <td>4</td>
      <td>7</td>
      <td>1</td>
      <td>8</td>
      <td>4</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>4</td>
      <td>9</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>17</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.7</td>
      <td>0.6</td>
      <td>0.840759</td>
      <td>11</td>
      <td>1</td>
      <td>-1</td>
      <td>0</td>
      <td>-1</td>
      <td>14</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>82</td>
      <td>3</td>
      <td>0.316070</td>
      <td>0.565832</td>
      <td>0.365103</td>
      <td>2.000000</td>
      <td>0.4</td>
      <td>0.6</td>
      <td>0.0</td>
      <td>2</td>
      <td>2</td>
      <td>6</td>
      <td>3</td>
      <td>10</td>
      <td>2</td>
      <td>12</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 595212 entries, 0 to 595211
    Data columns (total 59 columns):
    id                595212 non-null int64
    target            595212 non-null int64
    ps_ind_01         595212 non-null int64
    ps_ind_02_cat     595212 non-null int64
    ps_ind_03         595212 non-null int64
    ps_ind_04_cat     595212 non-null int64
    ps_ind_05_cat     595212 non-null int64
    ps_ind_06_bin     595212 non-null int64
    ps_ind_07_bin     595212 non-null int64
    ps_ind_08_bin     595212 non-null int64
    ps_ind_09_bin     595212 non-null int64
    ps_ind_10_bin     595212 non-null int64
    ps_ind_11_bin     595212 non-null int64
    ps_ind_12_bin     595212 non-null int64
    ps_ind_13_bin     595212 non-null int64
    ps_ind_14         595212 non-null int64
    ps_ind_15         595212 non-null int64
    ps_ind_16_bin     595212 non-null int64
    ps_ind_17_bin     595212 non-null int64
    ps_ind_18_bin     595212 non-null int64
    ps_reg_01         595212 non-null float64
    ps_reg_02         595212 non-null float64
    ps_reg_03         595212 non-null float64
    ps_car_01_cat     595212 non-null int64
    ps_car_02_cat     595212 non-null int64
    ps_car_03_cat     595212 non-null int64
    ps_car_04_cat     595212 non-null int64
    ps_car_05_cat     595212 non-null int64
    ps_car_06_cat     595212 non-null int64
    ps_car_07_cat     595212 non-null int64
    ps_car_08_cat     595212 non-null int64
    ps_car_09_cat     595212 non-null int64
    ps_car_10_cat     595212 non-null int64
    ps_car_11_cat     595212 non-null int64
    ps_car_11         595212 non-null int64
    ps_car_12         595212 non-null float64
    ps_car_13         595212 non-null float64
    ps_car_14         595212 non-null float64
    ps_car_15         595212 non-null float64
    ps_calc_01        595212 non-null float64
    ps_calc_02        595212 non-null float64
    ps_calc_03        595212 non-null float64
    ps_calc_04        595212 non-null int64
    ps_calc_05        595212 non-null int64
    ps_calc_06        595212 non-null int64
    ps_calc_07        595212 non-null int64
    ps_calc_08        595212 non-null int64
    ps_calc_09        595212 non-null int64
    ps_calc_10        595212 non-null int64
    ps_calc_11        595212 non-null int64
    ps_calc_12        595212 non-null int64
    ps_calc_13        595212 non-null int64
    ps_calc_14        595212 non-null int64
    ps_calc_15_bin    595212 non-null int64
    ps_calc_16_bin    595212 non-null int64
    ps_calc_17_bin    595212 non-null int64
    ps_calc_18_bin    595212 non-null int64
    ps_calc_19_bin    595212 non-null int64
    ps_calc_20_bin    595212 non-null int64
    dtypes: float64(10), int64(49)
    memory usage: 267.9 MB



```python
df.describe()
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
      <th>id</th>
      <th>target</th>
      <th>ps_ind_01</th>
      <th>ps_ind_02_cat</th>
      <th>ps_ind_03</th>
      <th>ps_ind_04_cat</th>
      <th>ps_ind_05_cat</th>
      <th>ps_ind_06_bin</th>
      <th>ps_ind_07_bin</th>
      <th>ps_ind_08_bin</th>
      <th>ps_ind_09_bin</th>
      <th>ps_ind_10_bin</th>
      <th>ps_ind_11_bin</th>
      <th>ps_ind_12_bin</th>
      <th>ps_ind_13_bin</th>
      <th>ps_ind_14</th>
      <th>ps_ind_15</th>
      <th>ps_ind_16_bin</th>
      <th>ps_ind_17_bin</th>
      <th>ps_ind_18_bin</th>
      <th>ps_reg_01</th>
      <th>ps_reg_02</th>
      <th>ps_reg_03</th>
      <th>ps_car_01_cat</th>
      <th>ps_car_02_cat</th>
      <th>ps_car_03_cat</th>
      <th>ps_car_04_cat</th>
      <th>ps_car_05_cat</th>
      <th>ps_car_06_cat</th>
      <th>ps_car_07_cat</th>
      <th>ps_car_08_cat</th>
      <th>ps_car_09_cat</th>
      <th>ps_car_10_cat</th>
      <th>ps_car_11_cat</th>
      <th>ps_car_11</th>
      <th>ps_car_12</th>
      <th>ps_car_13</th>
      <th>ps_car_14</th>
      <th>ps_car_15</th>
      <th>ps_calc_01</th>
      <th>ps_calc_02</th>
      <th>ps_calc_03</th>
      <th>ps_calc_04</th>
      <th>ps_calc_05</th>
      <th>ps_calc_06</th>
      <th>ps_calc_07</th>
      <th>ps_calc_08</th>
      <th>ps_calc_09</th>
      <th>ps_calc_10</th>
      <th>ps_calc_11</th>
      <th>ps_calc_12</th>
      <th>ps_calc_13</th>
      <th>ps_calc_14</th>
      <th>ps_calc_15_bin</th>
      <th>ps_calc_16_bin</th>
      <th>ps_calc_17_bin</th>
      <th>ps_calc_18_bin</th>
      <th>ps_calc_19_bin</th>
      <th>ps_calc_20_bin</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
      <td>595212</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>7.438036e+05</td>
      <td>0.036448</td>
      <td>1.900378</td>
      <td>1.358943</td>
      <td>4.423318</td>
      <td>0.416794</td>
      <td>0.405188</td>
      <td>0.393742</td>
      <td>0.257033</td>
      <td>0.163921</td>
      <td>0.185304</td>
      <td>0.000373</td>
      <td>0.001692</td>
      <td>0.009439</td>
      <td>0.000948</td>
      <td>0.012451</td>
      <td>7.299922</td>
      <td>0.660823</td>
      <td>0.121081</td>
      <td>0.153446</td>
      <td>0.610991</td>
      <td>0.439184</td>
      <td>0.551102</td>
      <td>8.295933</td>
      <td>0.829931</td>
      <td>-0.504899</td>
      <td>0.725192</td>
      <td>-0.157732</td>
      <td>6.555340</td>
      <td>0.910027</td>
      <td>0.832080</td>
      <td>1.328890</td>
      <td>0.992136</td>
      <td>62.215674</td>
      <td>2.346072</td>
      <td>0.379945</td>
      <td>0.813265</td>
      <td>0.276256</td>
      <td>3.065899</td>
      <td>0.449756</td>
      <td>0.449589</td>
      <td>0.449849</td>
      <td>2.372081</td>
      <td>1.885886</td>
      <td>7.689445</td>
      <td>3.005823</td>
      <td>9.225904</td>
      <td>2.339034</td>
      <td>8.433590</td>
      <td>5.441382</td>
      <td>1.441918</td>
      <td>2.872288</td>
      <td>7.539026</td>
      <td>0.122427</td>
      <td>0.627840</td>
      <td>0.554182</td>
      <td>0.287182</td>
      <td>0.349024</td>
      <td>0.153318</td>
    </tr>
    <tr>
      <th>std</th>
      <td>4.293678e+05</td>
      <td>0.187401</td>
      <td>1.983789</td>
      <td>0.664594</td>
      <td>2.699902</td>
      <td>0.493311</td>
      <td>1.350642</td>
      <td>0.488579</td>
      <td>0.436998</td>
      <td>0.370205</td>
      <td>0.388544</td>
      <td>0.019309</td>
      <td>0.041097</td>
      <td>0.096693</td>
      <td>0.030768</td>
      <td>0.127545</td>
      <td>3.546042</td>
      <td>0.473430</td>
      <td>0.326222</td>
      <td>0.360417</td>
      <td>0.287643</td>
      <td>0.404264</td>
      <td>0.793506</td>
      <td>2.508270</td>
      <td>0.375716</td>
      <td>0.788654</td>
      <td>2.153463</td>
      <td>0.844417</td>
      <td>5.501445</td>
      <td>0.347106</td>
      <td>0.373796</td>
      <td>0.978747</td>
      <td>0.091619</td>
      <td>33.012455</td>
      <td>0.832548</td>
      <td>0.058327</td>
      <td>0.224588</td>
      <td>0.357154</td>
      <td>0.731366</td>
      <td>0.287198</td>
      <td>0.286893</td>
      <td>0.287153</td>
      <td>1.117219</td>
      <td>1.134927</td>
      <td>1.334312</td>
      <td>1.414564</td>
      <td>1.459672</td>
      <td>1.246949</td>
      <td>2.904597</td>
      <td>2.332871</td>
      <td>1.202963</td>
      <td>1.694887</td>
      <td>2.746652</td>
      <td>0.327779</td>
      <td>0.483381</td>
      <td>0.497056</td>
      <td>0.452447</td>
      <td>0.476662</td>
      <td>0.360295</td>
    </tr>
    <tr>
      <th>min</th>
      <td>7.000000e+00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>0.250619</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>3.719915e+05</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>5.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.400000</td>
      <td>0.200000</td>
      <td>0.525000</td>
      <td>7.000000</td>
      <td>1.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>32.000000</td>
      <td>2.000000</td>
      <td>0.316228</td>
      <td>0.670867</td>
      <td>0.333167</td>
      <td>2.828427</td>
      <td>0.200000</td>
      <td>0.200000</td>
      <td>0.200000</td>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>7.000000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>1.000000</td>
      <td>6.000000</td>
      <td>4.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>7.435475e+05</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>4.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.700000</td>
      <td>0.300000</td>
      <td>0.720677</td>
      <td>7.000000</td>
      <td>1.000000</td>
      <td>-1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>65.000000</td>
      <td>3.000000</td>
      <td>0.374166</td>
      <td>0.765811</td>
      <td>0.368782</td>
      <td>3.316625</td>
      <td>0.500000</td>
      <td>0.400000</td>
      <td>0.500000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>3.000000</td>
      <td>9.000000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>5.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>7.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1.115549e+06</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>10.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.900000</td>
      <td>0.600000</td>
      <td>1.000000</td>
      <td>11.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>11.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>93.000000</td>
      <td>3.000000</td>
      <td>0.400000</td>
      <td>0.906190</td>
      <td>0.396485</td>
      <td>3.605551</td>
      <td>0.700000</td>
      <td>0.700000</td>
      <td>0.700000</td>
      <td>3.000000</td>
      <td>3.000000</td>
      <td>9.000000</td>
      <td>4.000000</td>
      <td>10.000000</td>
      <td>3.000000</td>
      <td>10.000000</td>
      <td>7.000000</td>
      <td>2.000000</td>
      <td>4.000000</td>
      <td>9.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1.488027e+06</td>
      <td>1.000000</td>
      <td>7.000000</td>
      <td>4.000000</td>
      <td>11.000000</td>
      <td>1.000000</td>
      <td>6.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>4.000000</td>
      <td>13.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.900000</td>
      <td>1.800000</td>
      <td>4.037945</td>
      <td>11.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>9.000000</td>
      <td>1.000000</td>
      <td>17.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>4.000000</td>
      <td>2.000000</td>
      <td>104.000000</td>
      <td>3.000000</td>
      <td>1.264911</td>
      <td>3.720626</td>
      <td>0.636396</td>
      <td>3.741657</td>
      <td>0.900000</td>
      <td>0.900000</td>
      <td>0.900000</td>
      <td>5.000000</td>
      <td>6.000000</td>
      <td>10.000000</td>
      <td>9.000000</td>
      <td>12.000000</td>
      <td>7.000000</td>
      <td>25.000000</td>
      <td>19.000000</td>
      <td>10.000000</td>
      <td>13.000000</td>
      <td>23.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#df_test.info()
```


```python
#df_test.describe()
```


```python
lignes = df.shape[0]
colonnes = df.shape[1]
print("Le jeu de données de training contient {0} lignes et {1} colonnes".format(lignes, colonnes))
```

    Le jeu de données de training contient 595212 lignes et 59 colonnes



```python
#lignes_t = df_test.shape[0]
#colonnes_t = df_test.shape[1]
#print("Le jeu de données de test contient {0} lignes et {1} colonnes".format(lignes_t, colonnes_t))
```

<a id='typd'></a>
### c) Type des données


```python
bin_featnames = []
cat_featnames = []
cont_featnames = []
ord_featnames = []
t=[]

for s in df.columns:
    if "ps_" in s:
        #print(s[3:])
        u=s[s.find("ps_")+3:]
        v=u.find("_")
        w=u[:v]
        t.append(w)

    if 'cat' in s:
        cat_featnames.append(s)

    if 'bin' in s:
        bin_featnames.append(s)

    if ('bin' not in s) & ('cat' not in s) & (s in df.dtypes[df.dtypes=='int64']) & (s not in ['target', 'id']):
        ord_featnames.append(s)

    if ('bin' not in s) & ('cat' not in s) & (s in df.dtypes[df.dtypes=='float64']):
        cont_featnames.append(s)

featgroups=sorted(dict(Counter(t)), key=dict(Counter(t)).get, reverse=True)

print("Il y a quatre groupes de features :\n")
print(featgroups)
```

    Il y a quatre groupes de features :

    ['calc', 'ind', 'car', 'reg']



```python
print("Et dans ces groupes, on retrouve quatre types de données \n")

print("Les " + str(len(bin_featnames)) + " features binaires sont :\n")
for x in bin_featnames:
    print("- "+x)
print("\n")

print("Les " + str(len(cat_featnames)) + " features catégorielles sont :\n")
for x in cat_featnames:
    print("- "+x)
print("\n")

print("Les " + str(len(cont_featnames)) + " features continues sont :\n")
for x in cont_featnames:
    print("- "+x)
print("\n")

print("Les " + str(len(ord_featnames)) + " features ordinales sont :\n")
for x in ord_featnames:
    print("- "+x)
```

    Et dans ces groupes, on retrouve quatre types de données

    Les 17 features binaires sont :

    - ps_ind_06_bin
    - ps_ind_07_bin
    - ps_ind_08_bin
    - ps_ind_09_bin
    - ps_ind_10_bin
    - ps_ind_11_bin
    - ps_ind_12_bin
    - ps_ind_13_bin
    - ps_ind_16_bin
    - ps_ind_17_bin
    - ps_ind_18_bin
    - ps_calc_15_bin
    - ps_calc_16_bin
    - ps_calc_17_bin
    - ps_calc_18_bin
    - ps_calc_19_bin
    - ps_calc_20_bin


    Les 14 features catégorielles sont :

    - ps_ind_02_cat
    - ps_ind_04_cat
    - ps_ind_05_cat
    - ps_car_01_cat
    - ps_car_02_cat
    - ps_car_03_cat
    - ps_car_04_cat
    - ps_car_05_cat
    - ps_car_06_cat
    - ps_car_07_cat
    - ps_car_08_cat
    - ps_car_09_cat
    - ps_car_10_cat
    - ps_car_11_cat


    Les 10 features continues sont :

    - ps_reg_01
    - ps_reg_02
    - ps_reg_03
    - ps_car_12
    - ps_car_13
    - ps_car_14
    - ps_car_15
    - ps_calc_01
    - ps_calc_02
    - ps_calc_03


    Les 16 features ordinales sont :

    - ps_ind_01
    - ps_ind_03
    - ps_ind_14
    - ps_ind_15
    - ps_car_11
    - ps_calc_04
    - ps_calc_05
    - ps_calc_06
    - ps_calc_07
    - ps_calc_08
    - ps_calc_09
    - ps_calc_10
    - ps_calc_11
    - ps_calc_12
    - ps_calc_13
    - ps_calc_14


<a id='vm'></a>
### d) Valeurs manquantes

```python
Nombre_de_donnees_manquantes=df.isna().sum()
Nombre_de_donnees_manquantes
```




    id                0
    target            0
    ps_ind_01         0
    ps_ind_02_cat     0
    ps_ind_03         0
    ps_ind_04_cat     0
    ps_ind_05_cat     0
    ps_ind_06_bin     0
    ps_ind_07_bin     0
    ps_ind_08_bin     0
    ps_ind_09_bin     0
    ps_ind_10_bin     0
    ps_ind_11_bin     0
    ps_ind_12_bin     0
    ps_ind_13_bin     0
    ps_ind_14         0
    ps_ind_15         0
    ps_ind_16_bin     0
    ps_ind_17_bin     0
    ps_ind_18_bin     0
    ps_reg_01         0
    ps_reg_02         0
    ps_reg_03         0
    ps_car_01_cat     0
    ps_car_02_cat     0
    ps_car_03_cat     0
    ps_car_04_cat     0
    ps_car_05_cat     0
    ps_car_06_cat     0
    ps_car_07_cat     0
    ps_car_08_cat     0
    ps_car_09_cat     0
    ps_car_10_cat     0
    ps_car_11_cat     0
    ps_car_11         0
    ps_car_12         0
    ps_car_13         0
    ps_car_14         0
    ps_car_15         0
    ps_calc_01        0
    ps_calc_02        0
    ps_calc_03        0
    ps_calc_04        0
    ps_calc_05        0
    ps_calc_06        0
    ps_calc_07        0
    ps_calc_08        0
    ps_calc_09        0
    ps_calc_10        0
    ps_calc_11        0
    ps_calc_12        0
    ps_calc_13        0
    ps_calc_14        0
    ps_calc_15_bin    0
    ps_calc_16_bin    0
    ps_calc_17_bin    0
    ps_calc_18_bin    0
    ps_calc_19_bin    0
    ps_calc_20_bin    0
    dtype: int64



Aucune donnée manquante n'a été détectée ici. Il n'y a donc que celles qui ont codées par la valeur -1. On relève donc les données manquantes en changeant les -1 en NaN et via le test isna()


```python
donnees=df.replace(-1, np.NaN)
#donnees_test=df_test.replace(-1, np.NaN)
```


```python
Nombre_de_donnees_manquantes=donnees.isna().sum()
Nombre_de_donnees_manquantes
```




    id                     0
    target                 0
    ps_ind_01              0
    ps_ind_02_cat        216
    ps_ind_03              0
    ps_ind_04_cat         83
    ps_ind_05_cat       5809
    ps_ind_06_bin          0
    ps_ind_07_bin          0
    ps_ind_08_bin          0
    ps_ind_09_bin          0
    ps_ind_10_bin          0
    ps_ind_11_bin          0
    ps_ind_12_bin          0
    ps_ind_13_bin          0
    ps_ind_14              0
    ps_ind_15              0
    ps_ind_16_bin          0
    ps_ind_17_bin          0
    ps_ind_18_bin          0
    ps_reg_01              0
    ps_reg_02              0
    ps_reg_03         107772
    ps_car_01_cat        107
    ps_car_02_cat          5
    ps_car_03_cat     411231
    ps_car_04_cat          0
    ps_car_05_cat     266551
    ps_car_06_cat          0
    ps_car_07_cat      11489
    ps_car_08_cat          0
    ps_car_09_cat        569
    ps_car_10_cat          0
    ps_car_11_cat          0
    ps_car_11              5
    ps_car_12              1
    ps_car_13              0
    ps_car_14          42620
    ps_car_15              0
    ps_calc_01             0
    ps_calc_02             0
    ps_calc_03             0
    ps_calc_04             0
    ps_calc_05             0
    ps_calc_06             0
    ps_calc_07             0
    ps_calc_08             0
    ps_calc_09             0
    ps_calc_10             0
    ps_calc_11             0
    ps_calc_12             0
    ps_calc_13             0
    ps_calc_14             0
    ps_calc_15_bin         0
    ps_calc_16_bin         0
    ps_calc_17_bin         0
    ps_calc_18_bin         0
    ps_calc_19_bin         0
    ps_calc_20_bin         0
    dtype: int64




```python
#Nombre_de_donnees_test_manquantes=donnees_test.isna().sum()
#Nombre_de_donnees_test_manquantes
```

Faisons une liste des features ayant des données manquantes :


```python
val_manquantes=donnees.columns[donnees.isna().any()].tolist()
val_manquantes
```




    ['ps_ind_02_cat',
     'ps_ind_04_cat',
     'ps_ind_05_cat',
     'ps_reg_03',
     'ps_car_01_cat',
     'ps_car_02_cat',
     'ps_car_03_cat',
     'ps_car_05_cat',
     'ps_car_07_cat',
     'ps_car_09_cat',
     'ps_car_11',
     'ps_car_12',
     'ps_car_14']




```python
#val_test_manquantes=donnees_test.columns[donnees_test.isna().any()].tolist()
#val_test_manquantes
```

Visualisons les données manquantes :


```python
import missingno as msno
msno.matrix(donnees[val_manquantes],width_ratios=(10,1),figsize=(20,10),color=(0.7,0.7,0.91),fontsize=18,\
            sparkline=True,labels=True)
```




    <matplotlib.axes._ subplots.AxesSubplot at 0x7fefc85b65c0>




![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_27_1.png)
{:class="img-responsive"}


Calculons les pourcentages de données manquantes :


```python
donnees_copy = (Nombre_de_donnees_manquantes / len(donnees)) * 100
donnees_copy = donnees_copy.drop(donnees_copy[donnees_copy == 0].index).sort_values(ascending=False)[:30]
# Rajouter une colonne avec le nombre de NaN avec pd.concat
manquantes = pd.DataFrame({'Données manquantes en %' :donnees_copy})
manquantes
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
      <th>Données manquantes en %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>ps_car_03_cat</th>
      <td>69.089837</td>
    </tr>
    <tr>
      <th>ps_car_05_cat</th>
      <td>44.782531</td>
    </tr>
    <tr>
      <th>ps_reg_03</th>
      <td>18.106490</td>
    </tr>
    <tr>
      <th>ps_car_14</th>
      <td>7.160474</td>
    </tr>
    <tr>
      <th>ps_car_07_cat</th>
      <td>1.930237</td>
    </tr>
    <tr>
      <th>ps_ind_05_cat</th>
      <td>0.975955</td>
    </tr>
    <tr>
      <th>ps_car_09_cat</th>
      <td>0.095596</td>
    </tr>
    <tr>
      <th>ps_ind_02_cat</th>
      <td>0.036290</td>
    </tr>
    <tr>
      <th>ps_car_01_cat</th>
      <td>0.017977</td>
    </tr>
    <tr>
      <th>ps_ind_04_cat</th>
      <td>0.013945</td>
    </tr>
    <tr>
      <th>ps_car_11</th>
      <td>0.000840</td>
    </tr>
    <tr>
      <th>ps_car_02_cat</th>
      <td>0.000840</td>
    </tr>
    <tr>
      <th>ps_car_12</th>
      <td>0.000168</td>
    </tr>
  </tbody>
</table>
</div>




```python
#donnees_test_copy = (Nombre_de_donnees_test_manquantes / len(donnees_test)) * 100
#donnees_test_copy = donnees_test_copy.drop(donnees_test_copy[donnees_test_copy == 0].index).sort_values(ascending=False)[:30]
# Rajouter une colonne avec le nombre de NaN avec pd.concat
#manquantes_test = pd.DataFrame({'Données test manquantes en %' :donnees_test_copy})
#manquantes_test
```

<a id='dvc'></a>
### e) Distribution de la variable cible


```python
#target=donnees.pop("target")
target = donnees.target
#type(target)
#target
```


```python
target.value_counts()
```




    0    573518
    1     21694
    Name: target, dtype: int64




```python
print("Le pourcentage de déclaration de sinistre est : " + str(np.mean(donnees["target"])))
```

    Le pourcentage de déclaration de sinistre est : 0.036447517859182946



```python
plt.style.use('ggplot')
sns.despine(left=True)
ax=sns.countplot(x=target, data=donnees)

plt.tight_layout()
```

![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_35_0.png)
{:class="img-responsive"}


Il n'y a que 3,6 % de déclaration de sinistres. On voir donc que le jeu de données est très déséquilibré. C'est surement la raison de cette compétition Kaggle. En effet, si la valeur 1 est aussi sous-représentée, il devient difficle de la prédire. Le but est alors soit de mieux prédire les futures déclarations de sinistres soit de détecter de la fraude.

Pour effectuer de bonnes prédictions malgré ce déséquilibre on peut sous-échantillonner les données de la classe dominante, sur-échantillonner les données de la classe minoritaire ou bien encore stratifier les données lors de la cross-validation.

## 2) Visualisation des données

### a) 	Corrélation des features selon leur type


```python
def correlation_features(titre, colonnes, dataframe):
    colormap = plt.cm.inferno
    plt.figure(figsize=(16,12))
    plt.title('Corrélation des features ' + titre + '\n', y=1, fontsize= 16)
    sns.heatmap(dataframe[colonnes].corr(), cmap=colormap, annot=True)
```

**Corrélation des features binaires**


```python
correlation_features('binaires', bin_featnames, donnees)
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_41_0.png)
{:class="img-responsive"}




**Corrélation des features continues**


```python
correlation_features('continues', cont_featnames,donnees)
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_44_0.png)
{:class="img-responsive"}




**Corrélation des features ordinales**


```python
correlation_features('ordinales', ord_featnames, donnees)
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_47_0.png)
{:class="img-responsive"}




### b) Distribution des features

**Catégorielles**


```python
fig , axes = plt.subplots(nrows=5,ncols=3,figsize=(16,16))
for i , colname in enumerate(cat_featnames):
    sns.countplot(colname,data=donnees[cat_featnames],ax=fig.axes[i])
plt.tight_layout()
fig.delaxes(axes[4][2])
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_51_0.png)
{:class="img-responsive"}


**Binaires**


```python
fig , axes = plt.subplots(nrows=5,ncols=4,figsize=(13,13))
for i , colname in enumerate(bin_featnames):
    sns.countplot(colname,data=donnees[bin_featnames],ax=fig.axes[i])
plt.tight_layout()
for i in range(1,4):
    fig.delaxes(axes[4][i])
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_53_0.png)
{:class="img-responsive"}


**Continues**


```python
compt=0
for i, colname in enumerate(cont_featnames):
    plt.figure(compt)
    sns.boxplot(x=colname,
                #hue='target',
                data=donnees)
    #plt.title(colname)
    compt+=1
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_0.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_1.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_2.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_3.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_4.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_5.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_6.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_7.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_8.png)
{:class="img-responsive"}



![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_55_9.png)
{:class="img-responsive"}


**Ordinales**


```python
fig , axes = plt.subplots(nrows=4,ncols=4,figsize=(16,16))
for i , colname in enumerate(ord_featnames):
    sns.countplot(colname,data=donnees[ord_featnames],ax=fig.axes[i])
plt.tight_layout()
#fig.delaxes(axes[4][2])
```


![alt]({{ site.url }}{{ site.baseurl }}/images/kaggle/output_59_0.png)
{:class="img-responsive"}
