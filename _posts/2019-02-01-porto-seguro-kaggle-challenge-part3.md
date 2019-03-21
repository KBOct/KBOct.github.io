---
title: "Porto Seguro Kaggle challenge (French) - Part 3"
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

# III. Construction des modèles

## 1) Choix du classifieur

### a)	Séparation du jeu de données


```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression, LogisticRegressionCV

X=donnees.drop(['target'], axis=1)
#X=donnees.drop(['id','target'], axis=1)
y=target

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, test_size=1/5)

#from imblearn.over_sampling import SMOTE
#from collections import Counter

#sm = SMOTE(ratio = 0.30, random_state = 42, k_neighbors=5)

#print('Original dataset shape {}'.format(Counter(y)))

#X_train, y_train = sm.fit_sample(X, y)

#print('Resampled dataset shape {}'.format(Counter(y_train)))

#X_train, X_test, y_train, y_test = train_test_split(X_train, y_train, stratify=y_train, test_size=0.3)
```

### b)	Cross-validation des classifieurs


```python
X_array = X_train.values
Y_array = y_train.values

#X_array = X_train
#Y_array = y_train

X_learning = X_array[:10000][:,0:7]
Y_learning = Y_array[:10000]


kfold = StratifiedKFold(n_splits=5)


clfs = []    

clfs.append(("RF", RandomForestClassifier()) )
clfs.append(("AdaBoost", AdaBoostClassifier()) )
clfs.append(("GBoost", GradientBoostingClassifier()) )
clfs.append(("DTree", DecisionTreeClassifier()) )
clfs.append(("ExTree", ExtraTreeClassifier()) )
clfs.append(("LogReg", LogisticRegression()) )
clfs.append(("XGBoost", XGBClassifier()) )




clf_names = []
means = []
stds = []


for nom, clf in clfs:
    #cross validation among models, score based on accuracy
    cv_results = cross_val_score(clf, X_learning, Y_learning, scoring='accuracy', cv=kfold)
    print(clf.__class__.__name__)
    #clf_names.append(clf.__class__.__name__)
    clf_names.append(nom)
    print("Résultat: " + str(cv_results))
    print("Moyenne: " + str(cv_results.mean()))
    print("Ecart type: " + str(cv_results.std())+'\n')
    means.append(cv_results.mean())
    stds.append(cv_results.std())
```

    RandomForestClassifier
    Résultat: [0.96501749 0.96401799 0.965      0.96498249 0.96548274]
    Moyenne: 0.9649001429750357
    Ecart type: 0.0004791943804980242

    AdaBoostClassifier
    Résultat: [0.96501749 0.96501749 0.9655     0.96548274 0.96548274]
    Moyenne: 0.9653000930500234
    Ecart type: 0.0002308294420302726

    GradientBoostingClassifier
    Résultat: [0.96501749 0.96501749 0.9655     0.96548274 0.96548274]
    Moyenne: 0.9653000930500234
    Ecart type: 0.0002308294420302726

    DecisionTreeClassifier
    Résultat: [0.96501749 0.96401799 0.965      0.96548274 0.96498249]
    Moyenne: 0.9649001429750357
    Ecart type: 0.0004791943804980242

    ExtraTreeClassifier
    Résultat: [0.96501749 0.96401799 0.9655     0.96548274 0.96548274]
    Moyenne: 0.9651001930000482
    Ecart type: 0.0005710574203836229

    LogisticRegression
    Résultat: [0.96501749 0.96501749 0.9655     0.96548274 0.96548274]
    Moyenne: 0.9653000930500234
    Ecart type: 0.0002308294420302726

    XGBClassifier
    Résultat: [0.96501749 0.96501749 0.9655     0.96548274 0.96548274]
    Moyenne: 0.9653000930500234
    Ecart type: 0.0002308294420302726




```python
x_loc = np.arange(len(clfs))
width = 0.5


plt.figure(figsize=(9, 5), dpi=100)

clf_graph = plt.bar(x_loc, means, width, yerr=stds)
plt.ylabel('Accuracy')
plt.title('Scores par modèles\n')
plt.xticks(x_loc, clf_names)

def addLabel(rects):
    for rect in rects:
        height = rect.get_height()
        plt.text(rect.get_x() + rect.get_width()/2., 1.05*height,
        '%f' % height, ha='center',
        va='bottom')

addLabel(clf_graph)
plt.show()
```


![png](output_101_0.png)


### c)	Courbes ROC


