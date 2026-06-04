# Analyse comparative — Détection et reconnaissance de texte sur P&ID

> **Approche A (baseline)** : PaddleOCR monolithique (PP-OCRv5_server_det + en_PP-OCRv5_mobile_rec)
> **Approche B (pipeline)** : YOLOv8m fine-tuné + PP-OCRv5 en reconnaissance seule
> **Corpus d'évaluation** : 12 images P&ID, échantillonnage stratifié par densité de texte
> **Date** : 2026-03-25

---

## 1. Cadre de la comparaison

### 1.1 Objectif

L'objectif est de déterminer si le remplacement du détecteur généraliste de PaddleOCR par un détecteur YOLO fine-tuné sur les P&ID améliore la qualité globale de l'extraction de texte. Les deux approches partagent le même modèle de reconnaissance (PP-OCRv5 mobile rec), ce qui permet d'isoler l'effet du détecteur.

### 1.2 Différences architecturales

| Composant | Approche A (PaddleOCR) | Approche B (YOLO + PP-OCRv5) |
|-----------|----------------------|----------------------------|
| Détection | PP-OCRv5_server_det (généraliste) | YOLOv8m fine-tuné sur P&ID |
| Reconnaissance | en_PP-OCRv5_mobile_rec | en_PP-OCRv5_mobile_rec (identique) |
| Mode d'exécution | Pipeline monolithique (`PaddleOCR.predict()`) | Détection puis reconnaissance séparées (`TextRecognition`) |
| Gestion texte vertical | Deux passes OCR (image originale + tournée 90° CW), filtrage par ratio d'aspect | Deux passes YOLO (image originale + tournée 90° CW), filtrage par ratio d'aspect |
| Résolution d'entrée | Redimensionnement à max 6 000 px | Tuilage SAHI 1 280 × 1 280 sur image pleine résolution (11 821 × 8 187) |
| Appariement d'évaluation | ID-based (même IDs entre prédictions et GT) | IoU-based (seuil 0,50, greedy matching) |
| Entraînement | Aucun (modèle pré-entraîné) | Fine-tuning YOLO sur 258 images P&ID annotées |

### 1.3 Biais méthodologique important

Le GT a été construit à partir des prédictions PaddleOCR : les boîtes GT sont soit des boîtes PaddleOCR acceptées telles quelles, soit des boîtes PaddleOCR ajustées. Cela introduit un **biais favorable au baseline** :

- L'appariement par ID donne un IoU médian de **1,0** au baseline (95,1 % des boîtes GT proviennent directement des prédictions).
- YOLO, qui produit des boîtes indépendantes, est évalué par IoU avec un score médian de **0,81** — non pas parce que ses boîtes sont mauvaises, mais parce qu'elles sont légèrement différentes de celles de PaddleOCR.
- La reconnaissance est pénalisée pour YOLO car les crops ne correspondent pas exactement aux zones GT définies par PaddleOCR.

Ce biais est inhérent à la méthodologie de construction du GT et doit être pris en compte dans l'interprétation des résultats.

---

## 2. Résultats comparatifs

### 2.1 Détection

| Métrique | PaddleOCR (A) | YOLO + PP-OCRv5 (B) | Écart |
|----------|:---:|:---:|:---:|
| **Précision** | **99,5 %** | 93,2 % | −6,3 pts |
| **Rappel** | 95,3 % | **95,1 %** | −0,2 pts |
| **F1** | **97,3 %** | 94,1 % | −3,2 pts |
| TP | 2 537 | 2 532 | −5 |
| FP | **13** | 186 | +173 |
| FN | 126 | **131** | +5 |

**Analyse :**

- Le **rappel est quasi identique** (95,3 % vs 95,1 %) : les deux approches détectent le même volume de texte. YOLO fine-tuné ne rate pas plus de zones que PaddleOCR généraliste.

- La **précision diverge fortement** (99,5 % vs 93,2 %) : YOLO produit 186 FP contre 13 pour PaddleOCR. Ces FP correspondent probablement à des éléments graphiques P&ID (symboles de vannes, connecteurs, lignes de flux) que YOLO interprète comme du texte. Le seuil `det_conf=0,50` pourrait être augmenté pour réduire ces FP, au prix d'un léger recul du rappel.

- Le baseline bénéficie de l'appariement par ID (pas de seuil IoU à satisfaire), tandis que YOLO perd des TP si l'IoU < 0,50 avec la boîte GT. Cela amplifie artificiellement l'écart de précision.

### 2.2 Qualité des boîtes

| Métrique | PaddleOCR (A) | YOLO + PP-OCRv5 (B) |
|----------|:---:|:---:|
| IoU médian | **1,000** | 0,810 |
| IoU moyen | **0,983** | 0,796 |
| Boîtes inchangées (IoU=1) | 95,1 % | — |

