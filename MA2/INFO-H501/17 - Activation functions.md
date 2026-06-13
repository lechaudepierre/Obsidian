# 17 — Activation functions

> [!abstract] Le fil rouge
> Ce court chapitre résout un problème posé dans [[15 - Neural networks]] : les activations classiques (sigmoïde, tanh) **saturent** et tuent le gradient dans les réseaux profonds. La réponse est **ReLU**, dont la simplicité même règle le problème et permet l'apprentissage profond moderne.

## 1. Le rappel du problème

Chaque neurone applique une activation à sa somme pondérée ; il faut du **non-linéaire** pour apprendre des problèmes complexes. Les classiques :

$$\text{sigmoïde}: f(x) = \frac{1}{1+e^{-x}} \in [0,1], \qquad \text{tanh}: f(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}} \in [-1,1]$$

> [!warning] Saturation = vanishing gradient
> Loin de 0, la pente de la sigmoïde/tanh devient **plate** → dérivée quasi nulle → le gradient « disparaît » en remontant les couches → les réseaux profonds n'apprennent plus efficacement.

---

## 2. ReLU (Rectified Linear Unit)

$$\text{ReLU}(x) = \max(0, x)$$

> [!success] Pourquoi ReLU s'est imposée
> Pour $x>0$, sa dérivée vaut **1** (pas de saturation côté positif) → le gradient passe sans s'atténuer, ce qui permet d'entraîner des réseaux profonds. Devenue l'activation **préférée** pour de nombreuses applications. Elle est aussi très peu coûteuse à calculer.

> [!tip] Lecture intuitive
> ReLU = « laisse passer le positif, coupe le négatif ». Appliquée à une couche filtrée (ex. après un Sobel), elle ne garde que les réponses positives → introduit la non-linéarité sans aplatir le gradient.

Il existe d'autres variantes d'activation (voir la documentation PyTorch pour le catalogue complet : Leaky ReLU, ELU, etc.).

---

> [!quote] À retenir
> Sigmoïde et tanh **saturent** → vanishing gradient → frein à la profondeur. **ReLU** ($\max(0,x)$) a une dérivée constante de 1 sur les positifs, évite la saturation et débloque l'entraînement profond. C'est une pièce du même puzzle que les techniques de [[20 - Optimization]].

Voir aussi : [[15 - Neural networks]] · [[20 - Optimization]] · [[00 - Index]]