```python
clfrs = [
    RandomForestClassifier(),
    AdaBoostClassifier(),
    GradientBoostingClassifier(),
    DecisionTreeClassifier(),
    ExtraTreeClassifier(),
    LogisticRegression(),
    XGBClassifier()
]

aucs = []
fprs = []
tprs = []
precisions = []
recalls = []
f1_scores = []

for clf in clfrs:
    print(clf.__class__.__name__)
    clf.fit(X_train, y_train)

    fpr, tpr, _ = roc_curve(y_test, clf.predict_proba(X_test)[:, 1])    
    fprs.append(fpr)
    tprs.append(tpr)
    aucs.append(auc(fpr, tpr))


    precision, recall, _ = precision_recall_curve(y_test, clf.predict_proba(X_test)[:, 1])
    precisions.append(precision)
    recalls.append(recall)
    f1_scores.append(f1_score(y_test, clf.predict(X_test)))  
```

    RandomForestClassifier
    AdaBoostClassifier
    GradientBoostingClassifier
    DecisionTreeClassifier
    ExtraTreeClassifier
    LogisticRegression
    XGBClassifier



```python
names = [clf.__class__.__name__ for clf in clfrs]

plt.figure(figsize=(8, 6))
plt.plot([0, 1], [0, 1], 'k--')

for fpr, tpr, auc, name in zip(fprs, tprs, aucs, names):
    plt.plot(fpr, tpr, label=name + ' (AUC=%.2f)' % auc, lw=2)
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('Taux de Faux Positifs \n False Positive Rate', fontsize=12)
plt.ylabel('Taux de Vrais Positifs \n True Positive Rate', fontsize=12)
plt.title('Receiver operating characteristic\n', fontsize=16)
plt.legend(loc="lower right", fontsize=10)

```




    <matplotlib.legend.Legend at 0x7fefc4a07630>




![png](output_104_1.png)


### d)	Courbes Precision-Recall


```python
plt.figure(figsize=(9, 7))

for precision, recall, f1_score, name in zip(precisions, recalls, f1_scores, names):
    plt.plot(recall, precision, label=name + ' (F1=%.5f)' % f1_score, lw=3)
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('Recall', fontsize=12)
plt.ylabel('Precision', fontsize=12)
plt.title('Courbe Precision-Recall\n', fontsize=16)
plt.legend(loc="upper right", fontsize=11)
```




    <matplotlib.legend.Legend at 0x7fefc49a2f28>




![png](output_106_1.png)


 ## 2) Tuning des hyperparamètres




```python
# Fonction de calcul du coefficient de Gini issue du kernel disponible à l'adresse URL :
# https://www.kaggle.com/tezdhar/faster-gini-calculation

from sklearn.metrics.scorer import make_scorer

def ginic(actual, pred):
    actual = np.asarray(actual) #In case, someone passes Series or list
    n = len(actual)
    a_s = actual[np.argsort(pred)]
    a_c = a_s.cumsum()
    giniSum = a_c.sum() / a_s.sum() - (n + 1) / 2.0
    return giniSum / n

def gini_normalizedc(a, p):
    if p.ndim == 2:#Required for sklearn wrapper
        p = p[:,1] #If proba array contains proba for both 0 and 1 classes, just pick class 1
    return ginic(a, p) / ginic(a, a)

gini_sklearn = make_scorer(gini_normalizedc, True, True)
```

**Séparation du jeu de données**

On va entraîner nos modèles par K-fold cross-validation. Etant donné que le jeu de données est très déséquilibré, on veut avoir le même nombre de "True" dans chacun des folds pour être sur que les modèles ne prédisent pas la classe "False" trop souvent.


```python
folds = 4
skf = StratifiedKFold(n_splits=folds, shuffle = True, random_state = 1001)
```

**Grille d'hyperparamètres pour GradientBoostingClassifier**


```python
params = {
        'min_samples_leaf': [1,5,10],
        'max_depth': [3, 5,7],
        'subsample': [0.6, 0.7,0.8],
        'max_features': [None, 50, 100]
        }
```


```python
gb_clr = XGBClassifier(n_estimators=100, learning_rate=0.02)
```


```python
#from sklearn.model_selection import RandomizedSearchCV

n_iter = 5
random_search = RandomizedSearchCV(gb_clr, param_distributions=params, n_iter=n_iter, scoring=gini_sklearn, n_jobs=-2, cv=skf.split(X_train, y_train), verbose=3, random_state=2018)

random_search.fit(X_train, y_train)
```

    Fitting 4 folds for each of 5 candidates, totalling 20 fits
    [CV] subsample=0.8, min_samples_leaf=1, max_features=50, max_depth=3 .


    [Parallel(n_jobs=-2)]: Using backend SequentialBackend with 1 concurrent workers.



```python
print('Meilleurs hyperparamètres :')
print(random_search.best_params_)
```
