# 15 — Neural networks

> [!abstract] Le fil rouge
> Ce chapitre ouvre le « changement de paradigme » : au lieu d'extraire des features à la main (Part I), on laisse un réseau **ajuster des poids** pour transformer des entrées en sorties. Tout le pipeline d'apprentissage est ici en miniature : une **couche dense** (somme pondérée + activation), une **sortie** transformée en probabilités (softmax), une **fonction de perte** (cross-entropy) qui mesure l'erreur, et un **optimiseur** (SGD) qui ajuste les poids. Chaque brique sera approfondie ensuite.

## 1. La couche dense (perceptron)

Exemple fil rouge : classification des **Iris** (3 classes, 4 mesures). Un perceptron à une couche : 4 neurones d'entrée, 3 de sortie, chaque connexion ayant un **poids**. Chaque neurone calcule la **somme pondérée** de ses entrées.

> [!info] One-hot encoding
> Les classes sont encodées en **one-hot** : Setosa = $[1,0,0]$, Versicolor = $[0,1,0]$, Virginica = $[0,0,1]$. L'idée d'apprentissage : régler les poids pour que la somme pondérée des entrées produise une sortie aussi proche que possible de la classe réelle.

« Dense » / « fully-connected » = chaque neurone d'une couche est connecté à **tous** ceux de la couche suivante.

---

## 2. Fonctions d'activation

Chaque neurone applique une **fonction d'activation** à sa somme pondérée. Une activation linéaire suffit pour la régression, mais pour des problèmes complexes on préfère des fonctions **non-linéaires**.

> [!info] Sigmoïde et tanh
> **Sigmoïde** (logistique), sortie dans $[0,1]$ :
> $$f(x) = \frac{1}{1+e^{-x}}$$
> **Tangente hyperbolique**, sortie dans $[-1,1]$ :
> $$f(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$$

> [!warning] Le problème de saturation → gradient qui disparaît
> Ces deux fonctions **saturent** : loin de 0, la pente devient plate. C'est le **vanishing gradient problem**, qui empêche les réseaux **profonds** d'apprendre efficacement. C'est exactement ce qui motivera ReLU ([[17 - Activation functions]]) et les techniques de [[20 - Optimization]].

---

## 3. Sortie du réseau — softmax

La classe prédite = neurone de sortie le plus élevé. Mais on préfère une **probabilité** de chaque classe → fonction **softmax** :

$$\sigma(z_i) = \frac{e^{z_i}}{\sum_{j=1}^K e^{z_j}}$$

> [!tip] Ce que fait softmax
> Transforme $K$ valeurs quelconques en $K$ valeurs **positives sommant à 1** → interprétables comme des probabilités. À reconnaître : c'est la même forme que la **régression logistique** multi-classe ([[03 - Bayesian approach]]).

---

## 4. Fonction de perte — cross-entropy

Pour ajuster les poids, il faut **mesurer l'erreur** entre sortie et supervision. C'est le rôle de la **loss**. Pour la classification on utilise la **cross-entropy** :

$$H(p,q) = -\sum_{x\in\chi} p(x)\log q(x)$$

où $p$ et $q$ sont les distributions d'entrée (vérité) et de sortie (prédiction).

> [!tip] Intuition
> La cross-entropy est petite quand la distribution prédite $q$ colle à la vraie $p$, et explose quand le réseau est **confiant et faux**. C'est ce signal qu'on minimise.

---

## 5. Optimisation des poids — SGD

> [!info] Descente de gradient stochastique
> Le **SGD** (Stochastic Gradient Descent) remplace le vrai gradient (calculé sur **tout** le dataset) par une **estimation** calculée sur un sous-ensemble aléatoire (mini-batch). Paramètres principaux : **learning rate**, **momentum**, **dampening**, **weight decay**.

> [!tip] Pourquoi « stochastique »
> Calculer le gradient sur tout le dataset à chaque pas est trop coûteux ; l'estimer sur un mini-batch est bien plus rapide et introduit un bruit utile qui aide à sortir des mauvais minima. Détails et variantes (Adam) dans [[20 - Optimization]].

---

> [!quote] À retenir
> Un réseau apprend en réglant ses **poids**. Le cycle : couche dense (somme pondérée) → **activation non-linéaire** (sinon le réseau reste linéaire) → **softmax** (probabilités) → **cross-entropy** (erreur) → **SGD** (mise à jour). Le grand obstacle introduit ici — la **saturation / vanishing gradient** des sigmoïdes — est le fil rouge qui justifie tout le reste de la Part II.

Voir aussi : [[03 - Bayesian approach]] · [[16 - Convolutional layer]] · [[17 - Activation functions]] · [[20 - Optimization]] · [[00 - Index]]
