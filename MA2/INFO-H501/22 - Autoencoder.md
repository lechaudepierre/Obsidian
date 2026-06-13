# 22 — Autoencoder

> [!abstract] Le fil rouge
> L'autoencodeur est la version **non supervisée** et **non-linéaire** de la PCA ([[14 - Classification]]) : un réseau qui apprend à se **reconstruire lui-même** en passant par un **goulot d'étranglement**. Ce goulot force le réseau à compresser l'information dans un **espace latent**, donc à capturer la structure des données. C'est le socle du débruitage et, par extension, de la génération (VAE).

## 1. Principe : compresser en se reconstruisant

> [!info] Architecture en sablier
> On présente $\mathbf{X}$ à la fois en **entrée et en sortie cible** (apprentissage non supervisé). Le réseau a un **goulot** au milieu, la couche $\mathbf{z}$ = **espace latent**. Trois parties :
> - **Encodeur** (entrée → latent),
> - **Espace latent** $\mathbf{z}$ (la version compressée),
> - **Décodeur** (latent → reconstruction $\mathbf{X'}$).

> [!tip] Pourquoi ça compresse quelque chose d'utile
> Si $\mathbf{z}$ est **plus petit** que $\mathbf{X}$, de l'information est forcément perdue — mais comme les données ont une **structure** (cohérence spatiale, redondances), le réseau apprend à garder l'essentiel pour que $\mathbf{X'} \approx \mathbf{X}$. Le latent devient une représentation compacte et significative.

**Undercomplete** ($\mathbf{z}$ plus petit que $\mathbf{X}$) : le cas le plus utile (extraction de features, débruitage). **Overcomplete** ($\mathbf{z}$ plus grand) : nécessite d'autres contraintes. En général encodeur et décodeur ont **plusieurs couches**.

> [!note] Autoencodeur vs PCA
> Analogue à la PCA (projection sur un sous-espace de plus faible dimension), mais l'autoencodeur peut être **non-linéaire** (plusieurs couches + activations), donc plus expressif que la projection linéaire de la PCA.

---

## 2. Comment forcer la compression

Deux leviers :
- un **goulot** dans l'architecture (latent plus petit), ou
- des **contraintes dans l'optimisation** (ex. terme de **régularisation** dans la loss).

> [!example] Autoencodeur sparse
> On peut imposer la **sparsité** en ajoutant à la fonction de coût une **pénalité** pour les réseaux non-sparse (Andrew Ng) — on pousse la plupart des activations latentes vers zéro.

---

## 3. Application : débruitage d'images

En entraînant à reconstruire une image **propre** à partir d'une version **bruitée**, l'autoencodeur apprend à **enlever le bruit** (le bruit n'a pas de structure apprenable, donc il est « jeté » au passage du goulot).

---

## 4. Limites et variational autoencoders

> [!warning] Limites de l'autoencodeur classique
> - Ne reconstruit bien que les **types d'images sur lesquels il a été entraîné**.
> - L'espace latent **n'est pas continu** → on **ne peut pas générer** de nouvelles images directement à partir d'un vecteur latent.

> [!success] Variational autoencoders (VAE)
> Réponse à cette limite : les **VAE** structurent l'espace latent pour le rendre continu et **génératif** (mentionnés, voir Pinheiro Cinelli et al. 2021).

---

> [!quote] À retenir
> Un autoencodeur apprend à **se reconstruire** via un **goulot latent** → compression non supervisée qui capture la structure des données (PCA non-linéaire). Undercomplete pour extraire des features / débruiter. Sa **limite** — latent non continu, pas de génération — motive les **VAE**.

Voir aussi : [[14 - Classification]] · [[16 - Convolutional layer]] · [[24 - Segmentation (deep)]] · [[00 - Index]]
