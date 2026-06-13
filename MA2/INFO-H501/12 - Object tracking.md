# 12 — Object tracking

> [!abstract] Le fil rouge
> Suivre un objet dans une séquence d'images se fait selon deux philosophies opposées : **top-down** (on suppose qu'un objet existe et on cherche sa position la plus probable → mean-shift) ou **bottom-up** (aucune hypothèse, on calcule le mouvement de tous les pixels → flux optique). Les deux reposent in fine sur les dérivées de l'image et une résolution itérative ou par moindres carrés.

## 1. Mean-shift (top-down, basé modèle)

> [!info] Hypothèse
> Le mean-shift suppose l'**existence et l'unicité** d'un objet cible, et cherche sa position la plus probable à partir d'une position initiale. C'est un algorithme de détection de **mode local** (maximum de densité).

Estimateur de densité local par noyau $K$ :

$$f(\mathbf{x}) = \frac{1}{nh^d}\sum_{i=1}^n K\!\left(\frac{\mathbf{x}-\mathbf{x}_i}{h}\right)$$

On cherche le maximum $\nabla f(\mathbf{x}) = 0$. Avec un noyau isotrope et $g = -k'$, le gradient s'annule lorsque le **vecteur de mean-shift** est nul :

$$\mathbf{m}_h(\mathbf{x}) = \frac{\sum_i \mathbf{x}_i\, g(\|\frac{\mathbf{x}-\mathbf{x}_i}{h}\|^2)}{\sum_i g(\|\frac{\mathbf{x}-\mathbf{x}_i}{h}\|^2)} - \mathbf{x}$$

> [!tip] L'intuition en une phrase
> Ce terme est le **centroïde (barycentre pondéré) du voisinage** moins le point courant. L'itération $\mathbf{x} \leftarrow \mathbf{m}_h(\mathbf{x})$ revient donc à **déplacer le centre de la fenêtre vers le centre de masse local**, encore et encore, jusqu'à atteindre le mode. C'est « grimper la colline de densité ».

---

## 2. Flux optique (bottom-up)

> [!info] Hypothèse de constance d'intensité
> Aucune hypothèse d'objet : la séquence est vue comme un **flot de pixels en mouvement**. On suppose qu'un pixel garde son intensité en se déplaçant :
> $$I(x,y,t) = I(x+\Delta x, y+\Delta y, t+\Delta t)$$

Développement de Taylor → **équation du flux optique** :

$$\frac{\partial I}{\partial x}V_x + \frac{\partial I}{\partial y}V_y + \frac{\partial I}{\partial t} = 0 \quad\Longleftrightarrow\quad \nabla I \cdot \vec{V} = -I_t$$

où $(V_x,V_y)$ est la vitesse (le flux) cherchée.

> [!warning] Le problème d'ouverture (aperture problem)
> Une seule équation, **deux inconnues** $(V_x,V_y)$ → insoluble en l'état. Il faut une contrainte supplémentaire.

> [!success] Solution de Lucas–Kanade
> On suppose le flux **constant dans un petit voisinage** → on écrit une équation par pixel voisin (ex. $3\times3$ → 9 équations, mêmes 2 inconnues). Système surdéterminé résolu par **moindres carrés** :
> $$\vec{v} = (A^TA)^{-1}A^T b$$
> La matrice $A^TA$ fait apparaître les mêmes sommes de produits de dérivées $\sum I_x^2, \sum I_xI_y, \sum I_y^2$ que la matrice de Harris ([[11 - Detection]]) — d'où le lien : on peut suivre fiablement les coins.

---

> [!quote] À retenir
> Deux paradigmes : **mean-shift** = top-down, on glisse une fenêtre vers le centroïde local (suivi d'un objet supposé unique) ; **flux optique** = bottom-up, on résout $\nabla I\cdot\vec V = -I_t$ par voisinage (Lucas-Kanade) pour contourner le problème d'ouverture. Tous deux carburent aux dérivées de l'image.

Voir aussi : [[11 - Detection]] · [[09 - Filter]] · [[25 - Celltracking by CNN]] · [[00 - Index]]
