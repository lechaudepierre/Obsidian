# Rapport d'évaluation OCR — PaddleOCR (détection + reconnaissance) sur schémas P&ID

> **Date d'évaluation :** 2026-02-24
> **Échantillon :** 12 images P&ID (échantillonnage stratifié par densité de texte)
> **Zones prédites :** 2 550 | **Zones de référence (GT) :** 2 663

---

## 1. Architecture du pipeline

### 1.1 Approche monolithique

Le pipeline de base repose sur **PaddleOCR** en mode monolithique : un seul framework assure à la fois la détection et la reconnaissance des zones de texte. Les deux modèles s'exécutent séquentiellement sur chaque image rastérisée :

1. **Détection** — `PP-OCRv5_server_det` : localise les boîtes englobantes autour des zones de texte.
2. **Reconnaissance** — `en_PP-OCRv5_mobile_rec` : transcrit le contenu textuel de chaque boîte détectée.

Cette approche a l'avantage de la simplicité (un seul appel API) et permet au détecteur et au reconnaisseur de partager une représentation cohérente de l'image.

### 1.2 Choix de configuration et paramètres

Plusieurs choix de configuration ont été effectués pour adapter PaddleOCR au contexte P&ID :

| Paramètre | Valeur | Justification |
|-----------|--------|---------------|
| `ocr_version` | `PP-OCRv5` | Dernière version du modèle, meilleure précision générale |
| `lang` | `en` | Texte des P&ID en alphabet latin |
| `text_det_thresh` | `0.6` | Seuil de détection — valeur par défaut, bon compromis précision/rappel |
| `text_det_box_thresh` | `0.5` | Seuil de confiance sur les boîtes détectées |
| `use_doc_orientation_classify` | `False` | Désactivé — les P&ID sont toujours orientés correctement |
| `use_doc_unwarping` | `False` | Désactivé — pas de déformation de document (images numériques) |
| `use_textline_orientation` | `False` | Désactivé — pour obtenir les coordonnées brutes sans correction d'orientation |
| `min_confidence` | `0.85` | Seuil de confiance minimum pour filtrer les résultats |
| `MIN_TEXT_LENGTH` | `2` | Filtrage des détections de moins de 2 caractères (bruit) |

### 1.3 Gestion de la rastérisation SVG → PNG

Les schémas P&ID originaux sont au format SVG. La conversion en PNG utilise `cairosvg` avec les paramètres suivants :

| Paramètre | Valeur | Justification |
|-----------|--------|---------------|
| `SVG_DPI` | `150` | Résolution de rastérisation — compromis entre qualité et taille mémoire |
| `max_side` | `6 000 px` | Limite de dimension maximale pour respecter les contraintes internes de PaddleOCR |

La conversion RGBA → RGB se fait sur fond blanc, ce qui correspond au fond naturel des schémas P&ID (lignes noires sur fond blanc).

### 1.4 Gestion du texte vertical (deux passes)

