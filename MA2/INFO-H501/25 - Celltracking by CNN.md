# 25 — Celltracking by CNN

> [!abstract] Le fil rouge
> Petit cas d'application concret : détecter et suivre des cellules avec un CNN entraîné sur **très peu** de données. Deux idées portent le chapitre : compenser le manque de données par une **augmentation** astucieuse, et reformuler la détection non pas en classification mais en **régression** (de la présence, puis de la direction vers la cellule).

## 1. Le contexte

Exemple minimal de réseau convolutif pour la **détection et le suivi de cellules** avec un entraînement minimal. Le manque de données annotées est le défi central → d'où le rôle clé de l'augmentation.

---

## 2. Data augmentation

> [!info] Deux techniques
> - **Random crop** : fenêtres aléatoires $64\times64$ extraites du jeu d'entraînement.
> - **Correction gamma aléatoire** des niveaux de gris.

Correction gamma : avec $r = U(0,1)$,

$$\gamma = \begin{cases} 2\,U(0,1) & \text{si } r > \tfrac{1}{2}\\[4pt] \dfrac{1}{0.5 + 0.5\,U(0,1)} & \text{si } r \le \tfrac{1}{2}\end{cases}, \qquad g_{out} = g_{in}^{\,\gamma}$$

> [!tip] À quoi sert la correction gamma
> Faire varier aléatoirement le **contraste / la luminosité** (assombrir ou éclaircir) rend le réseau **robuste** aux variations d'illumination des images de microscopie, à partir d'un petit jeu d'images. Le tirage symétrise les $\gamma$ autour de 1 (éclaircir ↔ assombrir).

---

## 3. Reformuler la détection en régression

> [!success] Régression scalaire
> Au lieu de classer « cellule / pas cellule », le réseau **régresse** une valeur continue : la **probabilité de présence** d'une cellule.

> [!success] Régression vectorielle
> Le réseau régresse aussi un **vecteur** : la **direction vers le centroïde de la cellule la plus proche**. En combinant présence + direction, on localise et on suit les cellules (approche apparentée à HoVernet).

> [!tip] Pourquoi régresser plutôt que classer
> Sortir une carte continue (présence + direction) donne une information **spatiale dense** et différentiable, exploitable pour pointer le centre des cellules même quand elles se touchent — là où une simple classification pixel serait ambiguë.

---

> [!quote] À retenir
> Sur peu de données, deux leviers : **augmentation** (crops aléatoires + correction gamma pour la robustesse au contraste) et **reformulation en régression** — présence (scalaire) + direction vers le centroïde (vecteur) — plutôt qu'en classification. C'est l'application directe des couches convolutives ([[16 - Convolutional layer]]) et de l'esprit U-Net ([[24 - Segmentation (deep)]]) au suivi cellulaire.

Voir aussi : [[12 - Object tracking]] · [[24 - Segmentation (deep)]] · [[16 - Convolutional layer]] · [[00 - Index]]
