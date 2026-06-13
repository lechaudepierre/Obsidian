# 10 — Segmentation

> [!abstract] Le fil rouge
> Segmenter = **séparer l'objet d'intérêt du fond**. Le chapitre est organisé par *ce sur quoi on s'appuie pour décider* : la valeur du pixel (seuillage), les contours (bords), ou des régions cohérentes (watershed). C'est la mise en pratique de l'idée « segmentation = classification de pixels » vue dans [[02 - General Background]], et la version classique de ce que fera [[24 - Segmentation (deep)]].

## 1. Segmentation par pixel (seuillage)

### Seuil fixe
Quand la valeur du pixel a un **sens physique précis**. Ex. CT-scan en unités Hounsfield (HU) :

$$HU = 1000\times\frac{\mu - \mu_{\text{water}}}{\mu_{\text{water}} - \mu_{\text{air}}}$$

Échelle calibrée : air $-1000$, poumon $-700$, graisse $-50$, eau $0$, sang $+30$ à $+45$, os $+1000$. → segmenter un tissu = isoler les pixels (voxels) dans une plage de valeurs.

### Seuil automatique — Otsu
Quand il n'y a pas de valeur définie pour l'objet, il faut **trouver** le seuil.

> [!info] Méthode d'Otsu
> Otsu découpe la distribution des niveaux de gris de sorte que le rapport **variance inter-classe / variance intra-classe soit maximum**. Autrement dit : on cherche le seuil qui sépare au mieux deux populations de pixels (objet / fond).

---

## 2. Segmentation par contours (border based)

Les objets sont souvent caractérisés par des **bords nets**. On utilise la détection de contours (ex. **Sobel**, voir [[09 - Filter]]), souvent après un **filtre médian** (non-linéaire) pour limiter le bruit, puis un seuillage de la carte de gradient.

> [!warning] Limite des contours
> Cette approche **ne garantit pas des contours fermés** : un objet à bords doux peut voir une partie de son contour manquée selon le seuil choisi → objet « ouvert », mal délimité.

---

## 3. Segmentation par régions (watershed)

Un objet n'est pas seulement défini par son intensité ; sa **forme** (ses bords) compte aussi.

> [!info] Watershed (ligne de partage des eaux)
> On interprète l'image (ou une transformée de distance) comme un **relief** : les minima sont des bassins, et on « inonde » depuis des *marqueurs* (ex. maxima locaux de la carte de distance). Les lignes où les bassins se rencontrent forment les **frontières** des régions.

> [!tip] Pipeline typique
> Carte de distance → maxima locaux → étiquetage en marqueurs → watershed sur l'inverse de la distance → contours des objets. Utile pour séparer des objets qui se touchent.

---

> [!quote] À retenir
> Trois familles selon le critère de décision : **pixel** (seuil fixe quand la valeur a un sens, sinon Otsu automatique), **contour** (gradient/Sobel, mais contours non garantis fermés), **région** (watershed à partir de marqueurs). Garde en tête que segmenter, c'est classer chaque pixel — d'où le lien avec la classification et, plus tard, le U-Net.

Voir aussi : [[09 - Filter]] · [[11 - Detection]] · [[13 - Feature extraction]] · [[24 - Segmentation (deep)]] · [[00 - Index]]
