# 11 — Detection

> [!abstract] Le fil rouge
> Détecter ≠ délimiter : on cherche **où** se trouve une caractéristique, pas son contour précis. Souvent c'est une étape de **prétraitement** pour focaliser l'attention. Le chapitre déroule quatre détecteurs classiques, du plus simple au plus structuré : motif (pattern matching), coins (Harris), lignes (Hough), visages (Haar/Viola-Jones).

## 1. Pattern matching

Comparer le voisinage de chaque pixel à un **motif** $h$ via un critère de ressemblance. Trois critères (plus la valeur est grande, meilleur le match) :

$$C_2(u,v) = \frac{1}{\sum_{(i,j)\in V}|f(i+u,j+v)-h(i,j)|}, \qquad C_3(u,v) = \frac{1}{\sum_{(i,j)\in V}[f(i+u,j+v)-h(i,j)]^2}$$

> [!tip] Intuition
> On glisse le template sur l'image et on mesure l'écart local. Écart faible → match. Simple mais **non robuste** à l'échelle, la rotation, l'éclairage.

---

## 2. Coins de Harris

Les **coins** sont des points robustes aux petites déformations (pose caméra, perspective) → utiles pour l'appariement de points (stitching, stéréo).

> [!info] Principe
> Un coin = un point où l'image a un **fort gradient dans deux directions perpendiculaires**. On balaie une fenêtre $w$ et on mesure la variation d'intensité :
> $$E(u,v) = \sum_{x,y} w(x,y)\,[I(x+u,y+v) - I(x,y)]^2$$

Par développement de Taylor ($I_x, I_y$ = dérivées image, calculées par convolution = filtrage linéaire), on obtient une forme matricielle :

$$E(u,v) \approx \begin{bmatrix} u & v\end{bmatrix} M \begin{bmatrix} u\\ v\end{bmatrix}, \qquad M = \sum_{x,y} w(x,y)\begin{bmatrix} I_x^2 & I_x I_y\\ I_x I_y & I_y^2\end{bmatrix}$$

Harris évite de calculer explicitement les valeurs propres $\lambda_1,\lambda_2$ de $M$ via :

$$R = \det(M) - k\,(\text{trace}\,M)^2, \quad \det(M)=\lambda_1\lambda_2,\ \text{trace}(M)=\lambda_1+\lambda_2$$

> [!tip] Lire $R$ via les valeurs propres
> - $|R|$ petit ($\lambda_1,\lambda_2$ petits) → région **plate**.
> - $R<0$ ($\lambda_1\gg\lambda_2$) → **bord**.
> - $R$ grand ($\lambda_1,\lambda_2$ grands et $\lambda_1\sim\lambda_2$) → **coin**.

> [!warning] Limite
> Harris **n'est pas robuste à l'échelle** : un coin à une échelle peut ne plus l'être à une autre.

---

## 3. Transformée de Hough (détection de lignes)

> [!info] Idée clé : changer d'espace
> Au lieu de relier des points voisins, Hough **regroupe les pixels appartenant à une même ligne**, même si elle est fragmentée. Une droite $y = ax+b$ s'écrit en représentation **normale** :
> $$x\cos\theta + y\sin\theta = \rho$$
> Une ligne est définie par $(\theta,\rho)$. Chaque pixel vote pour toutes les lignes passant par lui dans le plan $(\theta,\rho)$ ; les pics de votes = les lignes présentes.

---

## 4. Détection de visages — Haar / Viola-Jones

> [!info] Features de Haar
> Différences locales simples mesurées dans des **régions rectangulaires** adjacentes (claires vs sombres). En variant type, taille et position, on génère des **centaines** de features, évaluées sur des images avec/sans visage.

> [!success] Astuce : l'image intégrale
> Pour sommer rapidement n'importe quel rectangle, on précalcule l'**image intégrale** :
> $$I(x,y) = \sum_{x'\le x,\, y'\le y} i(x',y')$$
> La somme sur un rectangle $ABCD$ se calcule alors en **4 accès** : $I(D)+I(A)-I(B)-I(C)$. Puis du ML sélectionne les features les plus discriminantes.

---

> [!quote] À retenir
> La détection localise sans délimiter. **Pattern matching** (glisser un template, fragile), **Harris** (coins via les valeurs propres de la matrice de structure $M$, sensible à l'échelle), **Hough** (voter dans l'espace des paramètres $(\theta,\rho)$ pour des lignes), **Viola-Jones** (features de Haar + image intégrale pour des sommes en temps constant). Ces détecteurs préparent le terrain de [[13 - Feature extraction]] et trouvent leur équivalent profond dans [[23 - Object Detection]].

Voir aussi : [[09 - Filter]] · [[13 - Feature extraction]] · [[23 - Object Detection]] · [[00 - Index]]