L'IoU de 1,0 du baseline reflète le fait que le GT est construit à partir de ses propres prédictions. L'IoU de 0,81 pour YOLO est un score de concordance entre deux détecteurs indépendants, pas une mesure de qualité absolue. Un IoU de 0,81 signifie que YOLO et PaddleOCR détectent les mêmes zones à ~80 % de recouvrement, ce qui est cohérent pour deux modèles aux architectures différentes.

### 2.3 Reconnaissance

| Métrique | PaddleOCR (A) | YOLO + PP-OCRv5 (B) | Écart |
|----------|:---:|:---:|:---:|
| **Accuracy** | **83,6 %** | 77,8 % | −5,8 pts |
| **CER** | 0,054 | **0,049** | −0,005 |
| **WER** | **0,161** | 0,211 | +0,050 |
| Accuracy (clean, baseline) | **86,6 %** | — | — |
| CER (clean, baseline) | **0,030** | — | — |

**Analyse :**

- L'**accuracy exacte** est plus basse pour YOLO (77,8 % vs 83,6 %). Cependant, le **CER est comparable** (0,049 vs 0,054), voire légèrement meilleur. Cela signifie que les erreurs de YOLO sont souvent des différences mineures (un caractère en trop ou en moins à cause du cadrage), pas des erreurs de transcription fondamentales.

- Le **WER plus élevé** pour YOLO (0,211 vs 0,161) s'explique par la sensibilité du WER aux labels mono-token des P&ID : une seule différence de caractère donne WER = 1,0 pour ce label.

- Les métriques « clean » (fusion spatiale exclue) ne sont pas directement comparables car l'évaluation YOLO ne catégorise pas les erreurs de la même manière. Le CER clean du baseline (0,030) reflète la reconnaissance pure de PaddleOCR, sans les erreurs de détection.

- Le modèle de reconnaissance est **strictement identique** (PP-OCRv5 mobile rec). La différence de performance est donc entièrement attribuable à la **qualité des crops** envoyés au reconnaisseur, elle-même liée à la précision des boîtes.

### 2.4 Comparaison par image

| Image | Cat. | F1 (A) | F1 (B) | Acc. (A) | Acc. (B) | CER (A) | CER (B) |
|-------|------|-------:|-------:|---------:|---------:|--------:|--------:|
| 261 | low | 1,000 | 1,000 | 93,3 % | 76,7 % | 0,005 | 0,025 |
| 84 | med | 0,980 | 0,980 | 97,3 % | 94,7 % | 0,006 | 0,006 |
| 21 | med | 0,980 | 0,899 | 94,0 % | 91,8 % | 0,025 | 0,013 |
| 132 | high | 0,992 | 0,956 | 80,0 % | 76,6 % | 0,044 | 0,053 |
| 168 | high | 0,992 | 0,976 | 72,4 % | 64,5 % | 0,052 | 0,076 |
| 198 | high | 0,970 | 0,922 | 75,9 % | 73,1 % | 0,080 | 0,063 |
| 51 | high | 0,952 | 0,909 | 86,9 % | 83,5 % | 0,069 | 0,041 |
| 102 | med | 0,983 | 0,932 | 84,3 % | 71,9 % | 0,034 | 0,050 |
| 94 | med | 0,983 | 0,943 | 81,2 % | 79,3 % | 0,043 | 0,049 |
| 242 | h_err | 0,981 | 0,976 | 82,2 % | 83,1 % | 0,067 | 0,053 |
| 272 | h_err | 0,970 | 0,940 | 91,2 % | 70,9 % | 0,041 | 0,042 |
| 79 | low | 0,959 | 0,920 | 85,1 % | 84,8 % | 0,063 | 0,036 |

**Observations :**

- Le baseline est meilleur en F1 sur toutes les images (grâce à sa précision supérieure).
- En CER, YOLO est parfois meilleur (21, 198, 51, 242, 79) — le détecteur spécialisé produit dans certains cas des crops mieux cadrés que PaddleOCR.
- L'image **84** (medium) donne des résultats quasi identiques pour les deux approches.
- Les images denses (51, 198) voient un meilleur CER avec YOLO, ce qui suggère que YOLO gère mieux la séparation de zones adjacentes (moins de fusion spatiale), mais au prix de plus de FP.

---

## 3. Discussion

### 3.1 YOLO comme détecteur : prometteur mais pas suffisant

Le fine-tuning de YOLO sur les données P&ID lui permet d'atteindre un rappel comparable au détecteur généraliste de PaddleOCR (95,1 % vs 95,3 %). C'est un résultat positif : un modèle entraîné sur 258 images annotées rivalise avec un modèle pré-entraîné sur des millions de documents.

Cependant, la **précision est le point faible** : YOLO produit 14× plus de FP que PaddleOCR. Le détecteur n'a pas suffisamment appris à distinguer les zones de texte des éléments graphiques P&ID (symboles, cotations, lignes de flux). Des pistes d'amélioration :

- Augmenter `det_conf` (actuellement 0,50) pour filtrer les détections peu sûres.
- Enrichir le dataset d'entraînement avec des exemples négatifs (symboles non-texte annotés).
- Utiliser une architecture plus fine (YOLOv8l ou YOLOv9) avec plus de capacité.

