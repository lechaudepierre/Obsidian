# Rapport d'évaluation — Pipeline YOLOv8m + PP-OCRv5 sur schémas P&ID

> **Date d'évaluation :** 2026-03-25
> **Échantillon :** 12 images P&ID (échantillonnage stratifié par densité de texte)
> **Zones prédites :** 2 718 | **Zones de référence (GT) :** 2 663
> **Seuil IoU d'appariement :** 0,50

---

## 1. Architecture du pipeline

### 1.1 Approche séparée (deux modèles)

Contrairement à l'approche monolithique PaddleOCR (baseline), ce pipeline découple la détection et la reconnaissance en deux modèles spécialisés :

1. **Détection** — YOLOv8m fine-tuné sur les P&ID, avec inférence SAHI tuilée.
2. **Reconnaissance** — PP-OCRv5 (`en_PP-OCRv5_mobile_rec`) en mode reconnaissance seule (`TextRecognition`), sans re-détection interne.

L'hypothèse est qu'un détecteur spécialisé (entraîné sur les données P&ID) peut mieux localiser les zones de texte qu'un détecteur généraliste, tout en réutilisant le même modèle de reconnaissance pour une comparaison équitable.

### 1.2 Inférence SAHI tuilée

Les images P&ID (11 821 × 8 187 px) dépassent largement la résolution d'entrée de YOLO (1 280 × 1 280). Le pipeline utilise l'inférence SAHI (Sliced Adaptive Hyper Inference) :

1. L'image est découpée en tuiles de **1 280 × 1 280** avec un chevauchement de **200 px**.
2. YOLO est exécuté sur chaque tuile.
3. Les coordonnées des détections sont remappées dans l'espace de l'image complète.
4. Un NMS (Non-Maximum Suppression) élimine les doublons aux frontières des tuiles.

| Paramètre | Valeur |
|-----------|--------|
| `tile_size` | 1 280 px |
| `tile_overlap` | 200 px |
| `nms_iou` | 0,50 |
| `det_conf` | 0,50 |
| `det_imgsz` | 1 280 |
| `padding` | 5 px |

### 1.3 Gestion du texte vertical (deux passes de détection)

Les P&ID contiennent une proportion significative de texte vertical. YOLO produit des boîtes axis-aligned, et le reconnaisseur PP-OCRv5 attend du texte horizontal. Le pipeline exécute **deux passes de détection** :

- **Passe 1** : YOLO sur l'image originale → filtre les boîtes **horizontales** (largeur ≥ hauteur) → reconnaissance.
- **Passe 2** : YOLO sur l'image tournée de **90° CW** → filtre les boîtes horizontales (correspondant au texte vertical dans l'image originale) → reconnaissance → remapping des coordonnées vers l'espace original.
- **Fusion NMS** : les résultats des deux passes sont fusionnés et dédupliqués.

Ce mécanisme garantit que le reconnaisseur ne traite que du texte orienté horizontalement, indépendamment de l'orientation originale.

### 1.4 Reconnaissance seule (TextRecognition)

Un point technique important : le pipeline utilise `paddleocr.TextRecognition` (modèle de reconnaissance isolé) au lieu de `PaddleOCR` (pipeline complet det+rec). Cela évite que PaddleOCR ne relance sa propre détection sur chaque crop déjà découpé par YOLO, ce qui produisait des résultats dégradés lors de tests préliminaires.

### 1.5 Stratégie d'appariement (IoU-based)

Contrairement au baseline PaddleOCR (appariement par ID), le pipeline YOLO produit ses propres boîtes indépendamment du GT. L'appariement se fait par **IoU greedy** :

- Pour chaque prédiction, on cherche la boîte GT avec le meilleur IoU.
- Si IoU ≥ 0,50, la paire est un **TP**.
- Les prédictions non appariées sont des **FP**.
- Les boîtes GT non appariées sont des **FN**.

Cette stratégie est plus stricte que l'appariement par ID du baseline et pénalise les boîtes dont les frontières ne correspondent pas exactement au GT.

---

## 2. Détection des zones de texte

### 2.1 Résultats globaux

| Métrique | Valeur |
|----------|--------|
| Précision | **93,2 %** (2 532 TP / 2 718 prédites) |
| Rappel | **95,1 %** (2 532 TP / 2 663 zones GT) |
| F1-Score | **94,1 %** |
| TP | 2 532 |
| FP | 186 |
| FN | 131 |

### 2.2 Résultats par image

| Image | TP | FP | FN | Précision | Rappel | F1 |
|-------|---:|---:|---:|----------:|-------:|---:|
| 261 | 30 | 0 | 0 | 1,000 | 1,000 | 1,000 |
| 84 | 75 | 3 | 0 | 0,962 | 1,000 | 0,980 |
| 242 | 426 | 19 | 2 | 0,957 | 0,995 | 0,976 |
| 168 | 186 | 7 | 2 | 0,964 | 0,989 | 0,976 |
| 132 | 248 | 17 | 6 | 0,936 | 0,976 | 0,956 |
| 102 | 89 | 10 | 3 | 0,899 | 0,967 | 0,932 |
| 94 | 116 | 9 | 5 | 0,928 | 0,959 | 0,943 |
| 272 | 389 | 32 | 18 | 0,924 | 0,956 | 0,940 |
| 21 | 49 | 8 | 3 | 0,860 | 0,942 | 0,899 |
| 198 | 349 | 34 | 25 | 0,911 | 0,933 | 0,922 |
| 79 | 46 | 4 | 4 | 0,920 | 0,920 | 0,920 |
| 51 | 529 | 43 | 63 | 0,925 | 0,894 | 0,909 |

