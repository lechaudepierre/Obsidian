# 03 — Bayesian approach

> [!abstract] Le fil rouge
> L'approche bayésienne donne le classifieur **optimal** : celui qui minimise la probabilité d'erreur en choisissant la classe la **plus probable** au vu de la position dans l'espace des features. Tout le chapitre découle de cette idée, et se divise selon **comment** on estime ces probabilités : directement (régression logistique), ou indirectement via les densités des classes (LDA, gaussiennes, méthodes non-paramétriques).

## 1. Le classifieur bayésien

On veut minimiser la probabilité d'erreur de classification :

$$P_e = \sum_i \sum_{i\ne j} P(C(\mathbf{x}) = \omega_i, \mathbf{x} \in \omega_j)$$

**Solution** : choisir la classe la plus probable connaissant $\mathbf{x}$ (règle du *maximum a posteriori*) :

$$C(\mathbf{x}) = \underset{j}{\mathrm{argmax}}\ P(\omega_j \mid \mathbf{x})$$

où $P(\omega_j \mid \mathbf{x})$ est la **probabilité a posteriori** (= fonction discriminante). Via le **théorème de Bayes**, c'est équivalent à :

$$C(\mathbf{x}) = \underset{j}{\mathrm{argmax}}\ \big[ P(\omega_j)\, P(\mathbf{x}\mid\omega_j)\big]$$

> [!info] Deux familles d'approches
> - **Directe** : estimer directement le posterior $P(\omega_j \mid \mathbf{x})$ → *régression logistique*.
> - **Indirecte** : estimer les densités de classe $P(\mathbf{x}\mid\omega_j)$ et les prior $P(\omega_j)$ (prévalence des classes), puis appliquer Bayes → *LDA, gaussiennes, non-paramétrique*.

---

## 2. Méthode directe : régression logistique

Méthode **semi-paramétrique** modélisant directement le posterior par une distribution logistique :

$$\hat{P}(\omega_k \mid \mathbf{x}) = \frac{\exp(\mathbf{w}_k^\top \mathbf{x'})}{\sum_{j=1}^{q} \exp(\mathbf{w}_j^\top \mathbf{x'})}, \quad \mathbf{x'} = (1, x_1, \dots, x_p)^\top$$

> [!tip] À reconnaître
> C'est exactement la fonction **softmax** ! La régression logistique est l'une des meilleures méthodes pour trouver des frontières **linéaires**, et ses extensions non-linéaires sont... les réseaux de neurones (voir [[15 - Neural networks]]).

---

## 3. Méthode indirecte paramétrique : LDA et gaussiennes

**LDA (Linear Discriminant Analysis)** : méthode indirecte et paramétrique, modélise $P(\mathbf{x}\mid\omega_j)$ par des **gaussiennes** centrées sur les moyennes de classe, avec une **même matrice de variance-covariance $S$** pour toutes les classes.

> [!note] Lien géométrique
> Si les prior $P(\omega_j)$ sont égaux, LDA revient à affecter la classe dont le **centroïde est le plus proche au sens de la distance de Mahalanobis** ($M = S^{-1}$) = règle géométrique de Fisher.

Gaussienne multivariée (dimension $p$) :

$$P(\mathbf{x}\mid \omega_k) = \frac{1}{(2\pi)^{p/2}|\boldsymbol{\Sigma}_k|^{1/2}}\exp\!\left[-\tfrac{1}{2}(\mathbf{x}-\boldsymbol{\mu}_k)^\top\boldsymbol{\Sigma}_k^{-1}(\mathbf{x}-\boldsymbol{\mu}_k)\right]$$

avec $\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k$ estimés sur les données. Si chaque classe a **sa propre** $\boldsymbol{\Sigma}_k$, les frontières deviennent **non-linéaires** (extension de LDA).

---

## 4. Méthodes non-paramétriques (« model-free »)

On estime la densité localement, sans hypothèse de forme. Dans un hypercube/hypersphère $H(\mathbf{x})$ de volume $V$ centré sur $\mathbf{x}$, contenant $k$ des $N$ points :

$$p(\mathbf{x}) \approx \frac{k}{NV}$$

> [!warning] La difficulté centrale : la taille de $H(\mathbf{x})$
> Trop petit → estimation bruitée ; trop grand → trop lissé. Deux stratégies :
> - Fixer $V$ et compter $k$ → **fenêtres de Parzen**.
> - Fixer $k$ et calculer $V$ → **k plus proches voisins** (voir [[04 - Non-Bayesian approaches]]).

> [!tip] Alternative à noyau
> Plutôt que de fixer $V$ ou $k$, utiliser une fonction décroissant avec la distance, ex. gaussienne sphérique $\Phi(\mathbf{y}) = \exp\!\big[-\tfrac{\|\mathbf{y}-\mathbf{x}\|^2}{2s^2}\big]$ — c'est l'idée des méthodes à noyau et des réseaux **RBF**.

> [!danger] Limite des non-paramétriques
> Elles exigent **beaucoup** de données d'entraînement pour être fiables, surtout en grande dimension (fléau de la dimension).

---

> [!quote] À retenir
> Le classifieur bayésien est l'**optimum théorique** (minimise $P_e$). En pratique on ne connaît pas les vraies probabilités, alors on les estime : *directement* (logistique/softmax → frontières linéaires) ou *indirectement* via les densités (LDA gaussien, ou non-paramétrique au prix de beaucoup de données). C'est le socle probabiliste auquel se compareront ensuite arbres et réseaux.

Voir aussi : [[02 - General Background]] · [[04 - Non-Bayesian approaches]] · [[15 - Neural networks]] · [[00 - Index]]