### 3.2 L'avantage structurel de PaddleOCR

PaddleOCR combine détection et reconnaissance dans un pipeline optimisé conjointement. Le détecteur produit des boîtes que le reconnaisseur sait exploiter, et vice versa. Cette cohérence interne explique :

- Les boîtes très ajustées (IoU médian = 1,0 avec le GT basé sur ses propres prédictions).
- La faible proportion de FP : le détecteur ne propose que les zones qu'il « comprend » textuellement.
- La stabilité des résultats à travers les différents niveaux de densité.

### 3.3 Biais du GT et équité de la comparaison

Le GT construit depuis PaddleOCR favorise structurellement le baseline :

- L'appariement par ID (baseline) vs par IoU (YOLO) n'est pas symétrique.
- Les frontières de boîtes GT sont celles de PaddleOCR ; toute autre méthode sera pénalisée sur l'IoU.
- Pour une comparaison parfaitement équitable, il faudrait un GT annoté indépendamment des deux méthodes (boîtes dessinées from scratch), ce qui représente un effort d'annotation considérablement plus important.

Malgré ce biais, le fait que YOLO atteigne un CER comparable (et parfois meilleur) sur certaines images montre que le potentiel du détecteur spécialisé existe.

### 3.4 Complexité opérationnelle

| Critère | PaddleOCR (A) | YOLO + PP-OCRv5 (B) |
|---------|:---:|:---:|
| Installation | Un seul package (`paddleocr`) | Deux frameworks (PyTorch + PaddlePaddle) |
| Environnement | Unique | Conda séparé (GPU PyTorch + CPU PaddlePaddle) |
| Temps d'inférence | ~1 passe par image | ~2 passes YOLO + reconnaissance par crop |
| Entraînement requis | Non | Fine-tuning YOLO (258 images annotées) |
| Maintenance | Minimale | Dataset d'entraînement à maintenir |

---

## 4. Conclusion et recommandation

### 4.1 Approche retenue : PaddleOCR (baseline)

Pour la tâche d'extraction de texte sur les schémas P&ID, **l'approche monolithique PaddleOCR est supérieure** à l'approche séparée YOLO + PP-OCRv5 sur les métriques principales :

| Métrique | PaddleOCR | YOLO + PP-OCRv5 | Gagnant |
|----------|:---------:|:---------------:|:-------:|
| Précision détection | **99,5 %** | 93,2 % | PaddleOCR |
| Rappel détection | **95,3 %** | 95,1 % | ~ égal |
| F1 détection | **97,3 %** | 94,1 % | PaddleOCR |
| Accuracy reconnaissance | **83,6 %** | 77,8 % | PaddleOCR |
| CER | 0,054 | **0,049** | ~ comparable |
| CER clean (SB exclue) | **0,030** | — | PaddleOCR |
| Complexité | **Simple** | Complexe | PaddleOCR |

### 4.2 Apport de l'étude comparative

Malgré la supériorité du baseline, l'étude comparative apporte plusieurs enseignements pour la thèse :

1. **Un détecteur spécialisé ne garantit pas de meilleurs résultats** quand le détecteur généraliste est déjà bien calibré. PaddleOCR bénéficie d'un entraînement massif sur des millions de documents qui compense l'absence de fine-tuning spécifique.

2. **La cohérence interne d'un pipeline monolithique est un avantage** : le détecteur et le reconnaisseur sont optimisés conjointement, ce qui minimise les erreurs d'interface (crops mal cadrés, re-détection parasite).

3. **Le texte vertical est gérable par les deux approches** via la stratégie deux passes. Ce n'est pas un différenciateur.

4. **La fusion spatiale reste le problème principal** dans les deux cas, mais PaddleOCR le gère mieux grâce à son NMS interne optimisé pour le texte.

5. **Le CER comparable de YOLO** montre que le potentiel existe pour un détecteur spécialisé, à condition de réduire les FP et d'améliorer la qualité des frontières de boîtes.

### 4.3 Pistes d'amélioration identifiées

Pour le pipeline PaddleOCR retenu :

- **Post-traitement Ø/0** : une règle de substitution contextuelle éliminerait 60 % des erreurs de reconnaissance, portant l'accuracy estimée à **96,7 %**.
- **Seuil de confiance à 0,98** : permet de séparer les prédictions fiables (86 %) de celles nécessitant une vérification humaine (14 %).

Pour un éventuel usage futur du pipeline séparé :

- Augmenter `det_conf` au-delà de 0,50 pour réduire les FP.
- Entraîner YOLO avec des exemples négatifs (symboles P&ID non-texte).
- Construire un GT indépendant pour une comparaison non biaisée.

---

*Analyse comparative rédigée sur base des évaluations du 2026-02-24 (baseline) et 2026-03-25 (pipeline YOLO) — 12 images P&ID, nouveau ground truth corrigé.*
