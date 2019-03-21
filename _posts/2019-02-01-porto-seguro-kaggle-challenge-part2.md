---
title: "Porto Seguro Kaggle challenge (French) - Part 2"
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

# II. Pré-traitement des données
[Retourner au sommaire](#toc)<br>

## 1) Suppression de features

Les features ps_car_03_cat, ps_car_05_cat et ps_reg_03 ont trop de valeurs manquantes. On va donc les supprimer :


```python
donnees.drop(['ps_car_03_cat','ps_car_05_cat','ps_reg_03'], axis=1, inplace=True)
#donnees_test.drop(['ps_car_03_cat','ps_car_05_cat','ps_reg_03'], axis=1, inplace=True)
cont_featnames.remove('ps_reg_03')
cat_featnames.remove('ps_car_03_cat')
cat_featnames.remove('ps_car_05_cat')
```

On supprime également les variables 'calc' qui sont, comme on peut le voir sur les graphiques et comme leur nom le suggère , des simulations de variables gaussiennes


```python
suppr_calc = [c for c in donnees.columns if ('calc' in c and c in cont_featnames+ord_featnames)]
donnees.drop(suppr_calc, axis=1, inplace=True)
```


```python
suppr_calc
```




    ['ps_calc_01',
     'ps_calc_02',
     'ps_calc_03',
     'ps_calc_04',
     'ps_calc_05',
     'ps_calc_06',
     'ps_calc_07',
     'ps_calc_08',
     'ps_calc_09',
     'ps_calc_10',
     'ps_calc_11',
     'ps_calc_12',
     'ps_calc_13',
     'ps_calc_14']




```python
cont_featnames.remove('ps_calc_01')
cont_featnames.remove('ps_calc_02')
cont_featnames.remove('ps_calc_03')
```


```python
cont_featnames
```




    ['ps_reg_01', 'ps_reg_02', 'ps_car_12', 'ps_car_13', 'ps_car_14', 'ps_car_15']




```python
ord_featnames.remove('ps_calc_04')
ord_featnames.remove('ps_calc_05')
ord_featnames.remove('ps_calc_06')
ord_featnames.remove('ps_calc_07')
ord_featnames.remove('ps_calc_08')
ord_featnames.remove('ps_calc_09')
ord_featnames.remove('ps_calc_10')
ord_featnames.remove('ps_calc_11')
ord_featnames.remove('ps_calc_12')
ord_featnames.remove('ps_calc_13')
ord_featnames.remove('ps_calc_14')
```


```python
ord_featnames
```




    ['ps_ind_01', 'ps_ind_03', 'ps_ind_14', 'ps_ind_15', 'ps_car_11']




```python

```

On supprime également la colonne id (à cause de sa valeur prédictive nulle) :


```python
donnees.drop(['id'],axis=1, inplace=True)
#donnees_test.drop(['id'],axis=1, inplace=True)
```


```python
donnees.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 595212 entries, 0 to 595211
    Data columns (total 41 columns):
    target            595212 non-null int64
    ps_ind_01         595212 non-null int64
    ps_ind_02_cat     594996 non-null float64
    ps_ind_03         595212 non-null int64
    ps_ind_04_cat     595129 non-null float64
    ps_ind_05_cat     589403 non-null float64
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
    ps_car_01_cat     595105 non-null float64
    ps_car_02_cat     595207 non-null float64
    ps_car_04_cat     595212 non-null int64
    ps_car_06_cat     595212 non-null int64
    ps_car_07_cat     583723 non-null float64
    ps_car_08_cat     595212 non-null int64
    ps_car_09_cat     594643 non-null float64
    ps_car_10_cat     595212 non-null int64
    ps_car_11_cat     595212 non-null int64
    ps_car_11         595207 non-null float64
    ps_car_12         595211 non-null float64
    ps_car_13         595212 non-null float64
    ps_car_14         552592 non-null float64
    ps_car_15         595212 non-null float64
    ps_calc_15_bin    595212 non-null int64
    ps_calc_16_bin    595212 non-null int64
    ps_calc_17_bin    595212 non-null int64
    ps_calc_18_bin    595212 non-null int64
    ps_calc_19_bin    595212 non-null int64
    ps_calc_20_bin    595212 non-null int64
    dtypes: float64(14), int64(27)
    memory usage: 186.2 MB



```python
#donnees_test.info()
```

On remplace maintenant les données manquantes des autres features :
* Pour les features binaires, catégorielles et ordinales,  vu qu'il s'agit de valeurs entières, on choisit de les remplacer par la valeur la plus rencontrée dans la colonne
* Pour les features continues, on les remplace par la moyenne des valeurs de la colonne


```python
val_manquantes2=donnees.columns[donnees.isna().any()].tolist()
val_manquantes2
```




    ['ps_ind_02_cat',
     'ps_ind_04_cat',
     'ps_ind_05_cat',
     'ps_car_01_cat',
     'ps_car_02_cat',
     'ps_car_07_cat',
     'ps_car_09_cat',
     'ps_car_11',
     'ps_car_12',
     'ps_car_14']




```python
#val_manquantes_test2=donnees_test.columns[donnees_test.isna().any()].tolist()
#val_manquantes_test2
```


```python
donnees.isna().sum().sum()
```




    60904




```python
#donnees_test.isna().sum().sum()
#donnees['ps_calc_05']
cont_featnames
```




    ['ps_reg_01', 'ps_reg_02', 'ps_car_12', 'ps_car_13', 'ps_car_14', 'ps_car_15']




```python
#donnees2=donnees

for c in (bin_featnames + cat_featnames + ord_featnames):
    donnees[c].fillna(value=donnees[c].mode()[0], inplace=True)
    #donnees_test[c].fillna(value=donnees_test[c].mode()[0], inplace=True)

for c in cont_featnames:
    donnees[c].fillna(value=donnees[c].mean(), inplace=True)
    #donnees_test[c].fillna(value=donnees_test[c].mean(), inplace=True)       
```


```python
donnees.isna().sum().sum()
```




    0




```python
#donnees_test.isna().sum().sum()
```

## 2) Encodage des données catégorielles
[Retourner au sommaire](#toc)<br>




```python
from sklearn.preprocessing import OneHotEncoder, LabelEncoder

donnees = pd.get_dummies(donnees, columns=cat_featnames)
```


```python
donnees.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 595212 entries, 0 to 595211
    Columns: 200 entries, target to ps_car_11_cat_104
    dtypes: float64(7), int64(22), uint8(171)
    memory usage: 228.8 MB


## 3) Centrage et réduction des variables continues


```python
from sklearn.preprocessing import MinMaxScaler, StandardScaler

X_cont = donnees[cont_featnames]
scaler = StandardScaler()
#X_train = scaler.fit_transform(donnees.drop(['id','target'], axis=1))
X_train = scaler.fit_transform(X_cont)
```

## 4) ACP


```python
y_train = donnees['target'].values.astype(np.int8)

target_names = np.unique(y_train)
n_components=6

#ACP
acp = PCA(n_components=n_components, svd_solver='full', random_state=2018)
donnees_acp = acp.fit_transform(X_cont)

#Variance expliquée
print('Variance expliquée: %.4f' % acp.explained_variance_ratio_.sum())
n = 6
cumsum = 0
print('Contributions cumulées à la variance (pour les ' + str(n) + ' premières composantes):')
for j in range(n):
    cumsum += acp.explained_variance_ratio_[j]
    print(cumsum)
```

    Variance expliquée: 1.0000
    Contributions cumulées à la variance (pour les 6 premières composantes):
    0.6584548548181819
    0.8874145918721105
    0.9547948191088546
    0.9967413790253931
    0.9988949712110557
    0.9999999999999999



```python
#donnees.head()
```


```python
#Plot
plt.figure(1, figsize=(12, 12))
colors = ['blue', 'green']
for i in target_names:
    plt.scatter(donnees_acp[y_train == i, 0], donnees_acp[y_train == i, 1], color=colors[i], s=1, label=i)
plt.legend(fontsize=9)
plt.title("Données training projetées sur la 1ère et la 2ème composante de l'ACP")
plt.xlabel("1er axe - Justifie %.1f %% de la variance" % (acp.explained_variance_ratio_[0] * 100.0))
plt.ylabel("2ème axe - Justifie %.1f %% de la variance" % (acp.explained_variance_ratio_[1] * 100.0))


plt.show()
```


![png](output_93_0.png)
