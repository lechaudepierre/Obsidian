# 14 — Classification

> [!abstract] Le fil rouge
> Classer = affecter une étiquette à un item selon ses features. Le chapitre décline ce problème à trois niveaux : le **pixel** (reconnaissance de visage par PCA / eigenfaces), l'**objet** segmenté (reconnaissance par distance entre descripteurs de Hu), et l'**image entière** (Bag of visual Words). Le fil conducteur technique est la **PCA** : projeter un espace de très grande dimension sur un sous-espace en gardant le maximum de variance.

## 1. Classification de pixels — eigenfaces / PCA

Reconnaissance de visage : chaque image $67\times57$ donne un vecteur de $3819$ valeurs. Le problème devient « trouver le point le plus proche dans un espace à 3819 dimensions ».

> [!warning] La malédiction de la dimension
> L'espace est énorme et le **nombre de cas peut être inférieur au nombre de dimensions** → calculs difficiles, risque de surapprentissage. D'où le besoin de **réduire la dimension**.

> [!info] PCA — principe
> La **PCA** projette les données sur un sous-espace de plus faible dimension en **maximisant la variance** de la projection (pour préserver le plus d'information). Pour une projection sur l'axe unitaire $\mathbf{u}_1$, la variance projetée vaut $\mathbf{u}_1^T \mathbf{S}\,\mathbf{u}_1$, où $\mathbf{S}$ est la matrice de covariance :
> $$\mathbf{S} = \frac{1}{N}\sum_{n=1}^N (\mathbf{x}_n - \overline{\mathbf{x}})(\mathbf{x}_n - \overline{\mathbf{x}})^T$$

En maximisant sous contrainte $\mathbf{u}_1^T\mathbf{u}_1 = 1$ (multiplicateur de Lagrange), on obtient :

$$\mathbf{S}\,\mathbf{u}_1 = \lambda_1 \mathbf{u}_1$$

> [!tip] Le résultat clé
> La solution est un **vecteur propre** de $\mathbf{S}$, et la variance préservée = sa **valeur propre** $\lambda_1$. Donc : on range les vecteurs propres par valeur propre décroissante → les premières composantes principales. Appliqués aux visages, ces vecteurs propres sont les **eigenfaces**.

> [!success] Astuce calculatoire
> La taille de $\mathbf{S}$ explose avec la résolution, mais son **rang est limité par le nombre d'images**. On peut donc diagonaliser $\mathbf{T}\mathbf{T}^T$ (taille = nombre d'images) au lieu de $\mathbf{T}^T\mathbf{T}$ (taille = nombre de pixels) et récupérer les vecteurs propres par $\mathbf{v}_i = \mathbf{T}^T\mathbf{u}_i$.

---

## 2. Classification d'objets

Une fois les objets segmentés, on les décrit (forme, intensité) puis on reconnaît. Exemple : reconnaître des lettres en extrayant les **moments invariants de Hu** ([[13 - Feature extraction]]) puis en évaluant la **distance** entre vecteurs de descripteurs → on cherche l'objet le plus ressemblant (plus proche voisin, cf. [[04 - Non-Bayesian approaches]]).

---

## 3. Classification d'images — Bag of visual Words (BoW)

> [!info] Analogie avec le texte
> Initialement développé pour la classification de **texte**. Transposé à l'image :
> - détection de **points remarquables** (cf. Harris, [[11 - Detection]]),
> - extraction de petits **patchs** d'image,
> - construction d'un **dictionnaire de mots visuels** par **clustering** (k-means),
> - une image est caractérisée par la **distribution** de ses mots visuels (histogramme),
> - comparer deux images = comparer leurs distributions.

> [!note] Récap de l'approche classique
> Le pipeline complet vu en Part I : filtrage linéaire → détection simple → segmentation (pixel/bord/région) → extraction de features → classification.

---

> [!quote] À retenir
> Classer se fait au niveau **pixel** (eigenfaces : PCA = vecteurs propres de la covariance, gardant la variance maximale), **objet** (distance entre moments de Hu), ou **image** (BoW : dictionnaire de patchs par clustering + comparaison d'histogrammes). La **PCA** est l'outil-clé contre la malédiction de la dimension, et reviendra en miroir avec l'autoencodeur ([[22 - Autoencoder]]).

Voir aussi : [[13 - Feature extraction]] · [[04 - Non-Bayesian approaches]] · [[22 - Autoencoder]] · [[00 - Index]]
