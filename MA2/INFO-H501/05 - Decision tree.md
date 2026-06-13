# 05 — Decision tree

> [!abstract] Le fil rouge
> L'arbre de décision est l'archétype de l'**IA explicable** (XAI) : il produit des règles logiques « SI… ALORS… » lisibles par un humain, à l'opposé des « boîtes noires » que sont les réseaux profonds. Toute la mécanique tourne autour d'une seule question répétée à chaque nœud : **quel test découpe le mieux les données en sous-groupes purs en classes ?** Puis vient le contrepoids indispensable — l'**élagage** — pour éviter le sur-apprentissage, et enfin les **forêts** qui transforment l'instabilité de l'arbre en force.

## 1. Origines et philosophie

Méthode parmi les premières de l'IA (années 1970, ID3, C4.5, CART, CHAID, QUEST). Particularité : **conviviabilité et intelligibilité** des résultats. En classification, la sortie est un ensemble de **règles logiques** :

> SI $X_1 > \theta_1$ ET $X_2 > \theta_2$ ALORS « low-risk » SINON « high-risk »

> [!success] Explainable AI (XAI)
> Facile à comprendre et à appliquer, extraction explicite de connaissance, communication aisée avec les experts du domaine. Intérêt renouvelé aujourd'hui, **en opposition aux méthodes « boîte noire »** (réseaux profonds).

---

## 2. Principe général

**Croissance de l'arbre** : découpage successif (souvent binaire) de l'espace des features en régions de plus en plus pures. Le problème est décomposé en une série de tests emboîtés, chacun portant sur une feature (option univariée) ou une combinaison linéaire (option multivariée / arbre « oblique »).

- Chaque **chemin** racine → feuille = une règle de classification.
- Classe d'une feuille = **classe majoritaire** des cas d'entraînement qui y tombent.

**Arrêt et élagage** — règles « évidentes » d'arrêt sur un nœud : tous les cas dans une seule classe ; tous les cas ont la même description ; pas de baisse d'impureté ; trop peu de cas pour re-découper.

---

## 3. Mesures d'impureté (hétérogénéité de classe)

Cœur de la sélection des tests. Trois mesures classiques sur un nœud $N$ :

> [!info] Les trois critères
> **Entropie** (ID3, C4.5) :
> $$H(N) = -\sum_k P(\omega_k)\log_2 P(\omega_k)$$
> **Indice de Gini** (CART) :
> $$Gini(N) = 1 - \sum_j P^2(\omega_j)$$
> **Erreur de classification** :
> $$Er(N) = 1 - \max_j P(\omega_j)$$

| Critère | Minimum (= 0) | Maximum |
|---|---|---|
| Entropie | 1 seule classe dans $N$ | $\log_2(q)$ (équidistribution) |
| Gini | 1 seule classe | $1 - 1/q$ |
| Erreur | 1 seule classe | $1 - 1/q$ |

> [!tip] Comment lire l'impureté
> Une impureté nulle = nœud « pur » (une seule classe), c'est le but. L'impureté max = classes équiréparties (le pire pour décider). $P(\omega_k)$ est estimée par la **fréquence relative** de la classe dans le nœud.

---

## 4. Sélection du meilleur test : le gain d'homogénéité

Un test $T$ découpe le nœud $N$ (taille $n$) en $m$ sous-nœuds $N_j$ (taille $n_j$). Le **gain** :

$$Gain(N,T) = I(N) - \sum_j \frac{n_j}{n}\,I(N_j)$$

