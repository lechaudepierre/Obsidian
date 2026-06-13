# 16 — Convolutional layer

> [!abstract] Le fil rouge
> La couche convolutive est le pont entre les deux moitiés du cours : c'est **exactement le filtrage linéaire de la Part I** ([[09 - Filter]]), mais dont les poids du noyau sont **appris** au lieu d'être fixés à la main. Le chapitre explique pourquoi c'est bien plus efficace qu'une couche dense (connectivité locale + partage de poids), comment la taille des couches évolue (stride, padding), et comment le **champ récepteur** grandit en profondeur pour produire une représentation de plus en plus **abstraite**.

## 1. Convolution = filtrage linéaire appris

Une couche convolutive reçoit une image $A^{(m-1)}$ ($K_m$ canaux) et produit une nouvelle image $A^{(m)}$ ($O_m$ canaux).

> [!success] L'idée clé
> Si on remplit manuellement les poids du filtre avec, par exemple, le **Sobel horizontal**, la couche convolutive produit exactement l'image filtrée par Sobel. La différence avec la Part I : **ces poids sont appris** par descente de gradient. On peut appliquer **plusieurs filtres** simultanément (un par canal de sortie).

---

## 2. Convolutive vs fully-connected

> [!info] Connectivité locale + partage de poids
> Les réseaux convolutifs exploitent la **corrélation spatiale locale** : chaque neurone n'est connecté qu'à **une petite région** du volume d'entrée (pas à tous les pixels comme en dense). Cela réduit massivement le nombre de paramètres et intègre l'a priori que « ce qui compte est local et se répète partout dans l'image ».

---

## 3. Champ récepteur (receptive field)

> [!tip] Comprendre le champ récepteur
> Le **champ récepteur** d'un neurone = la zone de l'entrée qui l'influence. Un neurone de la couche 2 « voit » un patch $3\times3$ de la couche 1 ; mais un neurone de la couche 3 « voit » indirectement une zone **beaucoup plus grande** de l'image d'origine. En empilant les couches, le champ récepteur **grandit**.

---

## 4. Taille des couches : stride et padding

La convolution n'est pas définie sur les **bords** : avec un noyau $3\times3$, la sortie a 2 lignes et 2 colonnes de moins. Formule générale de la taille de sortie en fonction de l'entrée $W$, de la taille de noyau $K$, du stride $S$ et du padding $P$ :

$$\text{taille de sortie} = \frac{W - K + 2P}{S} + 1$$

> [!info] Les leviers
> - **Stride $S$** : pas de déplacement du filtre. Plus grand → moins de recouvrement des champs récepteurs → sortie spatiale plus petite.
> - **Padding $P$** : ajout de bords (zéros). Le « **same** padding » préserve exactement la taille spatiale de l'entrée.

> [!note] Convention PyTorch
> Les tenseurs sont en $N\,C\,H\,W$ : $N$ = taille du batch (nombre d'images), $C$ = nombre de canaux, $H,W$ = hauteur et largeur.

---

## 5. Abstraction

> [!tip] Pyramide d'abstraction
> Comme le champ récepteur (vis-à-vis de l'entrée) **augmente** avec la profondeur, les couches profondes sont entraînées sur de l'information **de plus en plus abstraite** (concernant une portion de plus en plus grande de l'image). Les premières couches captent des contours/textures locaux, les dernières des structures globales. C'est la contrepartie *apprise* de la hiérarchie de features de la Part I.

---

> [!quote] À retenir
> Une couche convolutive **apprend** des filtres au lieu qu'on les fixe (cf. Sobel/Gabor de la Part I). Elle est efficace grâce à la **connectivité locale** et au **partage de poids**. La taille de sortie suit $\frac{W-K+2P}{S}+1$ (stride réduit, padding préserve). En profondeur, le **champ récepteur grandit** → représentation de plus en plus abstraite. C'est la brique fondatrice de toutes les architectures de [[21 - Classification (CNN)]].

Voir aussi : [[09 - Filter]] · [[15 - Neural networks]] · [[18 - Pooling layer]] · [[21 - Classification (CNN)]] · [[00 - Index]]
