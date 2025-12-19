# Réponses aux Questions des Diapositives (ELEC-H-473 Th07)

Voici les réponses aux questions identifiées dans les diapositives du document `ELECH473_Th07.pdf`.

## Diapositive 7 : Image digitale et C

- **Q : Why do we need to know image width & height?** (Pourquoi avons-nous besoin de connaître la largeur et la hauteur de l'image ?)
    
    - **R :** Pour un fichier image RAW qui ne contient que les données brutes des pixels sans en-tête, connaître la largeur (Width) et la hauteur (Height) est essentiel pour reconstruire correctement l'image 2D à partir du flux de données 1D lu depuis le fichier. Sans ces dimensions, on ne sait pas où une ligne de pixels se termine et où la suivante commence. La largeur est nécessaire pour calculer l'offset mémoire correspondant à un pixel (x,y) comme y×Width+x. La hauteur (avec la largeur) détermine la taille totale de l'image (W×H) et donc la quantité de données à lire ou à allouer en mémoire.
        

## Diapositive 12 : Ajout d'une valeur de décalage (offset)

- **Q : Can you guess what?** (Pouvez-vous deviner quoi ? [Problème potentiel lors de l'ajout d'un offset])
    
    - **R :** Le problème potentiel est le **dépassement de capacité (overflow)** ou la **saturation**. Les pixels sont souvent stockés comme des `unsigned char` (0-255). Si on ajoute un offset à une valeur de pixel déjà élevée, le résultat peut dépasser 255. Par exemple, 200+60=260, ce qui ne peut pas être stocké dans un `unsigned char`. Il faut donc gérer ce cas, soit en laissant la valeur "boucler" (wrap around, par ex. 260 deviendrait 4 en modulo 256), soit, plus couramment en traitement d'image, en **saturant** la valeur au maximum (255 dans ce cas). De même, si on soustrait un offset, le résultat pourrait devenir négatif, et il faudrait saturer à 0.
        

## Diapositive 13 : Ajout d'une valeur de décalage en C

- **Q : why do I use int here?** (Pourquoi est-ce que j'utilise int ici ? [pour `tmp`])
    
    - **R :** La variable temporaire `tmp` est déclarée comme `int` pour pouvoir stocker le résultat intermédiaire de l'addition `src[i] + offset` sans risque de dépassement immédiat. Comme `src[i]` et `offset` sont des `unsigned char` (0-255), leur somme peut atteindre 255+255=510. Un `unsigned char` ne pourrait pas stocker cette valeur, mais un `int` (généralement 32 bits signés, allant jusqu'à +2 milliards environ) le peut sans problème. Cela permet de vérifier ensuite si le résultat (`tmp`) dépasse 255 avant de l'assigner (ou la valeur saturée 255) à `src[i]` (qui est `unsigned char`).
        
- **Q : use of int, why?** (utilisation de int, pourquoi ? [pour le cast])
    
    - **R :** Le cast explicite `(int)` appliqué à `src[i]` et `offset` avant l'addition (ligne 14: `tmp=(int)(src[i])+(int)(offset);`) force le compilateur à traiter ces valeurs comme des entiers avant de les additionner. Bien que le compilateur puisse effectuer une promotion implicite vers `int` dans une expression arithmétique, le cast explicite garantit que l'addition elle-même est effectuée en utilisant l'arithmétique des entiers (plus larges), évitant ainsi tout risque de dépassement au niveau `unsigned char` _pendant_ l'addition, avant même que le résultat ne soit stocké dans `tmp`. C'est une précaution pour assurer que le calcul intermédiaire se fait avec une précision suffisante.
        

## Diapositive 15 : Ajout d'une valeur de décalage en SIMD

- **Q : look for saturated addition inst; see next slide (option);** (cherchez l'instruction d'addition saturée ; voir diapo suivante (option) ;)
    
    - **R :** Cette note suggère qu'au lieu d'utiliser `paddb` (addition simple d'octets, qui peut boucler en cas de dépassement) et de gérer la saturation manuellement (ce qui n'est pas montré dans ce code assembleur), on pourrait utiliser une instruction SIMD qui effectue directement une **addition saturée**. La diapositive suivante (16) présente `paddusb` (Packed Add Unsigned Bytes with Saturation). Utiliser `paddusb xmm0, xmm1;` à la place de `paddb xmm0, xmm1;` réaliserait l'addition et la saturation en une seule instruction, ce qui est plus efficace.
        

## Diapositive 17 : Ajout de deux images : C

- **Q : why do I use int here?** (Pourquoi est-ce que j'utilise int ici ? [pour `tmp`])
    
    - **R :** Pour la même raison que dans la diapositive 13. L'addition de deux valeurs de pixels (`src1[i] + src2[i]`), chacune pouvant aller jusqu'à 255, peut donner un résultat allant jusqu'à 510. Utiliser une variable intermédiaire `tmp` de type `int` permet de stocker ce résultat sans dépassement, afin de pouvoir ensuite vérifier s'il faut saturer à 255 avant de stocker le résultat final dans `dst[i]` (qui est `unsigned char`).
        

## Diapositive 18 : Ajout de deux images en SIMD

- **Q : Why?** (Pourquoi ? [calcul de la longueur de boucle `ii=(sizex)*(sizey)/16`])
    
    - **R :** Les instructions SIMD utilisées ici (`movdqu`, `paddb`) opèrent sur des registres XMM de 128 bits. Comme les données d'image sont des `unsigned char` (1 octet = 8 bits), chaque opération SIMD traite 128/8=16 pixels (octets) à la fois. La boucle assembleur doit donc itérer sur des blocs de 16 pixels. Si l'image a une taille totale de `sizex * sizey` pixels, le nombre d'itérations nécessaires pour traiter toute l'image par blocs de 16 est `(sizex * sizey) / 16`. La variable `ii` stocke ce nombre d'itérations.
        
- **Q : Can you explain?** (Pouvez-vous expliquer ? [instruction `emms`])
    
    - **R :** L'instruction `emms` (Empty MMX Technology State) était historiquement nécessaire après avoir utilisé des instructions MMX pour nettoyer l'état des registres MMX, car ceux-ci étaient aliasés avec les registres de l'unité de calcul en virgule flottante (FPU). Ne pas le faire pouvait causer des problèmes lors de l'utilisation ultérieure de la FPU. Cependant, les instructions utilisées ici (`movdqu`, `paddb`) sont des instructions SSE (Streaming SIMD Extensions), qui utilisent des registres dédiés (XMM) distincts de la FPU et de MMX. Sur les CPU modernes supportant SSE, l'instruction `emms` n'est plus nécessaire après l'utilisation d'instructions SSE et n'a généralement aucun effet. Elle est laissée ici peut-être par habitude ou pour une compatibilité avec du code très ancien, mais elle est superflue pour ce code SSE.
        

## Diapositive 22 : Fonction de seuillage en C

- **Q : why do we use int here?** (Pourquoi est-ce que j'utilise int ici ? [pour `offset`])
    
    - **R :** Il semble y avoir une erreur de copier-coller dans cette diapositive. Le code implémente une fonction de **seuillage** (thresholding), pas l'ajout d'un offset. La variable à la ligne 4 devrait logiquement s'appeler `threshold` (seuil) et non `offset`. En supposant qu'elle représente le seuil, elle est déclarée `unsigned char` (ligne 4), ce qui est approprié car le seuil est comparé à des valeurs de pixels qui sont aussi des `unsigned char`. L'utilisation de `int` pour `i` (ligne 2) est standard pour un compteur de boucle. Il n'y a pas d'utilisation problématique de `int` dans ce fragment de code spécifique au seuillage.
        

## Diapositive 24 : Fonction de seuillage en SIMD

- **Q : Note we have used align accesses in the above. Can you justify?** (Notez que nous avons utilisé des accès alignés ci-dessus. Pouvez-vous justifier ?)
    
    - **R :** Le code utilise `movapd` (Move Aligned Packed Double-Precision Floating-Point) pour charger le masque (`xmm7`) et les données d'image (`xmm0`), ainsi que pour stocker le résultat. L'utilisation d'accès alignés (`movapd`, `movaps`, `movdqa`) est justifiée si l'on peut **garantir** que les adresses mémoire pointées par `eax` (pour `mask`), `esi` (pour `ptrin`) et `edi` (pour `ptrout`) sont des multiples de 16 octets (pour les registres XMM 128 bits). Les accès alignés sont potentiellement plus rapides sur certaines architectures que les accès non alignés (`movupd`, `movups`, `movdqu`) car ils peuvent éviter des pénalités liées à la lecture de données chevauchant deux lignes de cache ou à une gestion plus complexe par l'unité de chargement/stockage. Cependant, si les adresses ne sont pas garanties d'être alignées, l'utilisation de `movapd` provoquera une **exception de protection générale (crash)**. L'utilisation de `movdqu` (non aligné) serait plus sûre si l'alignement n'est pas certain. La justification repose donc sur une hypothèse (ou une garantie obtenue par l'allocation mémoire) d'alignement des données pour une performance potentiellement meilleure.
        

## Diapositive 36 : Important à connecter (Filtre Max/Min SIMD)

- **Q : Why do we increment ptrin & ptrout pointers by 14?** (Pourquoi incrémentons-nous les pointeurs ptrin & ptrout de 14 ?)
    
    - **R :** Le calcul du filtre spatial (ici 3×3) avec un vecteur SIMD de 16 octets (N=16) produit 16 résultats potentiels. Cependant, en raison de l'effet de bordure inhérent au filtre spatial et à l'algorithme SIMD utilisé (qui décale les vecteurs pour comparer les voisins), seuls les N−(n−1) éléments centraux du vecteur résultat sont valides pour un filtre de taille n. Ici, n=3 (filtre 3×3), donc 16−(3−1)=16−2=14 pixels sont valides par itération SIMD. On n'écrit donc que 14 octets valides dans `ptrout`. Pour l'itération suivante, on avance le pointeur d'entrée `ptrin` de 14 pour commencer le calcul du prochain bloc de 14 pixels valides (même si on lit 16 octets, les 2 premiers chevaucheront les 2 derniers lus dans l'itération précédente, fournissant le contexte nécessaire). Les pointeurs sont incrémentés de 14 pour traiter séquentiellement les blocs de résultats valides. _(Note: L'implémentation exacte peut varier, mais c'est l'explication la plus probable pour un incrément de 14 dans ce contexte)_.
        
- **Q : Could you write the code for the aligned access?** (Pourriez-vous écrire le code pour l'accès aligné ?)
    
    - **R :** Pour utiliser l'accès aligné (par ex. `movdqa` au lieu de `movdqu`), il faudrait s'assurer que les pointeurs `esi` (entrée) et `edi` (sortie) pointent toujours vers des adresses multiples de 16 au début de chaque itération de la boucle. Cela implique :
        
        1. **Allocation Alignée :** Allouer les buffers `src` et `dest` avec une fonction qui garantit un alignement sur 16 octets (par ex. `_aligned_malloc` sous Windows/MSVC, `aligned_alloc` en C11, ou des techniques manuelles).
            
        2. **Gestion des Bords de Ligne :** Si la largeur de l'image (`W`) n'est pas un multiple de 16, le début de chaque ligne (sauf la première) ne sera pas aligné si on traite l'image comme un seul bloc 1D. Il faudrait traiter l'image ligne par ligne et potentiellement gérer les pixels restants à la fin de chaque ligne (qui ne forment pas un bloc complet de 16) avec du code scalaire ou des techniques de masquage SIMD.
            
        3. **Incrémentation des Pointeurs :** L'incrémentation des pointeurs (`add esi, 14` / `add edi, 14`) rendrait l'alignement impossible après la première itération. Pour maintenir l'alignement, il faudrait lire/écrire des blocs de 16 octets (`add esi, 16` / `add edi, 16`) et gérer le fait que seuls 14 résultats sont valides (par exemple, en ne copiant que les 14 octets pertinents ou en utilisant des masques). Cela complexifie la logique.
            
    - En résumé, passer à l'accès aligné nécessite une gestion plus complexe de l'allocation mémoire et potentiellement de la logique de boucle pour maintenir l'alignement, surtout si la largeur de l'image n'est pas un multiple de la taille du vecteur SIMD.
        
- **Q : How to calculate the value to place in ecx register? (how do you decide on the loop counter)** (Comment calculer la valeur à placer dans le registre ecx ? (comment décidez-vous du compteur de boucle))
    
    - **R :** Le compteur de boucle `ecx` doit contenir le nombre total d'itérations SIMD nécessaires pour traiter tous les pixels valides de l'image.
        
        - Chaque itération traite un bloc produisant 14 pixels valides (comme vu ci-dessus).
            
        - L'image a une largeur `W` et une hauteur `H`.
            
        - Le filtre 3×3 ne peut pas être calculé sur les bords (1 pixel de chaque côté). La zone de calcul valide a une largeur de `W-2` et une hauteur de `H-2`.
            
        - Le nombre total de pixels valides à calculer est `(W-2) * (H-2)`.
            
        - Cependant, l'algorithme SIMD traite généralement l'image ligne par ligne ou par blocs. Si on traite ligne par ligne, chaque ligne a `W-2` pixels valides horizontalement. Le nombre d'itérations SIMD par ligne serait `ceil((W-2) / 14)` (en gérant le dernier bloc potentiellement incomplet). Le nombre total d'itérations serait `(H-2) * ceil((W-2) / 14)`.
            
        - Si l'implémentation de la diapo 35 traite l'image comme un seul grand tableau 1D et avance de 14 à chaque fois, le nombre d'itérations `ii` serait approximativement `(W*H) / 14`, mais cela ne gère pas correctement les bords de l'image ni les fins de ligne. Un calcul plus précis dépendrait de la stratégie exacte de parcours de l'image (ligne par ligne ou bloc 1D) et de la gestion des bords. La valeur `ii` doit représenter le nombre de fois où la boucle principale (label `l1`) doit s'exécuter.
            
- **Q : What happens with image border?** (Que se passe-t-il avec la bordure de l'image ?)
    
    - **R :** Comme mentionné dans la diapositive 33, les pixels sur la bordure de l'image ne peuvent pas être calculés car leur voisinage 3×3 sortirait des limites de l'image. L'implémentation ignore généralement ces pixels. L'image résultante sera donc légèrement plus petite (par exemple, `(W-2) x (H-2)` pour un filtre 3×3) ou aura une bordure non définie (ou remplie avec une couleur constante, ou une copie du bord de l'image originale). Le code de la diapo 35 ne montre pas explicitement la gestion des bords globaux de l'image, il se concentre sur le calcul SIMD d'une ligne (ou d'un bloc).
        
- **Q : How will sub-window impact the algorithm above?** (Comment la sous-fenêtre impactera-t-elle l'algorithme ci-dessus ?)
    
    - **R :** La taille de la sous-fenêtre (le voisinage du filtre, n×n) impacte directement l'algorithme SIMD :
        
        - **Nombre de lectures :** Il faut lire n lignes de l'image source pour calculer le maximum/minimum vertical (`pmaxub`/`pminub`).
            
        - **Nombre de décalages/comparaisons horizontales :** Il faut effectuer n−1 décalages (`psrldq`) et n−1 comparaisons horizontales (`pmaxub`/`pminub`) pour trouver le maximum/minimum dans le voisinage horizontal.
            
        - **Nombre de pixels valides par vecteur :** Le nombre de pixels valides calculés par itération SIMD diminue à mesure que la taille du filtre n augmente : N−(n−1). Un filtre plus grand produit moins de résultats valides par opération SIMD.
            
- **Q : How it will affect speed-up?** (Comment cela affectera-t-il l'accélération ?)
    
    - **R :** L'accélération (speed-up) obtenue grâce au SIMD dépend du nombre de pixels traités en parallèle par rapport à une implémentation scalaire.
        
        - Un filtre plus petit (par ex. 3×3) produit plus de résultats valides par itération (14 sur 16) et nécessite moins d'opérations par pixel, conduisant potentiellement à une accélération plus élevée par rapport à sa version scalaire.
            
        - Un filtre plus grand (par ex. 7×7) produit moins de résultats valides par itération (16−(7−1)=10) et nécessite plus de lectures et d'opérations SIMD par pixel de sortie. Bien que le SIMD puisse toujours offrir une accélération par rapport au scalaire, le facteur d'accélération _relatif_ pourrait être moindre pour des filtres plus grands en raison de l'efficacité réduite par vecteur et de la complexité accrue des opérations par pixel. L'accélération absolue dépendra toujours de la complexité de l'opération scalaire équivalente.
            

## Diapositive 44 : Histogramme d'une image

- **Q : What do you get by summing the numbers in all bins?** (Qu'obtenez-vous en additionnant les nombres dans tous les bacs [bins] ?)
    
    - **R :** Chaque "bin" de l'histogramme (indexé de 0 à 255) stocke le nombre de pixels dans l'image ayant cette valeur de niveau de gris spécifique. En additionnant les nombres (comptes) dans tous les bins, on obtient le **nombre total de pixels dans l'image** (W×H).
        
- **Q : We need to be careful here, why?** (Nous devons être prudents ici, pourquoi ? [lors du calcul de l'histogramme])
    
    - **R :** La prudence est requise principalement à cause des **accès mémoire potentiellement aléatoires** lors de l'incrémentation des compteurs dans le tableau `histo`. L'instruction `histo[src[i]]++;` implique :
        
        1. Lire la valeur du pixel `src[i]`.
            
        2. Utiliser cette valeur comme indice pour accéder à un emplacement dans le tableau `histo`.
            
        3. Lire la valeur actuelle du compteur à cet emplacement.
            
        4. Incrémenter cette valeur.
            
        5. Écrire la nouvelle valeur dans le tableau `histo`.
            
    - Si des pixels voisins dans l'image ont des valeurs très différentes, les accès au tableau `histo` sauteront entre des emplacements mémoire potentiellement éloignés. Cela peut entraîner une mauvaise localité de cache (cache misses fréquents sur le tableau `histo`), ce qui peut ralentir considérablement l'exécution, même si l'algorithme semble simple. De plus, dans un contexte parallèle (multi-threading ou SIMD complexe), il faudrait gérer les accès concurrents (race conditions) au même bin de l'histogramme.
        

## Diapositive 46 : Histogramme : Implémentation C

- **Q : why do we need int???** (pourquoi avons-nous besoin de int ??? [pour le tableau `histo`])
    
    - **R :** Le tableau `histo` stocke le _nombre d'occurrences_ de chaque niveau de gris. Si l'image est grande, le nombre de pixels ayant une certaine valeur peut dépasser la capacité d'un type plus petit comme `unsigned char` (max 255) ou `unsigned short` (max 65535). Par exemple, une image de 1024x1024 a plus d'un million de pixels. Il est tout à fait possible que plus de 65535 pixels aient la même valeur (par exemple, le noir dans une image majoritairement noire). Utiliser `int` (généralement 32 bits signés, max ~2 milliards) ou `unsigned int` (max ~4 milliards) ou même `long` offre une plage suffisante pour compter les occurrences de pixels même dans de très grandes images sans risque de dépassement du compteur lui-même.
        

## Diapositive 47 : Histogramme : Implémentation SIMD

- **Q : Can you do this?** (Pouvez-vous faire cela ?)
    
    - **R :** Oui, il est techniquement possible d'implémenter le calcul d'histogramme en utilisant SIMD, mais ce n'est pas direct.
        
- **Q : Suggest how to implement this in SIMD** (Suggérez comment implémenter cela en SIMD)
    
    - **R :** L'implémentation SIMD d'un histogramme est complexe à cause du problème d'"accès dispersé" (scatter) : plusieurs pixels traités en parallèle par SIMD peuvent nécessiter d'incrémenter des bins différents (et potentiellement identiques) de l'histogramme. Les approches possibles incluent :
        
        1. **Histogrammes multiples/privés :** Chaque "voie" SIMD (ou chaque thread) calcule un histogramme partiel dans une mémoire privée. Ensuite, tous les histogrammes partiels sont fusionnés à la fin.
            
        2. **Instructions Scatter/Gather (si disponibles) :** Des extensions SIMD plus récentes (comme AVX2/AVX-512) incluent des instructions `gather` pour lire des données depuis des adresses mémoire dispersées (basées sur des indices dans un vecteur) et parfois des instructions `scatter` pour écrire. On pourrait les utiliser pour accéder aux bins, mais la gestion des conflits (plusieurs pixels voulant incrémenter le même bin simultanément) reste nécessaire, souvent via des opérations atomiques ou des techniques de tri préalable.
            
        3. **Approches basées sur les conflits :** Détecter les conflits au sein d'un vecteur SIMD (plusieurs pixels mappant au même bin) et les gérer séquentiellement ou via des opérations atomiques.
            
    - Aucune de ces approches n'est aussi simple que les exemples précédents (addition, seuillage).
        
- **Q : Will it be efficient?** (Sera-ce efficace ?)
    
    - **R :** L'efficacité d'une implémentation SIMD pour l'histogramme **n'est pas garantie** et est souvent **limitée**. Les problèmes de localité de cache et la complexité de la gestion des accès dispersés et des conflits peuvent annuler, voire inverser, les gains potentiels du traitement parallèle des pixels. Pour de nombreuses architectures, une implémentation scalaire bien optimisée (qui bénéficie potentiellement mieux du cache pour le tableau `histo`) peut être aussi rapide, voire plus rapide, qu'une implémentation SIMD complexe, surtout si le matériel ne dispose pas d'instructions `gather`/`scatter` efficaces. C'est un cas classique où le SIMD n'est pas nécessairement la meilleure solution en raison de la nature du problème (dépendances via les indices de tableau).
        

## Diapositive 54 : SSE3 contre SSE2 (Multiplication complexe)

- **Q : What do move functions used in both codes require?** (Qu'est-ce que les fonctions de déplacement utilisées dans les deux codes nécessitent ?)
    
    - **R :** En examinant les instructions de déplacement :
        
        - **Code SSE2 :** Utilise `movsd` (Move Scalar Double-Precision Floating-Point) et `movapd` (Move Aligned Packed Double-Precision). `movsd` déplace 64 bits, `movapd` déplace 128 bits. `movapd` **nécessite que l'adresse mémoire soit alignée sur 16 octets**. `movsd` n'a pas cette contrainte stricte pour l'adresse mémoire unique qu'il accède.
            
        - **Code SSE3 :** Utilise `movapd` et `movddup` (Move One Double-FP and Duplicate). `movapd` nécessite toujours un **alignement sur 16 octets** pour les accès mémoire. `movddup` lit 64 bits (un `double`) depuis la mémoire et n'a pas de contrainte d'alignement stricte pour ces 64 bits.
            
    - Donc, la principale exigence commune (pour `movapd`) est **l'alignement des données en mémoire sur une frontière de 16 octets** lors du transfert de 128 bits entre la mémoire et les registres XMM.
        

## Diapositive 57 : Déplacements standards : alignés vs non alignés & type de données

- **Q : Can you write piece of code to check this?** (Pouvez-vous écrire un bout de code pour vérifier cela ? [performance aligné vs non aligné])
    
    - **R :** Pour vérifier la différence de performance, on peut écrire un programme C/C++ qui :
        
        1. Alloue deux grands blocs de mémoire : un garanti aligné sur 16 octets (par ex. avec `_aligned_malloc`) et un non aligné (par ex. alloué normalement avec `malloc` et décalé d'un octet).
            
        2. Écrit une boucle qui copie une grande quantité de données (par ex. plusieurs Mo ou Go) du bloc source vers le bloc destination en utilisant l'instruction de déplacement **alignée** (`movaps`, `movapd` ou `movdqa` via intrinsics ou assembleur inline) sur les blocs alignés. Mesure le temps d'exécution (par ex. avec `QueryPerformanceCounter`).
            
        3. Écrit une boucle similaire qui copie les données en utilisant l'instruction de déplacement **non alignée** correspondante (`movups`, `movupd` ou `movdqu`) sur les blocs non alignés (ou même sur les blocs alignés pour comparer). Mesure le temps d'exécution.
            
        4. Répète les mesures plusieurs fois et compare les temps moyens pour voir s'il y a une différence significative. Il faut s'assurer que le compilateur n'optimise pas excessivement le code (par ex. en déclarant les pointeurs comme `volatile` ou en utilisant des techniques pour empêcher l'élimination de la boucle).
            

## Diapositive 59 : Gestion des cache-splits

- **Q : Can you write piece of code to check this?** (Pouvez-vous écrire un bout de code pour vérifier cela ? [performance `lddqu` vs `movdqu`/`movupd`])
    
    - **R :** La méthodologie serait similaire à la précédente :
        
        1. Allouer un grand bloc de mémoire source.
            
        2. Créer des scénarios d'accès où les lectures de 16 octets chevauchent systématiquement les lignes de cache (par exemple, en lisant à partir d'adresses comme `base + 8`, `base + 24`, `base + 40`, etc., en supposant des lignes de cache de 64 octets).
            
        3. Écrire une boucle qui effectue un grand nombre de ces lectures "cache-split" en utilisant l'instruction **non alignée standard** (`movdqu` ou `movupd`). Mesurer le temps.
            
        4. Écrire une boucle similaire effectuant les mêmes lectures "cache-split" mais en utilisant l'instruction `lddqu`. Mesurer le temps.
            
        5. Comparer les temps. Selon la documentation, `lddqu` devrait être plus performant dans ce scénario spécifique de lectures non alignées provoquant des cache-splits. Il faudrait aussi tester le scénario mentionné où les écritures ultérieures aux mêmes adresses pourraient être ralenties après `lddqu`.
            

## Diapositive 60 : Déplacements non temporels

- **Q : Can you could write a code to compare?** (Pourriez-vous écrire un code pour comparer ? [performance des déplacements non temporels])
    
    - **R :** Pour comparer les déplacements non temporels (`movntdq`, `movntps`, `movntpd` pour les écritures, `movntdqa` pour les lectures) avec les déplacements standards (cachés) :
        
        1. Allouer de grands blocs de mémoire source et destination (garantis alignés, car les instructions `movnt*` le requièrent).
            
        2. Écrire une boucle qui lit des données de la source et les écrit dans la destination en utilisant les instructions de déplacement **standards** (par ex. `movdqa`). Mesurer le temps. Il est crucial que le volume de données dépasse largement la taille des caches du CPU pour observer l'effet du caching.
            
        3. Écrire une boucle similaire effectuant la même copie mais en utilisant les instructions de déplacement **non temporelles** (`movntdqa` pour lire si applicable, `movntdq`/`movntps`/`movntpd` pour écrire). Mesurer le temps.
            
        4. Comparer les temps. Les déplacements non temporels devraient être plus rapides si les données ne sont effectivement lues/écrites qu'une seule fois et ne bénéficient pas du caching (par exemple, lors du streaming de très grands volumes de données), car ils évitent la pollution du cache. Si les données sont réutilisées peu après, les déplacements standards (cachés) seront probablement plus rapides.
            

## Diapositive 64 : Utilisation du temps (absolu) de l'ordinateur

- **Q : Resolution is not great, could we do better?** (La résolution n'est pas géniale, pourrait-on faire mieux ?)
    
    - **R :** Oui, on peut faire beaucoup mieux. La fonction `time()` retourne le temps en secondes, ce qui est une résolution très grossière pour mesurer la performance de code qui s'exécute souvent en millisecondes ou microsecondes. Les diapositives suivantes présentent des alternatives avec une meilleure résolution : `clock()` (résolution en millisecondes, mais liée aux "ticks" de l'OS) et surtout `QueryPerformanceCounter` (QPC) sous Windows (résolution en microsecondes ou mieux, basée sur un compteur matériel haute fréquence).
        

## Diapositive 66 : Exemple (Utilisation de `clock()`)

- **Q : What do you expect to see as results with & without line 15?** (Que vous attendez-vous à voir comme résultats avec & sans la ligne 15 ? [Probablement ligne 13])
    
    - **R :** En supposant que la question concerne l'activation ou non de la ligne 13 (`tmp2=j*sin(j);`) à l'intérieur de la boucle interne :
        
        - **Sans la ligne 13 (commentée) :** La boucle interne effectue uniquement une incrémentation d'entier (`tmp1+=1;`), ce qui est une opération très rapide. Le temps mesuré par `clock()` pour les 100 millions d'itérations sera relativement court.
            
        - **Avec la ligne 13 (décommentée) :** La boucle interne effectue maintenant, en plus de l'incrémentation, un calcul en virgule flottante impliquant une multiplication et un appel à la fonction trigonométrique `sin()`. Ces opérations sont beaucoup plus coûteuses en termes de cycles CPU qu'une simple incrémentation d'entier. Par conséquent, le temps mesuré par `clock()` pour exécuter la boucle sera **significativement plus long** avec la ligne 13 activée que sans elle. Les résultats affichés pour `t` seront donc nettement plus élevés dans le second cas.