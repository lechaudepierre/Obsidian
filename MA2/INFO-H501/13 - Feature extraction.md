# 13 — Feature extraction

> [!abstract] Le fil rouge
> Classer, c'est décider à partir de **features** — des mesures extraites des données brutes. Ce chapitre est le catalogue des features classiques, organisé selon *d'où* on les tire : du **pixel** (indices spectraux), de la **texture** (co-occurrence, Gabor, fractales), ou de l'**objet segmenté** (forme via chain code, moments invariants). C'est l'étape que le deep learning rendra automatique — d'où son importance pour comprendre ce que les CNN apprennent.

## 1. Features de pixels

Exemple en télédétection : le **NDVI** (Normalized Difference Vegetation Index) exploite la réponse différente de la végétation dans le visible (VIS) et le proche-infrarouge (NIR) :

$$\text{NDVI} = \frac{\text{NIR} - \text{VIS}}{\text{NIR} + \text{VIS}}$$

> [!tip] Pourquoi un ratio normalisé
> La différence NIR−VIS capte la « signature » de la végétation ; la normalisation par la somme rend l'indice robuste aux variations d'éclairage. Une feature peut donc être une simple combinaison des canaux.

---

## 2. Features de texture

La **texture** = arrangement spatial des valeurs/couleurs des pixels.

### Banc de filtres
Les filtres (ex. **Gabor**, voir [[09 - Filter]]) rehaussent certaines textures → les réponses servent de features.

### Matrice de co-occurrence
Compte combien de fois des **paires de pixels** de valeurs $(i,j)$ apparaissent pour un décalage donné $(\Delta x, \Delta y)$ :

$$C_{\Delta x,\Delta y}(i,j) = \sum_{x,y}\begin{cases}1 & \text{si } I(x,y)=i \text{ et } I(x+\Delta x, y+\Delta y)=j\\ 0 & \text{sinon}\end{cases}$$

À partir de $C$, **Haralick** définit des descripteurs (énergie, contraste, corrélation), ex. :

$$f_1 = \sum_{i,j}\Big(\tfrac{C(i,j)}{R}\Big)^2 \quad(\text{énergie}), \qquad f_3 = \frac{\sum_{i,j}\frac{i\,j\,C(i,j)}{R} - \mu_x\mu_y}{\sigma_x\sigma_y}\quad(\text{corrélation})$$

avec $R$ = nombre de paires (normalisation).

### Analyse fractale
La longueur mesurée dépend de l'**échelle** : à petite échelle, plus de détails → longueur plus grande. La relation suit souvent $\log L_2(X,\lambda_i) = f\,\log\lambda_i$. Le **coefficient de Hurst** (lié à la dimension fractale locale) s'obtient en : calculant max/min locaux sur un voisinage, traçant log(différence) vs log(distance), puis ajustant la droite par moindres carrés.

---

## 3. Features d'objet (après segmentation)

### Chain code (description de contour)
Décrit le contour d'un ensemble connexe comme une **suite ordonnée de pixels** : depuis un pixel de départ, on cherche à chaque pas le pixel-bord suivant ; la prochaine direction de recherche est $\mod_8(n+5)$ à partir de la direction trouvée $n$. On itère jusqu'au retour au point de départ.

> [!tip] Du contour aux features
> Le chain code donne aire et centroïde, puis le contour peut être vu comme un **signal 1D périodique** (distance au centroïde) → une **transformée de Fourier** révèle les régularités (un cercle ≈ composante continue ; un carré ≈ pic à la fréquence de ses oscillations). On en tire des descripteurs de forme :
> $$\text{FormFactor} = \frac{4\pi\,\text{Area}}{\text{Perimeter}^2},\quad \text{Solidity} = \frac{\text{Area}}{\text{ConvexArea}},\quad \text{AspectRatio} = \frac{\text{MaxDiameter}}{\text{MinDiameter}}$$

### Moments invariants (Hu)
Descripteur statistique d'un ensemble de pixels. Moment d'ordre $(p,q)$ et version **centrée** (invariante en translation) :

$$m_{pq} = \sum_{x,y} x^p y^q f(x,y), \qquad \mu_{pq} = \sum_{x,y} (x-\bar x)^p (y-\bar y)^q f(x,y)$$

Normalisation pour l'invariance d'**échelle** : $\eta_{pq} = \dfrac{\mu_{pq}}{\mu_{00}^\gamma}$ avec $\gamma = \tfrac{p+q}{2}+1$.

> [!success] Les 7 invariants de Hu
> Hu combine les $\eta_{pq}$ pour obtenir des invariants **en translation, échelle ET rotation**, ex. :
> $$I_1 = \eta_{20}+\eta_{02}, \qquad I_2 = (\eta_{20}-\eta_{02})^2 + (2\eta_{11})^2$$
> Ces 7 valeurs décrivent une forme indépendamment de sa position/taille/orientation.

> [!warning] Sensibilité au bruit
> Les invariants d'**ordre élevé** sont très sensibles au bruit (exposants élevés dans la somme).

---

> [!quote] À retenir
> Une feature est une mesure discriminante extraite des données. Trois sources : **pixel** (ratios spectraux type NDVI), **texture** (co-occurrence/Haralick, Gabor, fractale/Hurst), **objet** (chain code + Fourier pour la forme, **moments de Hu** invariants en translation/échelle/rotation). C'est précisément ce travail manuel que les couches profondes de la Part II vont apprendre toutes seules.

Voir aussi : [[09 - Filter]] · [[10 - Segmentation]] · [[14 - Classification]] · [[00 - Index]]
