# 18 — Pooling layer

> [!abstract] Le fil rouge
> Pour qu'un CNN abstraie l'information, il faut **réduire la dimension spatiale** des couches au fil de la profondeur. Ce chapitre présente trois manières de « compresser » une couche : le **max-pooling** (le classique), la **convolution à stride** (réduction intégrée à la convolution), et la **couche à trous** (dilatée). Une étude montre d'ailleurs que le pooling explicite n'est pas toujours indispensable.

## 1. Max-pooling

> [!info] Principe
> Le **max-pooling** réduit les dimensions spatiales en ne gardant, sur chaque petite fenêtre (ex. $2\times2$), que la **valeur maximale**. Une fenêtre $2\times2$ divise par 2 la hauteur et la largeur.

> [!tip] Intuition
> Garder le max revient à dire « la feature est-elle présente quelque part dans ce voisinage ? ». Cela rend la représentation plus **compacte** et un peu **invariante aux petits déplacements**.

---

## 2. Convolution à stride (strided convolution)

> [!info] Alternative au pooling
> Au lieu d'une couche de pooling séparée, on utilise une **convolution avec un stride > 1** (voir [[16 - Convolutional layer]]) : la convolution elle-même réduit la taille spatiale en sautant des positions.

---

## 3. Couche à trous (à trous / dilated)

> [!info] Sous-échantillonnage par dilatation
> La couche **à trous** (dilatée) est une autre façon de réduire/élargir la portée : le noyau est « étiré » en insérant des espaces entre ses coefficients, ce qui augmente le champ récepteur sans augmenter le nombre de poids.

---

## 4. Pooling explicite : vraiment nécessaire ?

> [!note] Le résultat de Springenberg et al. (2015)
> Testé sur CIFAR-10 et CIFAR-100 :
> - un réseau **uniquement** à base de convolutions et de sous-échantillonnage égale ou dépasse légèrement l'état de l'art ;
> - inclure du **max-pooling explicite n'améliore pas toujours** les performances d'un CNN ;
> - les auteurs proposent aussi une nouvelle méthode de **visualisation** des représentations des couches hautes.

---

> [!quote] À retenir
> Réduire la dimension spatiale se fait de plusieurs façons : **max-pooling** (max sur une fenêtre, le classique), **convolution à stride** (réduction intégrée à la convolution), **couche à trous** (champ récepteur élargi sans poids supplémentaires). Le pooling explicite n'est pas toujours nécessaire — une pile de convolutions à stride peut suffire.

Voir aussi : [[16 - Convolutional layer]] · [[24 - Segmentation (deep)]] · [[00 - Index]]
