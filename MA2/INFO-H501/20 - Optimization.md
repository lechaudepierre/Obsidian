# 20 — Optimization

> [!abstract] Le fil rouge
> Entraîner un réseau profond, c'est minimiser une perte dans un paysage de millions de paramètres qu'on **ne voit jamais en entier** : on ne dispose que du gradient local. Ce chapitre liste les **obstacles** (minima locaux, vanishing gradient, overfitting) puis la boîte à outils qui les contourne : **dropout** et **batch normalization** (régularisation), **initialisation des poids** (Xavier, He) et **optimiseurs adaptatifs** (Adam).

## 1. Les défis de l'optimisation

> [!warning] Minima locaux
> L'espace de recherche est **immense** (autant de dimensions que de paramètres entraînables), la **vue globale est inaccessible**, et on n'utilise que le **gradient local** → risque de rester coincé.

> [!warning] Vanishing gradient
> Dans les réseaux profonds, les couches proches de la **sortie** apprennent bien mieux que celles proches de l'**entrée**. Cause : la dérivée de la sigmoïde devient petite pour de grandes entrées, et comme ces dérivées se **multiplient** de la sortie vers l'entrée, le gradient décroît drastiquement. Plus il y a de couches, plus il s'évanouit → d'où le recours à **ReLU** ([[17 - Activation functions]]).

> [!warning] Overfitting
> Le réseau devient excellent sur l'entraînement mais incapable de généraliser. Illustré par Bishop : un réseau à 2 couches sur 10 points d'une sinusoïde, avec $M=1, 3, 10$ unités cachées → trop d'unités = surapprentissage (rappel de [[02 - General Background]]).

---

## 2. Dropout (régularisation)

> [!info] Principe
> Pendant l'entraînement, on **« éteint » aléatoirement** des unités (cachées et visibles) à chaque passe. On entraîne donc à chaque fois un **réseau aminci** différent → un réseau avec dropout ≈ entraîner une **collection** de réseaux amincis.

À la **prédiction**, on utilise le réseau complet (non aminci) avec des poids réduits $p\mathbf{w}$.

> [!success] Effets
> Réduit l'overfitting (= forme de **model averaging** efficace), empêche les **co-adaptations** complexes entre neurones, et rend les activations **sparse**. Améliore les perfs dans de nombreux domaines.

> [!warning] Coût
> Entraînement **2-3× plus long** qu'un réseau normal (difficulté introduite par la perte de nœuds).

---

## 3. Batch normalization

> [!info] Le problème visé
> Pendant l'entraînement, la **distribution des entrées de chaque couche change** au fil de la mise à jour des couches précédentes (internal covariate shift). La **batch norm** intègre la **normalisation dans l'architecture**, effectuée pour chaque **mini-batch**.

> [!success] Bénéfices
> Même précision en **14× moins d'étapes**. Travailler en mini-batches : le gradient sur un batch **estime** celui du dataset (qualité ↑ avec la taille), calcul **parallélisable** sur GPU, permet des **learning rates plus élevés**, empêche les petites variations de paramètres de s'amplifier, et **régularise** le modèle.

---

## 4. Initialisation des poids

La performance dépend fortement de l'**initialisation**. Trois règles : ne pas initialiser avec de **petites** valeurs, pas avec des valeurs **identiques**, et fournir une **bonne variance**.

| Méthode | Écart-type / limite | Adaptée à |
|---|---|---|
| Uniforme | ex. $[-0.05, 0.05]$ (défaut PyTorch) | sigmoïde, tanh |
| **Xavier** (normal) | $\sigma = \sqrt{\tfrac{2}{fan_{in}+fan_{out}}}$ | sigmoïde, tanh |
| Xavier (uniform) | $\text{limit} = \sqrt{\tfrac{6}{fan_{in}+fan_{out}}}$ | sigmoïde, tanh |
| **He** (normal) | $\sigma = \sqrt{\tfrac{2}{fan_{in}}}$ | **ReLU** |
| He (uniform) | $\text{limit} = \sqrt{\tfrac{6}{fan_{in}}}$ | **ReLU** |

> [!tip] Comment choisir
> Le bon choix dépend de l'activation : **Xavier** pour sigmoïde/tanh (tient compte de $fan_{in}$ ET $fan_{out}$), **He** pour ReLU (ne compte que $fan_{in}$, car ReLU coupe la moitié des activations). $fan_{in}/fan_{out}$ = nombre d'unités d'entrée/sortie du tenseur de poids.

---

## 5. Optimiseurs : Adam

> [!info] Adam (adaptive moment estimation)
> Au-delà du SGD ([[15 - Neural networks]]), **Adam** adapte le pas d'apprentissage à chaque paramètre à partir d'estimations des **moments** (moyenne et variance) des gradients passés.

> [!tip] Intuition
> Adam combine momentum (mémoire de la direction) et mise à l'échelle par l'amplitude récente des gradients → converge souvent plus vite et plus stablement que le SGD pur, surtout sur des paysages mal conditionnés.

---

> [!quote] À retenir
> Trois obstacles : **minima locaux**, **vanishing gradient** (→ ReLU), **overfitting**. La boîte à outils : **dropout** (model averaging, plus lent), **batch norm** (normaliser par mini-batch → convergence bien plus rapide), **initialisation** adaptée à l'activation (**Xavier** pour sigmoïde/tanh, **He** pour ReLU), et **Adam** (pas adaptatif). Ensemble, ces techniques rendent l'apprentissage profond possible.

Voir aussi : [[15 - Neural networks]] · [[17 - Activation functions]] · [[02 - General Background]] · [[00 - Index]]
