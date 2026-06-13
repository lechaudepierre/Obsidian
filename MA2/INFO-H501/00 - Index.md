# 00 — Index · INFO-H501 Pattern Recognition & Image Analysis

> [!abstract] Carte du cours
> Le cours raconte **une seule histoire** : comment décider (classer, segmenter, détecter, suivre) à partir d'images. Il va du **classique** — où l'humain conçoit les features et les règles — vers le **deep learning** — où le réseau apprend tout lui-même. La **Part 0** pose les fondements de la décision (Bayes, arbres) ; la **Part I** traite l'image à la main (filtres, features) ; la **Part II** démonte le réseau de neurones brique par brique ; la **Part III** assemble ces briques en applications réelles. Garde ce fil en tête : *features faites main → features apprises*.

> [!tip] Comment utiliser ces fiches dans Obsidian
> Place ce dossier dans ton vault. Chaque fiche est reliée aux autres par des `[[wikilinks]]` — ouvre la **vue graphe** d'Obsidian pour voir les connexions entre chapitres. Les `> [!callout]` colorés font ressortir définitions (info), intuitions (tip), pièges (warning) et points-clés (quote/success).

---

## Part 0 — Fondements du Machine Learning classique

La théorie de la décision : qu'est-ce qu'apprendre, et deux grandes familles de classifieurs.

- [[02 - General Background]] — vocabulaire, généralisation, sur/sous-apprentissage, le triangle complexité/données/erreur.
- [[03 - Bayesian approach]] — classifieur optimal (MAP), régression logistique, LDA, méthodes non-paramétriques.
- [[04 - Non-Bayesian approaches]] — k plus proches voisins, intro aux arbres.
- [[05 - Decision tree]] — impureté (entropie/Gini), gain, élagage, instabilité, forêts (IA explicable).

---

## Part I — Analyse d'image classique

Le pipeline « fait main » : transformer → segmenter → détecter → suivre → décrire → classer.

- [[09 - Filter]] — image numérique, convolution, passe-bas/passe-haut (Sobel), Gabor.
- [[10 - Segmentation]] — seuillage (Otsu), contours, régions (watershed).
- [[11 - Detection]] — pattern matching, coins de Harris, transformée de Hough, Viola-Jones.
- [[12 - Object tracking]] — mean-shift (top-down), flux optique / Lucas-Kanade (bottom-up).
- [[13 - Feature extraction]] — pixel (NDVI), texture (co-occurrence, Gabor, fractale), forme (chain code, moments de Hu).
- [[14 - Classification]] — eigenfaces/PCA, distance de Hu, Bag of visual Words.

---

## Part II — Changement de paradigme : le deep learning

Le réseau de neurones, brique par brique.

- [[15 - Neural networks]] — couche dense, activation, softmax, cross-entropy, SGD.
- [[16 - Convolutional layer]] — convolution apprise, champ récepteur, stride/padding, abstraction.
- [[17 - Activation functions]] — saturation des sigmoïdes, ReLU.
- [[18 - Pooling layer]] — max-pooling, convolution à stride, couche à trous.
- [[20 - Optimization]] — minima locaux, vanishing gradient, dropout, batch norm, init (Xavier/He), Adam.

---

## Part III — Applications

Les briques de la Part II assemblées pour des tâches réelles.

- [[21 - Classification (CNN)]] — LeNet → AlexNet → VGG → ResNet → MobileNet.
- [[22 - Autoencoder]] — compression non supervisée, espace latent, débruitage, VAE.
- [[23 - Object Detection]] — R-CNN (régions + CNN + SVM + régression de bbox), lignée Fast/Faster/YOLO.
- [[24 - Segmentation (deep)]] — du FC à la conv $1\times1$ + upsampling, U-Net.
- [[25 - Celltracking by CNN]] — data augmentation, détection par régression.

---

> [!note] Chapitres écartés (vides ou non rédigés dans le livre)
> Conformément au contenu réellement disponible, n'ont **pas** de fiche : *1 Women of science* (historique, non technique), *6 Neural networks / 7 Performance evaluation / 8 Bias* (pages vides en Part 0), *19 Up/Down-sampling layer* (≈ 1 ligne), *26 Super-resolution by CNN* (≈ 1 ligne), ainsi que les sous-sections marquées « to be developed » (GoogLeNet, ResNeXt, Xception, DenseNet, EfficientNet…) et *27 Discussion*.

> [!quote] Le message à emporter
> Toute la progression du cours = **déplacer l'intelligence de l'ingénieur vers les données**. En Part I, on code les filtres (Sobel, Gabor) et les descripteurs (Hu, co-occurrence) ; en Part II/III, le réseau **apprend** ces mêmes filtres (convolutions) et descripteurs (couches profondes). Comprendre le classique, c'est comprendre ce que le deep learning automatise.