L'image **51** (la plus dense, 592 zones GT) est la plus difficile avec 63 FN et 43 FP. L'image **261** (la moins dense) est détectée parfaitement.

---

## 3. Qualité des boîtes englobantes

| Statistique | Valeur |
|-------------|--------|
| IoU médian | **0,810** |
| IoU moyen | 0,796 |

L'IoU médian de 0,81 indique que les boîtes YOLO ne recouvrent pas exactement les mêmes zones que le GT. Cela est attendu car :

1. Le GT a été construit à partir des prédictions PaddleOCR (pas YOLO), donc les frontières de boîtes sont naturellement différentes.
2. YOLO tend à produire des boîtes légèrement plus larges ou plus étroites que PaddleOCR.
3. Le mécanisme deux passes + remapping introduit de légères imprécisions sur les coordonnées des boîtes verticales remappées.

---

## 4. Reconnaissance de texte

### 4.1 Résultats globaux

| Métrique | Valeur |
|----------|--------|
| Paires TP évaluées | 2 532 |
| Correspondances exactes (CER=0) | 1 971 (77,8 %) |
| CER agrégé | **0,049** |
| WER agrégé | 0,211 |

### 4.2 Résultats par image

| Image | TP | Corrects | Accuracy | CER | WER | IoU (méd.) |
|-------|---:|--------:|---------:|----:|----:|-----------:|
| 84 | 75 | 71 | 94,7 % | 0,006 | 0,047 | 0,828 |
| 21 | 49 | 45 | 91,8 % | 0,013 | 0,071 | 0,830 |
| 79 | 46 | 39 | 84,8 % | 0,036 | 0,136 | 0,810 |
| 51 | 529 | 442 | 83,5 % | 0,041 | 0,159 | 0,810 |
| 242 | 426 | 354 | 83,1 % | 0,053 | 0,167 | 0,834 |
| 94 | 116 | 92 | 79,3 % | 0,049 | 0,195 | 0,807 |
| 261 | 30 | 23 | 76,7 % | 0,025 | 0,150 | 0,838 |
| 132 | 248 | 190 | 76,6 % | 0,053 | 0,213 | 0,815 |
| 198 | 349 | 255 | 73,1 % | 0,063 | 0,263 | 0,807 |
| 102 | 89 | 64 | 71,9 % | 0,050 | 0,250 | 0,817 |
| 272 | 389 | 276 | 70,9 % | 0,042 | 0,289 | 0,757 |
| 168 | 186 | 120 | 64,5 % | 0,076 | 0,321 | 0,805 |

La variabilité inter-images est importante (64,5 % à 94,7 %). L'image **168** est la plus difficile en reconnaissance, comme pour le baseline PaddleOCR.

### 4.3 Impact de la qualité des boîtes sur la reconnaissance

L'IoU médian de ~0,81 signifie que les crops de texte envoyés au reconnaisseur ne couvrent pas exactement la zone de texte définie dans le GT. Cela peut :

- Tronquer une partie du texte (si la boîte est trop petite)
- Inclure du bruit graphique adjacent (si la boîte est trop large)

Ces deux phénomènes dégradent la reconnaissance, indépendamment de la qualité du modèle PP-OCRv5.

---

## 5. Synthèse

### Résultats clés

| Dimension | Résultat |
|-----------|----------|
| Précision détection | **93,2 %** |
| Rappel détection | **95,1 %** |
| F1 détection | **94,1 %** |
| FP | 186 (détections parasites) |
| FN | 131 (zones manquées) |
| Accuracy reconnaissance | **77,8 %** |
| CER | **0,049** |
| IoU médian (boîtes) | **0,810** |

### Points forts

- **Rappel comparable au baseline** (95,1 % vs 95,3 %) — YOLO détecte la quasi-totalité des zones de texte.
- **Reconnaissance du texte vertical** : l'approche deux passes résout le problème du texte vertical que PaddleOCR gérait déjà en interne.
- **Recognition-only mode** : `TextRecognition` évite la re-détection parasite sur les crops.
- **Détecteur spécialisé** : YOLO fine-tuné sur les P&ID, potentiellement adaptable à d'autres types de documents techniques.

### Limitations

- **Plus de faux positifs** (186 vs 13) : YOLO détecte des éléments graphiques (symboles, lignes) comme du texte.
- **IoU plus faible** (~0,81 vs ~1,0) : les frontières des boîtes YOLO ne correspondent pas exactement à celles du GT construit depuis PaddleOCR, ce qui pénalise aussi la reconnaissance.
- **Accuracy plus basse** (77,8 % vs 83,6 %) : liée à la qualité des boîtes (IoU) et aux FP.
- **Pipeline plus complexe** : deux passes de détection, tuilage SAHI, environnement conda séparé.

---

*Rapport généré à partir de `text_detection_recognition/evaluate.py` — évaluation du 2026-03-25.*
