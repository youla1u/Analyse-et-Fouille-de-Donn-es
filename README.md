# Fouille de Données pour la Prédiction de Classes d’Heures d’Absentéisme au Travail

## 🎓 Contexte
Université d’Artois  
Faculté des Sciences Jean Perrin  

**Présenté par :** Mohamed YOULA  
**Date :** 30/11/2020  

---

## 📌 Introduction
Ce projet porte sur un jeu de données concernant l’absentéisme au travail disponible ici :  
[Absenteeism_at_work.csv - UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/Absenteeism+at+work)

- **Nombre d’instances** : 740  
- **Variables explicatives** : 20 (numériques et nominales)  
- **Variable cible** : `Absenteeism_time_in_hours`  

### Prétraitements effectués :
- Conversion de certaines variables numériques en nominales (ID, Reason_for_absence, Month_of_absence, etc.).  
- Normalisation des variables numériques avec la technique **Min-Max**.  
- Suppression des valeurs aberrantes détectées via des estimateurs de densité.  

**Outils utilisés :**
- Prétraitement et visualisation : **R**
- Classification et évaluation : **Weka**

---

## 🔍 Analyse de la variable cible
Nous avons estimé la distribution de la variable `Absenteeism_time_in_hours` avec trois estimateurs de densité (Histogramme, Polygone de fréquence, Estimateur à noyau gaussien), renforcés par la technique du **bagging**.

### Résultats des erreurs :
| Estimateur              | Erreur  |
|--------------------------|---------|
| Histogramme             | 0.026   |
| Polygone de fréquence   | 0.023   |
| Estimateur à noyau      | 0.072   |

👉 Le **Polygone de fréquence** est le meilleur estimateur.


---

## 🤖 Classification
La variable cible a été transformée en **classes binaires** :
- **Classe A** : Absences < 4.293h  
- **Classe B** : Absences ≥ 4.293h  

### Cas 1 : Jeu de données déséquilibré
| Modèle                  | Accuracy | Précision | Rappel | ROC Area |
|--------------------------|----------|-----------|--------|----------|
| J48 (10-fold CV)        | 83.48%   | 0.833     | 0.835  | 0.862    |
| JRip (10-fold CV)       | 77.59%   | 0.772     | 0.776  | 0.761    |
| Bagging J48 (100)       | 82.90%   | 0.827     | 0.829  | **0.883**|
| Bagging JRip (100)      | 80.17%   | 0.798     | 0.802  | 0.850    |
| AdaBoostM1 J48 (100)    | 79.45%   | 0.794     | 0.795  | 0.864    |
| AdaBoostM1 JRip (100)   | 79.74%   | 0.795     | 0.797  | 0.847    |
| RandomForest            | 81.03%   | 0.807     | 0.810  | 0.879    |

👉 **Meilleur modèle : Bagging J48 (100)**  

### Cas 2 : Jeu de données équilibré
| Modèle                  | Accuracy | Précision | Rappel | ROC Area |
|--------------------------|----------|-----------|--------|----------|
| J48 (10-fold CV)        | 83.15%   | 0.832     | 0.831  | 0.864    |
| JRip (10-fold CV)       | 77.31%   | 0.774     | 0.773  | 0.805    |
| Bagging J48 (100)       | 82.39%   | 0.824     | 0.824  | 0.885    |
| Bagging JRip (100)      | **83.66%** | **0.837** | **0.837** | **0.894** |
| AdaBoostM1 J48 (100)    | 77.62%   | 0.782     | 0.776  | 0.865    |
| AdaBoostM1 JRip (100)   | 80.47%   | 0.805     | 0.805  | 0.872    |
| RandomForest            | 80.71%   | 0.810     | 0.807  | 0.882    |

👉 **Meilleur modèle : Bagging JRip (100)**  

![ROC Curves](images/roc_curves.png)

---

## 📊 Règles d’association
Quelques règles extraites avec **JRip** :  

1. `(transportation.expense >=235) AND (Age <=33) AND (Disciplinary.failure=0) → Classe B`  
2. `(Reason.for.absence=26) AND (Age <=43) → Classe B`  
3. `(Reason.for.absence=22) → Classe B`  
4. `(Weight <=67.99) AND (Work.load.Average.day >=264.60) AND (Age>=41) → Classe B`  
5. `(Reason.for.absence=13) AND (Service.time>=11) → Classe B`  

👉 La **raison d’absence** et **l’âge** apparaissent comme variables très discriminantes.  

---

## ✅ Conclusion
- Les valeurs aberrantes faussent fortement les moyennes et perturbent les classifieurs.  
- Après nettoyage, le **Bagging JRip (100)** est le modèle le plus performant.  
- Les règles d’association montrent que certains profils (jeunes, forte dépense transport, raisons médicales) sont plus susceptibles de dépasser 4h d’absence.  

---