On sélectionne à chaque nœud le test qui **maximise le gain** (= minimise l'impureté moyenne pondérée après découpage).

> [!warning] Biais du gain et corrections
> $Gain$ **favorise les tests à grand $m$** (beaucoup d'alternatives → petits nœuds → faciles à « purifier »). Solutions :
> - n'utiliser que des **tests binaires**, ou
> - le **Gain ratio** (C4.5) : $R_{Gain}(N,T) = Gain(N,T)/H(T)$ avec $H(T) = -\sum_j P(N_j)\log_2 P(N_j)$.
>
> Mais $R_{Gain}$ a son propre biais (favorise les partitions déséquilibrées) → contrainte additionnelle : choisir $T$ qui maximise $R_{Gain}$ **parmi ceux ayant un grand Gain**. Tous ces problèmes disparaissent avec les tests binaires uniquement.

### Features quantitatives
Chaque valeur observée $x_i$ donne un test candidat « $X < x_i$ ? ». Les critères d'impureté favorisent les seuils situés **à la frontière entre deux classes**.

> [!warning] Sensibilité au bruit
> Les frontières inter-classes observées sont prises au pied de la lettre → **forte sensibilité** aux variations du training set / bruit / erreurs de labels → augmentation de la variance des performances. (Le bruit n'affecte que les zones-frontières ; l'arrêt et l'élagage atténuent l'impact des exceptions internes.)

**Avantages** du découpage parallèle (univarié) : tests univariés, combinaison facile de features qualitatives/quantitatives (pas de mise à l'échelle), sélection automatique de features, rapidité, règles logiques simples. **Inconvénients** : approximation des frontières « en escalier » ; les critères ignorent la densité des données.

---

## 5. Élagage (pruning)

> [!info] But et principe
> Supprimer les parties de l'arbre **inexactes pour prédire de nouveaux cas**. Processus « bottom-up » (feuilles → racine) : on compare les taux d'erreur estimés **avant vs après** suppression d'une branche ; un nœud interne est remplacé par une feuille (classe majoritaire).

Estimation de l'erreur selon l'algorithme : validation set indépendant ; estimation statistique (borne sup d'un intervalle de confiance binomial, pessimiste — C4.5) ; ou validation croisée. L'élagage est **requis** quand on utilise des critères d'impureté, et il **simplifie** l'interprétation des règles.

### Des règles encore plus souples
On peut convertir l'arbre en système de règles puis simplifier (supprimer des tests ou des règles entières) **indépendamment** les unes des autres, ce qui relâche la contrainte bottom-up — au prix de la perte des propriétés d'exhaustivité/exclusivité (on classe alors par règles **ordonnées** selon leur taux estimé, avec une classe par défaut).

---

## 6. Instabilité et forêts d'arbres

> [!warning] Le talon d'Achille : l'instabilité
> Algorithmes **séquentiels** qui ne reconsidèrent jamais leurs choix passés. Une petite variation des données peut changer un choix à un niveau donné → sous-arbres différents → quel est l'arbre « optimal » ? Cela questionne même la valeur de l'« extraction de connaissance ».

Pistes : discrétiser les features quantitatives (→ ordinales) ; **présélectionner les features stables** (souvent choisies dans les arbres de validation croisée).

> [!success] Les forêts (Random Forest) — transformer le défaut en qualité
> On **exploite** l'instabilité : on injecte de la variation (échantillonnage des cas, sous-ensembles aléatoires de features, seuils aléatoires) pour générer **un grand nombre de classifieurs faibles**, puis on combine leurs sorties (vote). Résultat : performances **stables et améliorées** (l'un des meilleurs classifieurs de l'état de l'art), au prix de la perte d'intelligibilité.

Avantages des forêts : simple, précis, rapide ; pas besoin d'élaguer (profondeur max fixée, souvent petite) ; évaluation d'erreur directe (cas non sélectionnés = *out-of-bag*) ; gère un grand nombre de features (ex. images) sans sélection a priori ; parallélisable.

> [!example] Application biomédicale
> Aide au diagnostic : features hétérogènes (cliniques, radiologiques, morphologiques, immuno-histologiques), peu de cas vs beaucoup de features (→ sélection nécessaire), classes déséquilibrées et labels possiblement erronés. **Double objectif** : aide au diagnostic (règles précises) ET extraction de connaissance (pertinence des features → biomarqueurs, sous-groupes via feuilles de même classe).

---

> [!quote] À retenir
> Un arbre = des règles logiques lisibles obtenues en **minimisant récursivement l'impureté** (entropie/Gini), avec un **élagage** obligatoire pour généraliser. Son point faible est l'**instabilité**, que les **forêts** convertissent en force par agrégation de nombreux arbres faibles — au prix de l'explicabilité. C'est le contre-modèle « boîte blanche » face aux réseaux de neurones de la Part II.

Voir aussi : [[04 - Non-Bayesian approaches]] · [[02 - General Background]] · [[00 - Index]]
