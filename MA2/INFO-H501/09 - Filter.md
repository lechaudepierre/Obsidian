# 09 — Filter

> [!abstract] Le fil rouge
> Avant de classer ou segmenter, il faut **transformer** l'image. Ce chapitre fonde toute la Part I : une image numérique est une matrice de valeurs, et le **filtre linéaire = convolution** par un petit noyau. Selon le noyau, on lisse (passe-bas), on rehausse les contours (passe-haut), ou on analyse les textures (banc de Gabor). C'est aussi, sans le dire, l'opération de base des CNN de la Part II ([[16 - Convolutional layer]]).

## 1. L'image numérique

Une image numérique est une **matrice de nombres** représentant l'intensité lumineuse dans un espace 2D ; chaque élément est un **pixel**. On peut associer un *vecteur* de valeurs à chaque pixel (ex. couleur = triplet $(R,G,B)$). La valeur est **discrète** : ex. niveaux de gris codés sur 1 octet → 256 niveaux.

---

## 2. Filtrage et convolution

> [!info] Définition centrale
> Le filtre linéaire (= convolution linéaire) consiste à appliquer une **convolution** entre l'image et un **élément structurant** : une petite matrice de nombres qui définit localement le filtre. Le type d'effet dépend entièrement des poids du noyau.

### Filtre passe-bas (low-pass)
Moyenne locale : tous les poids identiques (ex. noyau $3\times3$ ou $5\times5$ de 1). Effet : **floutage / lissage**, réduction du bruit. Plus le noyau est grand, plus l'image est lissée.

### Filtre passe-haut (high-pass)
Rehausse les hautes fréquences = les **contours**. Exemple des filtres de **Sobel** (un pour les contours horizontaux, un pour les verticaux) :

$$\text{Sobel}_h = \begin{bmatrix} -1 & -2 & -1\\ 0 & 0 & 0\\ 1 & 2 & 1\end{bmatrix}, \qquad \text{Sobel}_v = \text{Sobel}_h^{\,T}$$

> [!tip] Comment lire un noyau
> Somme des poids = 1 (ou positive) → passe-bas (moyenne). Somme des poids = 0 avec des signes opposés → passe-haut (différence = dérivée → détecte les variations = contours). Sobel approxime la **dérivée** de l'image.

---

## 3. Banc de filtres de Gabor

Exemple plus riche : les filtres de **Gabor**, paramétrés par $\lambda,\theta,\psi,\sigma,\gamma$ (fréquence spatiale, orientation, phase, échelle, aspect) :

$$g(x,y;\lambda,\theta,\psi,\sigma,\gamma) = \exp\!\left(-\frac{x'^2+\gamma^2 y'^2}{2\sigma^2}\right)\exp\!\left(i\left(2\pi\frac{x'}{\lambda}+\psi\right)\right)$$

> [!example] Banc de filtres (filter bank)
> En faisant varier les paramètres, on génère **plusieurs filtres** = un *banc de filtres*. Différentes textures répondent différemment à chaque filtre → on peut **caractériser une texture** par ses réponses. Ce lien est réutilisé dans [[13 - Feature extraction]] (texture features).

> [!tip] Intuition Gabor
> Un Gabor = une sinusoïde orientée modulée par une gaussienne. Il réagit fortement quand l'image contient localement une oscillation à **la même orientation et fréquence** que le filtre — comme un détecteur de « rayures » accordé.

---

> [!quote] À retenir
> Filtrer une image = la **convoluer** par un noyau. Le noyau seul décide de l'effet : moyenne (passe-bas, lissage), différence (passe-haut, contours type Sobel), ou Gabor orienté (texture). Retiens que la **même opération de convolution** sera apprise automatiquement par les couches convolutives des CNN — la Part I fait à la main ce que la Part II apprendra.

Voir aussi : [[10 - Segmentation]] · [[13 - Feature extraction]] · [[16 - Convolutional layer]] · [[00 - Index]]
