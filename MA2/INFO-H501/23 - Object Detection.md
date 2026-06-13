# 23 — Object Detection

> [!abstract] Le fil rouge
> Détecter = localiser (point ou **bounding box**), pas délimiter finement. Le défi propre à la détection profonde : un nombre **variable** d'objets par image, ce qui interdit une simple couche fully-connected en sortie. La famille **R-CNN** résout cela en deux temps — *proposer des régions* puis *les classer* — la suite de la lignée (Fast/Faster R-CNN, YOLO) ne faisant qu'accélérer ce schéma.

## 1. Qu'est-ce que la détection ?

Localiser une structure par un **point** ou une **bounding box**, sans contour précis ; **rapidité** souvent recherchée. Usages : détection d'obstacles, présence/absence, ou **prétraitement** pour concentrer l'attention sur des régions d'intérêt.

> [!warning] Pourquoi pas une simple couche fully-connected en sortie ?
> Le nombre d'instances varie d'une image à l'autre → une sortie FC de taille fixe ne convient pas. Il faut un mécanisme gérant un **nombre variable** de détections.

---

## 2. R-CNN (2014)

> [!info] Décomposer le problème
> R-CNN sépare la détection en deux sous-problèmes :
> - **localiser** les objets avec un réseau profond ;
> - entraîner un modèle de **forte capacité** avec **peu** de données annotées.

Trois modules :
1. génération de **propositions de régions** indépendantes de la catégorie (par *selective search*) ;
2. un **grand CNN** qui extrait un vecteur de features de **longueur fixe** (4096-d) de chaque région ;
3. un ensemble de **SVM linéaires** spécifiques à chaque classe.

> [!info] Extraction de features
> Chaque région est redimensionnée à un patch fixe $227\times227$, propagé dans 5 couches convolutives + 2 FC (réseau type AlexNet, [[21 - Classification (CNN)]]) → vecteur 4096-d.

### Régression de bounding-box
Après scoring par les SVM, on **affine** la boîte via un régresseur CNN spécifique à la classe. Pour une paire (proposition $P$, vérité $G$), les cibles de régression :

$$t_x = \frac{G_x - P_x}{P_w}, \quad t_y = \frac{G_y - P_y}{P_h}, \quad t_w = \log\frac{G_w}{P_w}, \quad t_h = \log\frac{G_h}{P_h}$$

> [!tip] Lecture de ces cibles
> $t_x, t_y$ = **décalage** du centre, normalisé par la taille de la proposition ; $t_w, t_h$ = **facteur d'échelle** en log. On n'apprend donc pas la boîte absolue mais une **correction relative** de la proposition — plus simple et plus stable à apprendre.

> [!note] Benchmark PASCAL VOC
> Standard d'évaluation en détection/reconnaissance : dataset d'images annotées + procédures d'évaluation standardisées (20 classes, images récupérées via flickr).

---

## 3. La lignée : Fast / Faster R-CNN, YOLO

> [!info] Évolutions
> Le cours mentionne **Fast R-CNN** (Girshick 2015), **Faster R-CNN** (Ren et al. 2016) et **YOLO** — qui accélèrent le schéma R-CNN (notamment en intégrant la proposition de régions dans le réseau), mais **ne sont pas détaillés** dans le texte.

---

> [!quote] À retenir
> La détection gère un **nombre variable** d'objets (d'où l'inadéquation d'une sortie FC fixe). **R-CNN** = *propositions de régions* (selective search) → *CNN* (features 4096-d) → *SVM* + **régression de bbox** (corrections relatives $t_x,t_y,t_w,t_h$). Fast/Faster R-CNN et YOLO accélèrent ce pipeline.

Voir aussi : [[11 - Detection]] · [[21 - Classification (CNN)]] · [[24 - Segmentation (deep)]] · [[00 - Index]]
