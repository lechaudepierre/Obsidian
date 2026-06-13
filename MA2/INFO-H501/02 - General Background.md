# 02 — General Background

> [!abstract] Le fil rouge
> Ce chapitre pose le vocabulaire commun de tout le cours : la **reconnaissance de formes** consiste à apprendre, à partir d'**exemples**, une règle capable de prédire la **classe** d'un nouveau cas à partir de ses **features**. Tout le reste (Bayes, arbres, réseaux de neurones) n'est qu'une façon différente de construire cette règle. Le concept central à comprendre : on ne cherche pas à bien coller aux données d'entraînement, mais à **généraliser** à des données jamais vues.

## 1. De quoi parle-t-on ?

**Pattern Recognition** = reconnaissance de régularités dans un signal (1D, 2D, 3D) ou des données multivariées, à partir de connaissance métier et/ou d'information statistique extraite des données. Cela couvre la **classification supervisée** et le **clustering** (non supervisé), et s'appuie souvent sur du Machine Learning.

> [!info] Machine learning vs Deep learning
> Le **deep learning** analyse signaux/images/vidéos d'une façon que le ML classique ne peut pas faire facilement, et surtout **sans extraction manuelle de features** (il les apprend lui-même). En contrepartie il exige beaucoup plus de données, de temps de calcul et de hardware (GPU). C'est exactement la transition entre la Part I (features faites main) et la Part II de ce cours.

---

## 2. Le cadre supervisé

On apprend **à partir d'exemples** (ex. diagnostic médical : associer un nom de maladie à une liste de symptômes).

| Terme | Signification |
|---|---|
| Cases | les individus (patients) |
| Descriptive features $X_j$ | symptômes, mesures, caractéristiques |
| Class $Y$ | la classe catégorielle à prédire |
| Modeling step | construire le modèle sur des données historiques |
| Production step | appliquer le modèle sur de nouveaux cas (features seules connues) |

On distingue les **données rétrospectives** (historiques, classe connue → apprentissage, test, évaluation) et les **données prospectives** (nouveaux cas → production).

> [!note] Segmentation = classification de pixels
> Une idée transversale du cours : segmenter une image, c'est classer chaque pixel. Ce pont relie ce chapitre à [[10 - Segmentation]] et [[24 - Segmentation (deep)]].

---

## 3. Formalisation du classifieur

**Training set** rétrospectif :

$$\{(\mathbf{x}_i, y_i);\ i = 1, \dots, N\}$$

avec $\mathbf{x}_i = [x_{i1}, \dots, x_{ip}]^T$ le vecteur de features du cas $i$, et $y_i = \omega_k \in \{\omega_1, \dots, \omega_q\}$ sa classe connue (sortie désirée).

Le **classifieur** est une fonction $C : \mathbf{x} \rightarrow C(\mathbf{x}) \in \{\omega_1, \dots, \omega_q\}$. Approche usuelle : $q$ **fonctions discriminantes** $f_1, \dots, f_q$ telles que

$$C(\mathbf{x}) = \omega_k \iff f_k(\mathbf{x}) > f_j(\mathbf{x}) \quad \forall j \neq k$$

> [!tip] Comment lire ça
> Chaque classe a son « score » $f_k(\mathbf{x})$. On choisit la classe au plus gros score. Cela **découpe l'espace des features en $q$ régions** $R_k = \{\mathbf{x} \mid C(\mathbf{x}) = \omega_k\}$. La **frontière de décision** entre deux classes est l'ensemble où $f_k(\mathbf{x}) - f_j(\mathbf{x}) = 0$ — linéaire ou non selon le choix des $f_j$.

---

## 4. Le cœur du cours : généralisation, sur/sous-apprentissage

On distingue deux performances :
- **Performance descriptive** : erreur sur les données d'entraînement (minimisée pendant l'apprentissage).
- **Performance prédictive** : erreur sur des cas nouveaux (test set), jamais utilisés auparavant.

> [!warning] Underfitting vs Overfitting
> **Underfitting** : le modèle est trop simple pour capturer la relation (ex. modèle linéaire sur frontières non-linéaires) → mauvaise perf *descriptive*.
> **Overfitting** : le modèle est trop spécialisé sur l'entraînement et généralise mal → bonne perf descriptive mais mauvaise perf *prédictive*. Causes : modèle trop complexe (trop de degrés de liberté), ou trop de features par rapport à la taille du training set.

> [!tip] Le compromis fondamental
> Trois facteurs en tension : complexité du modèle $C_p$, nombre de cas d'entraînement $N$, erreur de prédiction $E_p$.
> - Si $N \nearrow$ alors $E_p \searrow$ (plus de données aide).
> - Si $C_p \nearrow$ alors $E_p$ **d'abord $\searrow$ puis $\nearrow$** → il existe une complexité optimale.

Données qui causent des problèmes : **bruit** (erreurs dans les features ou les labels → frontières déformées) et **manque de données** (frontières mal estimées).

---

> [!quote] À retenir
> L'objectif d'un classifieur n'est pas de mémoriser les exemples mais d'**extraire une règle générale** pour bien décider sur de nouveaux cas. La stratégie usuelle : ajuster les paramètres du modèle (et sélectionner les features) pour optimiser un critère de performance. Garde en tête le triangle complexité / quantité de données / erreur — c'est lui qui explique pourquoi on régularise ([[20 - Optimization]]) et pourquoi on élague les arbres ([[05 - Decision tree]]).

Voir aussi : [[03 - Bayesian approach]] · [[04 - Non-Bayesian approaches]] · [[00 - Index]]
