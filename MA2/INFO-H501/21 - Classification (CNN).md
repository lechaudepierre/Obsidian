# 21 — Classification (architectures CNN)

> [!abstract] Le fil rouge
> Ce chapitre est une **histoire de l'architecture des CNN de classification**, racontée par une question récurrente : *comment aller plus profond sans casser l'apprentissage ?* De **LeNet** (la preuve de concept) à **AlexNet** (le coup d'éclat ImageNet), **VGG** (la profondeur par empilement de petits $3\times3$), **ResNet** (les connexions résiduelles qui débloquent les très grands réseaux) et **MobileNet** (la même puissance, mais légère pour l'embarqué). Chaque architecture répond à une limite de la précédente.

## 1. LeNet (1998)

> [!info] La preuve de concept
> Yann LeCun et al. : réseau de classification d'images. **Reconnaissance d'écriture** sur petites images $32\times32$ en niveaux de gris. ~**60k** paramètres entraînables, **20 epochs**. Pose l'architecture canonique : convolutions + pooling + couches denses.

---

## 2. AlexNet (2012)

> [!success] Le tournant historique
> Premier grand succès de la classification par CNN (« ImageNet Classification with Deep CNN », Krizhevsky et al.). Entraîné sur **1,2 million** d'images ImageNet, **1000 classes**. Erreurs top-1 / top-5 de **37,5 %** et **17,0 %**.

Architecture : **60 millions** de paramètres, 650 000 neurones, **8 couches apprises** (5 convolutives + 3 fully-connected).
- Activation **ReLU** (choisie pour accélérer l'entraînement).
- **Dropout** pour limiter l'overfitting.
- Graphe **scindé sur 2 GPU** (deux branches quasi séparées).
- Dataset : ImageNet (~15M images, ~22 000 catégories), redimensionné à $256\times256$ RGB centré.

---

## 3. VGG (2015)

> [!info] La profondeur par les petits noyaux
> Simonyan & Zisserman étendent l'idée d'AlexNet en **ajoutant des couches** : « pousser la profondeur à **16–19 couches** de poids » apporte une amélioration significative. Entrée fixe $224\times224$ RGB (moyenne soustraite), stride convolutif = 1, max-pooling $2\times2$. Sortie : 3 couches FC (4096, 4096, 1000 classes), ReLU, dropout 0,5.

> [!tip] L'astuce : empiler des $3\times3$ plutôt qu'un gros noyau
> Au lieu d'un grand champ récepteur en une couche (ex. $11\times11$ stride 4 d'AlexNet), VGG **empile des $3\times3$** sans pooling entre eux. Gains :
> - **plus de non-linéarités** (3 ReLU au lieu d'1) → fonction de décision plus discriminante ;
> - **moins de paramètres**.
>
> Résultat : 1ʳᵉ/2ᵉ places en localisation/classification à ImageNet 2014. La profondeur **aide** la précision.

---

## 4. ResNet (2015)

> [!warning] Le problème : la dégradation
> Plus on empile de couches, plus c'est **dur à entraîner** (vanishing/exploding gradient — cf. [[20 - Optimization]]). Même après normalisation, on observe une **dégradation** : la précision **sature** puis chute. Ce **n'est pas** de l'overfitting (testé en augmentant les données).

> [!success] La solution : l'unité résiduelle
> He et al. introduisent le **residual unit** : on ajoute une **copie de l'entrée** (identité) plus loin dans le réseau via une **« shortcut connection »**. La couche apprend alors une **fonction résiduelle** par rapport à son entrée :
> $$\mathcal{F}(\mathbf{x}) + \mathbf{x}$$

> [!tip] Pourquoi ça marche
> Il est plus facile d'apprendre une **petite correction** $\mathcal{F}(\mathbf{x})$ (le résidu) que la transformation complète. Si une couche n'a rien à apporter, elle peut apprendre $\mathcal{F}\approx 0$ et laisser passer l'identité — le gradient circule directement via le shortcut.

Résultats : jusqu'à **152 couches** (8× VGG) avec une complexité **moindre** ; **1ʳᵉ place ILSVRC 2015** (3,57 % top-5).

---

## 5. MobileNet v1 (2017)

> [!info] Le problème : la latence sur l'embarqué
> Pour le temps réel sur appareils mobiles/embarqués (voitures autonomes…), la **vitesse** compte. Howard et al. proposent une décomposition optimisant taille et latence.

> [!success] Convolutions séparables en profondeur (depthwise separable)
> On **factorise** une convolution standard (qui filtre ET combine en une étape) en deux couches :
> - **depthwise** : un filtre par canal d'entrée (filtrage seul) ;
> - **pointwise** : convolution $1\times1$ qui **combine** les sorties.
>
> Analogie : factoriser un noyau $3\times3$ en $3\times1$ puis $1\times3$ (ex. le Sobel se décompose ainsi — mais **tous les noyaux ne sont pas factorisables**).

> [!note] Gain
> Réduit **drastiquement** le calcul et la taille du modèle : les convolutions séparables $3\times3$ utilisent **8 à 9× moins** de calcul que les standards, pour une **petite** perte de précision. Utilisé pour détection, classification fine, attributs de visage, géolocalisation. Toutes les couches : batchnorm + ReLU.

> [!example] Autres architectures mentionnées
> Le chapitre liste aussi GoogLeNet/Inception, ResNeXt, Xception, DenseNet, MobileNet v2, EfficientNet, RegNet, ConvMixer, ConvNeXt — mais **non développées** dans le cours (« to be developed (or not…) »).

---

> [!quote] À retenir
> L'évolution des CNN de classification suit une quête de profondeur **utilisable** : **LeNet** (preuve de concept) → **AlexNet** (ReLU + dropout + 2 GPU, choc ImageNet) → **VGG** (empiler des $3\times3$ : plus de non-linéarité, moins de poids) → **ResNet** (connexions résiduelles $\mathcal F(\mathbf x)+\mathbf x$ contre la dégradation, jusqu'à 152 couches) → **MobileNet** (convolutions séparables pour l'embarqué). Chaque saut résout une limite précise du précédent.

Voir aussi : [[16 - Convolutional layer]] · [[20 - Optimization]] · [[23 - Object Detection]] · [[00 - Index]]