Les schémas P&ID contiennent une proportion significative de texte orienté verticalement (labels de tuyauteries, identifiants d'instruments). PaddleOCR, comme la plupart des moteurs OCR, est optimisé pour le texte horizontal.

Pour capturer le texte vertical, le pipeline exécute **deux passes** :

1. **Passe originale** : OCR sur l'image telle quelle → on ne conserve que les boîtes **horizontales** (largeur > hauteur) et les boîtes quasi-carrées (ratio entre 0,85 et 1,15).
2. **Passe tournée** : l'image est tournée de 90° dans le sens horaire → OCR à nouveau → on ne conserve que les boîtes horizontales (qui correspondent au texte vertical original) → les coordonnées sont remappées dans l'espace de l'image originale.
3. **Fusion** : les résultats des deux passes sont combinés dans un fichier d'annotations unique.

Ce choix garantit que le modèle de reconnaissance ne traite que du texte en orientation horizontale, condition nécessaire pour une transcription fiable.

### 1.5 Construction du ground truth

Le ground truth a été produit par revue manuelle experte dans Label Studio. Pour chaque boîte prédite, l'annotateur a :

- **Accepté** la boîte (en corrigeant éventuellement la transcription),
- **Redimensionné** la boîte pour mieux couvrir la zone de texte,
- **Supprimé** la boîte (détection parasite), ou
- **Dessiné une nouvelle boîte** pour une zone manquée par l'OCR.

Le champ `origin` de Label Studio suit les modifications géométriques :

| `origin` | Signification |
|----------|---------------|
| `prediction` | Géométrie de la boîte inchangée ; le texte peut avoir été corrigé |
| `prediction-changed` | Boîte déplacée ou redimensionnée par l'annotateur |
| `manual` | Boîte dessinée entièrement (zone manquée par l'OCR — FN) |

> **Note importante :** Le champ `origin` ne reflète pas les corrections textuelles. Une boîte avec `origin='prediction'` peut avoir un texte corrigé (ex. `0` → `Ø`). L'évaluation de la reconnaissance compare toujours directement `pred_text` vs `gt_text`.

### 1.6 Stratégie d'appariement

Label Studio conserve les identifiants des boîtes de prédiction dans le fichier d'annotations. L'appariement est **basé sur les ID** — aucun seuil IoU n'est nécessaire :

- **TP** : ID de prédiction présent dans le GT (accepté/corrigé par l'annotateur)
- **FP** : ID de prédiction absent du GT (supprimé par l'annotateur)
- **FN** : Boîte GT avec `origin='manual'` (ajoutée par l'annotateur)

L'IoU est calculé *a posteriori* sur les paires TP pour mesurer la qualité des contours des boîtes.

---

## 2. Détection des zones de texte

### 2.1 Résultats globaux

| Métrique | Valeur |
|----------|--------|
| Précision | **99,5 %** (2 537 TP / 2 550 prédites) |
| Rappel | **95,3 %** (2 537 TP / 2 663 zones GT) |
| F1-Score | **97,3 %** |
| FP (détections parasites) | 13 |
| FN total | 126 (= 37 vrais manques + 89 subsumed) |
| Événements de fusion spatiale | 86 |

PaddleOCR atteint une précision quasi-parfaite : seules 13 zones prédites sur 2 550 sont des faux positifs. Le rappel plus faible (95,3 %) s'explique par 126 zones manquées, mais la lecture est nuancée :

- **89 FN subsumed** — à l'intérieur d'une boîte élargie par fusion spatiale ; le détecteur a bien repéré la zone, mais l'a fusionnée avec une zone adjacente.
- **37 FN vrais** — zones réellement ignorées par le détecteur.

Le rappel corrigé (en ne comptant que les vrais manques) est de **98,6 %**.

### 2.2 Résultats par image

| Image | Catégorie | TP | FP | FN_vrai | FN_sub | SB | Précision | Rappel | F1 | IoU (méd.) |
|-------|-----------|----|----|---------|--------|----|-----------|--------|----|-----------:|
| 102 | medium | 89 | 0 | 0 | 3 | 3 | 1,000 | 0,967 | 0,983 | 1,000 |
| 132 | high | 250 | 0 | 0 | 4 | 4 | 1,000 | 0,984 | 0,992 | 1,000 |
| 168 | high | 185 | 0 | 1 | 2 | 2 | 1,000 | 0,984 | 0,992 | 1,000 |
| 198 | high | 352 | 0 | 0 | 22 | 19 | 1,000 | 0,941 | 0,970 | 1,000 |
| 21 | medium | 50 | 0 | 0 | 2 | 2 | 1,000 | 0,962 | 0,980 | 1,000 |
| 242 | high_error | 416 | 4 | 1 | 11 | 11 | 0,990 | 0,972 | 0,981 | 1,000 |
| 261 | low | 30 | 0 | 0 | 0 | 0 | 1,000 | 1,000 | 1,000 | 1,000 |
| 272 | high_error | 385 | 2 | 21 | 1 | 2 | 0,995 | 0,946 | 0,970 | 1,000 |
| 51 | high | 541 | 3 | 14 | 37 | 36 | 0,994 | 0,914 | 0,952 | 1,000 |
| 79 | low | 47 | 1 | 0 | 3 | 3 | 0,979 | 0,940 | 0,959 | 1,000 |
| 84 | medium | 75 | 3 | 0 | 0 | 0 | 0,962 | 1,000 | 0,980 | 1,000 |
| 94 | medium | 117 | 0 | 0 | 4 | 4 | 1,000 | 0,967 | 0,983 | 1,000 |

*SB = événements de fusion spatiale ; FN_sub = FN subsumed par les boîtes SB.*

### 2.3 Résultats par catégorie de densité

| Catégorie | TP | FP | FN_vrai | FN_sub | SB | Précision | Rappel | F1 |
|-----------|----|----|---------|--------|----|-----------|--------|---:|
| low | 77 | 1 | 0 | 3 | 3 | 0,987 | 0,963 | 0,975 |
| medium | 331 | 3 | 0 | 9 | 9 | 0,991 | 0,974 | 0,982 |
| high | 1 328 | 3 | 15 | 65 | 61 | 0,998 | 0,943 | 0,970 |
| high_error | 801 | 6 | 22 | 12 | 13 | 0,993 | 0,959 | 0,976 |

L'observation principale : la **fusion spatiale** est le mode de défaillance dominant en détection, concentré dans les images à haute densité. Les images *low* et *medium* affichent une détection quasi-parfaite. L'image 51 (high, 592 zones GT) cumule 36 événements SB et 37 FN subsumed — le détecteur couvre 97,6 % du contenu textuel mais fusionne certaines zones adjacentes.

### 2.4 La fusion spatiale : redéfinir le rappel

| Lecture | TP | FN | Rappel |
|---------|----|----|-------:|
| Naïve (toutes FN) | 2 537 | 126 | 95,3 % |
| Corrigée (vrais manques uniquement) | 2 537 | 37 | **98,6 %** |

Le rappel corrigé de **98,6 %** reflète la capacité réelle du détecteur à localiser les zones de texte. La fusion spatiale n'est pas un manque de détection mais une erreur de délimitation des frontières entre zones adjacentes.

---

## 3. Qualité des boîtes englobantes (IoU sur paires TP)

| Statistique | Valeur |
|-------------|--------|
| Paires TP évaluées | 2 537 |
| IoU moyen | 0,983 |
| IoU médian | 1,000 |
| IoU minimum | 0,222 |
| IoU = 1,00 (boîte inchangée) | 2 413 (95,1 %) |

Distribution de l'IoU :

| Intervalle | Nombre | Part |
|------------|-------:|-----:|
| < 0,50 | 30 | 1,2 % |
| 0,50 – 0,75 | 60 | 2,4 % |
| 0,75 – 0,90 | 25 | 1,0 % |
| 0,90 – < 1,00 | 9 | 0,4 % |
| = 1,00 | 2 413 | 95,1 % |

**95,1 %** des boîtes prédites ont été acceptées sans aucune correction géométrique. Les cas avec IoU < 1,0 incluent les 86 événements de fusion spatiale (IoU typique 0,22–0,84) et quelques ajustements mineurs de frontières.

---

## 4. Reconnaissance de texte

### 4.1 Métriques utilisées

- **CER** (Character Error Rate) : distance de Levenshtein / nombre de caractères GT. Métrique primaire pour les labels P&ID (codes alphanumériques courts).
- **WER** (Word Error Rate) : distance de Levenshtein au niveau mot. Moins informative pour les labels mono-token (une seule erreur de caractère → WER = 1,0).
- **Accuracy** : fraction des paires TP avec CER = 0 (correspondance exacte caractère par caractère).

### 4.2 Toutes les paires TP

| Métrique | Valeur |
|----------|--------|
| Paires TP évaluées | 2 537 |
| Correspondances exactes (CER=0) | 2 122 (83,6 %) |
| CER agrégé | 0,054 |
| WER agrégé | 0,161 |

### 4.3 Paires TP propres (fusion spatiale exclue)

Les 86 paires TP de fusion spatiale sont exclues ici car leurs erreurs de reconnaissance sont une conséquence directe de l'erreur de détection (zones fusionnées), et non un défaut du modèle de reconnaissance.

| Métrique | Valeur |
|----------|--------|
| Paires TP propres | 2 451 (= 2 537 − 86 SB) |
| Correspondances exactes (CER=0) | 2 122 (86,6 %) |
| CER agrégé | **0,030** |
| WER agrégé | 0,135 |

Le CER propre de **0,030** est le reflet le plus juste de la qualité du modèle de reconnaissance pris isolément.

### 4.4 Résultats par catégorie de densité (TP propres)

| Catégorie | TP propres | Corrects | Accuracy | CER | WER |
|-----------|-----------|----------|----------|-----|-----|
| low | 74 | 68 | 91,9 % | 0,009 | 0,065 |
| medium | 322 | 290 | 90,1 % | 0,015 | 0,108 |
| high | 1 267 | 1 071 | 84,5 % | 0,033 | 0,155 |
| high_error | 788 | 693 | 87,9 % | 0,035 | 0,122 |

### 4.5 Résultats par image (TP propres)

| Image | Catégorie | TP propres | Accuracy | CER | WER |
|-------|-----------|-----------|----------|-----|-----|
| 21 | medium | 48 | 97,9 % | 0,003 | 0,036 |
| 84 | medium | 75 | 97,3 % | 0,006 | 0,059 |
| 261 | low | 30 | 93,3 % | 0,005 | 0,067 |
| 51 | high | 505 | 93,1 % | 0,029 | 0,064 |
| 79 | low | 44 | 90,9 % | 0,012 | 0,063 |
| 272 | high_error | 383 | 91,6 % | 0,039 | 0,089 |
| 102 | medium | 86 | 87,2 % | 0,018 | 0,123 |
| 242 | high_error | 405 | 84,4 % | 0,031 | 0,158 |
| 94 | medium | 113 | 84,1 % | 0,026 | 0,154 |
| 132 | high | 246 | 81,3 % | 0,034 | 0,218 |
| 198 | high | 333 | 80,2 % | 0,033 | 0,195 |
| 168 | high | 183 | 73,2 % | 0,043 | 0,231 |

La variabilité inter-images est importante (73,2 % à 97,9 %) et n'est pas entièrement expliquée par la densité : l'image 272 (high_error) obtient une meilleure reconnaissance que 168 (high), et l'image 84 (medium) surpasse toutes les autres. Des facteurs spécifiques à chaque schéma (conventions typographiques, taille des polices, qualité de numérisation) influencent la reconnaissance au moins autant que la densité textuelle.

---

## 5. Taxonomie des erreurs

Sur 2 537 paires TP, **415** (16,4 %) ont requis au moins une correction. Les erreurs sont classées en cinq catégories mutuellement exclusives :

| Type d'erreur | Nombre | % des erreurs | % des TP | Description |
|---------------|-------:|:-------------:|:--------:|-------------|
| `slash_o_confusion` | 247 | 59,5 % | 9,7 % | `Ø` lu comme `0` — erreur dominante |
| `spatial_bleeding` | 86 | 20,7 % | 3,4 % | Erreur de détection : fusion de zones adjacentes |
| `other` | 59 | 14,2 % | 2,3 % | Substitutions/insertions/suppressions diverses |
| `duplication` | 15 | 3,6 % | 0,6 % | Label répété dans une même boîte |
| `spurious_chars` | 8 | 1,9 % | 0,3 % | Caractères parasites aux bords de la boîte |
| *correct* | 2 122 | — | 83,6 % | — |

### 5.1 Confusion Ø/0 (247 cas, 59,5 % des erreurs)

Le symbole **Ø** (U+00D8, diamètre nominal en nomenclature P&ID) est systématiquement lu comme le chiffre **0** par PaddleOCR. Cette erreur est :

- **Systématique** : elle se reproduit à l'identique sur chaque occurrence.
- **Spécifique au domaine** : le modèle a été entraîné sur des corpus généralistes où `Ø` est rare.
- **Entièrement corrigeable** en post-traitement : une règle de substitution contextuelle (`0` → `Ø` dans les formats de diamètre nominal `Ø<digits>`) suffirait.

Accuracy estimée après correction Ø/0 : **(2 122 + 247) / 2 451 = 96,7 %** (+10,1 points).

| Prédiction | Vérité terrain |
|------------|----------------|
| `0200` | `Ø200` |
| `025` | `Ø25` |
| `050` | `Ø50` |
| `0100` | `Ø100` |

### 5.2 Fusion spatiale (86 cas, erreur de détection)

Quand PaddleOCR fusionne deux zones adjacentes dans une boîte unique, la transcription contient le texte de plusieurs labels concaténés. Cette erreur est exclue des métriques de reconnaissance propres (section 4.3) car elle relève de la détection.

| Prédiction | Vérité terrain |
|------------|----------------|
| `10LBG10 TI` | `10LBG10` |
| `10LBG10 TIS` | `10LBG10` |
| `10LBG10 \PI` | `10LBG10` |
| `10LCW10 \PI` | `10LCW10` |

### 5.3 Autres erreurs

- **Duplication** (15 cas) : le label est lu deux fois dans la même boîte (`10QKC3010QKC30` → `10QKC30`).
- **Caractères parasites** (8 cas) : 1–2 caractères de ponctuation aux bords (`-+14,00m` → `+14,00m`).
- **Other** (59 cas) : principalement des différences de délimiteurs (espacement autour de `/`, présence de `\`) — erreurs mineures à impact pratique faible.

---

## 6. Calibration de la confiance

Le score de confiance de PaddleOCR est-il prédictif de la qualité de reconnaissance ? Analyse sur les paires TP propres :

| Intervalle de confiance | N | Accuracy (CER=0) | CER moyen |
|------------------------|----:|:-----------------:|----------:|
| 0,85 – 0,90 | 43 | 25,6 % | 0,219 |
| 0,90 – 0,95 | 111 | 59,5 % | 0,100 |
| 0,95 – 0,98 | 157 | 56,7 % | 0,123 |
| **0,98 – 1,00** | **2 114** | **91,5 %** | **0,029** |

Observations :

1. **86,2 %** des prédictions propres ont un score ≥ 0,98, avec une accuracy de 91,5 %. Le modèle est confiant et cette confiance est globalement justifiée.
2. Le score n'est **pas monotone** entre 0,90 et 0,98 : l'intervalle 0,95–0,98 affiche une accuracy inférieure (56,7 %) à 0,90–0,95 (59,5 %). Le score de confiance dans cette plage n'est donc pas un indicateur fiable.
3. **Seuil opérationnel recommandé : 0,98.** En dessous, les 340 prédictions (14 %) ont une accuracy de seulement 53 % et devraient être soumises à vérification humaine.

---

## 7. Synthèse

### Résultats clés

| Dimension | Résultat |
|-----------|----------|
| Précision détection | **99,5 %** — très peu de détections parasites |
| Rappel détection | **95,3 %** (98,6 % corrigé, hors fusion spatiale) |
| FN vrais (manques réels) | **37** sur 2 663 zones GT (1,4 %) |
| Fusion spatiale | **86** événements (mode de défaillance principal en détection) |
| Accuracy reconnaissance (propre) | **86,6 %** correspondance exacte |
| CER (propre) | **0,030** |
| Erreur dominante | **Ø/0** (247 cas, 60 % des erreurs) — corrigeable en post-traitement |
| Accuracy estimée post-correction Ø/0 | **96,7 %** |

### Points forts

- **Détection très fiable** : 99,5 % de précision et 98,6 % de rappel corrigé. Le modèle localise la quasi-totalité des zones de texte.
- **Reconnaissance solide** : CER de 0,030 sur les paires propres. L'erreur dominante (Ø/0) est systématique et corrigeable.
- **Approche deux passes** pour le texte vertical : garantit que le reconnaisseur ne traite que du texte horizontal, condition de fonctionnement optimal.
- **Score de confiance exploitable** : le seuil de 0,98 permet de séparer les prédictions fiables (86 %) de celles à vérifier (14 %).

### Limitations identifiées

- **Fusion spatiale** dans les zones denses : le détecteur tend à regrouper des zones adjacentes en une boîte unique. Ce comportement est concentré dans les images à haute densité textuelle.
- **Pas de gestion du vocabulaire P&ID** : le modèle de reconnaissance, entraîné sur des corpus généralistes, ne connaît pas les symboles spécifiques au domaine (Ø notamment).
- **Variabilité inter-images** : l'accuracy varie de 73,2 % à 97,9 % selon les schémas, indépendamment de la densité textuelle.
- **Limite de résolution** : le paramètre `max_side=6000` impose une réduction d'échelle sur les images les plus grandes, ce qui peut affecter la détection de petits textes.

---

*Rapport généré à partir des résultats de `text_recognition/evaluate_ocr.py` — évaluation du 2026-02-24.*
