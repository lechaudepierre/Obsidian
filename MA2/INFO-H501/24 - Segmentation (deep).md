# 24 — Segmentation (deep)

> [!abstract] Le fil rouge
> Segmenter avec un CNN = classer **chaque pixel** (l'idée de [[10 - Segmentation]], version apprise). Le problème : un CNN de classification produit **une seule étiquette** pour toute l'image. La solution se construit par étapes : remplacer les couches denses par des convolutions $1\times1$ (réseau **entièrement convolutif**), puis ajouter de l'**upsampling** pour reconstruire une carte à la résolution de l'image. **U-Net** pousse cette idée à son aboutissement.

## 1. De la classification à la segmentation

> [!info] Le raisonnement par étapes
> 1. Conv + **fully-connected** → sortie = **une classe**, image d'entrée de taille fixe.
> 2. Conv + **convolution $1\times1$** (au lieu du FC) → si même taille d'entrée, sortie $1\times1$ ; mais le réseau accepte maintenant n'importe quelle taille.
> 3. Image d'entrée **plus grande** → sortie **plus grande** que $1\times1$ (réduite par les convolutions/pooling).
> 4. Ajout d'une couche d'**upsampling** après les $1\times1$ → on **reconstruit une image** = une carte de segmentation.

> [!tip] L'astuce du $1\times1$
> Remplacer le fully-connected par une convolution $1\times1$ rend le réseau **entièrement convolutif** : il n'impose plus de taille d'entrée fixe et produit une **carte spatiale** de prédictions au lieu d'un seul score. C'est ce qui rend la segmentation pixel-à-pixel possible.

**Segmentation sémantique entièrement convolutive** : Long et al. (2015) — architecture / entraînement / résultats non détaillés dans le texte.

---

## 2. U-Net (2015)

> [!success] L'architecture de référence (imagerie biomédicale)
> Ronneberger et al. : un **chemin contractant** (capture le **contexte**) et un **chemin expansif symétrique** (permet la **localisation précise**). Entièrement convolutif, **sans couches denses**. Les opérateurs de pooling sont remplacés par des opérateurs d'**upsampling** dans la branche montante.

> [!warning] Le compromis que U-Net résout
> La stratégie naïve « une fenêtre par pixel » échoue :
> - **lente** (réseau lancé séparément par patch, beaucoup de redondance entre patchs voisins) ;
> - **dilemme localisation vs contexte** : grands patchs → plus de pooling → localisation dégradée ; petits patchs → peu de contexte.
>
> U-Net obtient **bonne localisation ET contexte en même temps** grâce à sa structure en U (les *skip connections* relient les niveaux contractant et expansif).

> [!info] Entraînement
> **Data augmentation** par **déformations élastiques** des images. Pour les bords, le contexte manquant est extrapolé par **miroir** de l'image. Pour économiser la mémoire GPU, les auteurs privilégient de **grandes tuiles** d'entrée plutôt qu'un gros batch (batch réduit à **une seule image**).

> [!note] Résultat
> A **gagné le ISBI cell tracking challenge 2015** — d'où le lien avec [[25 - Celltracking by CNN]].

---

> [!quote] À retenir
> Segmenter = classer chaque pixel. On passe de la classification à la segmentation en rendant le réseau **entièrement convolutif** (FC → conv $1\times1$) puis en **upsamplant** pour retrouver la résolution. **U-Net** = chemin contractant (contexte) + chemin expansif (localisation) reliés par des *skip connections*, entraîné avec déformations élastiques — la référence en imagerie biomédicale.

Voir aussi : [[10 - Segmentation]] · [[18 - Pooling layer]] · [[22 - Autoencoder]] · [[25 - Celltracking by CNN]] · [[00 - Index]]
