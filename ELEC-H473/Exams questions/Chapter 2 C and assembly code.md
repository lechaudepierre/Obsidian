# Réponses aux Questions des Diapositives (ELEC-H-473 Th02)

Voici les réponses aux questions identifiées dans les diapositives du document `ELECH473_Th02.pdf`.

## Diapositive 11 : Variables (1/2)

- **Q : Why is it important to know the data type at this stage?** (Pourquoi est-il important de connaître le type de données à ce stade [de la déclaration] ?)
    
    - **R :** Il est important de connaître le type de données lors de la déclaration d'une variable car cela permet au compilateur de réserver la quantité correcte d'espace mémoire nécessaire pour stocker une valeur de ce type (par exemple, 1 octet pour un `char`, 4 octets pour un `int` sur de nombreux systèmes). Cela détermine également les opérations valides pour cette variable et comment sa valeur sera interprétée.
        
- **Q : What is the size of the address space assuming the address above?** (Quelle est la taille de l'espace d'adressage en supposant l'adresse ci-dessus [0x56472384] ?)
    
    - **R :** L'adresse `0x56472384` est un nombre hexadécimal de 8 chiffres. Chaque chiffre hexadécimal représente 4 bits (16=24). Donc, 8 chiffres hexadécimaux représentent 8×4=32 bits. Un espace d'adressage de 32 bits peut adresser 232 emplacements mémoire uniques. Si chaque emplacement contient 1 octet, la taille totale de l'espace d'adressage est de 232 octets, ce qui équivaut à 4 Gigaoctets (Go).
        

## Diapositive 15 : Ordre des octets (Byte order)

- **Q : What is the 4 byte integer stored at address 0X12345678 assuming little endian order?** (Quel est l'entier de 4 octets stocké à l'adresse 0X12345678 en supposant un ordre little-endian ?)
    
    - **R :** En ordre little-endian, l'octet de poids faible est stocké à l'adresse la plus basse, et l'octet de poids fort à l'adresse la plus haute. Les octets donnés aux adresses successives sont :
        
        - `0x12345678`: `0x10` (Octet de poids le plus faible - LSB)
            
        - `0x12345679`: `0xAB`
            
        - `0x1234567A`: `0x23`
            
        - `0x1234567B`: `0x75` (Octet de poids le plus fort - MSB)
            
    - En reconstituant l'entier de 4 octets avec le MSB à gauche et le LSB à droite, la valeur est `0x7523AB10`.
        

## Diapositive 18 : Portée des variables (Variable scope) dans la fonction

- **Q : Where does this 5 comes from?** (D'où vient ce 5 ?)
    
    - **R :** Le 5 provient de la variable `i` déclarée à la ligne 2 (`int i=4;`). Cette variable est locale à la fonction `main`. Elle est incrémentée à la ligne 4 (`i++;`), passant de 4 à 5. Le bloc `if` aux lignes 10-14 déclare une _nouvelle_ variable locale `i` (initialisée à 100) qui masque temporairement la première `i`. Cette seconde `i` est détruite à la fin du bloc `if` (ligne 14). Le `printf` à la ligne 16 fait donc référence à la première variable `i` (celle déclarée à la ligne 2), dont la valeur est restée 5.
        

## Diapositive 19 : Portée des variables en dehors de la fonction

- **Q : What are the values of i, j before and after func() has been called?** (Quelles sont les valeurs de i, j avant et après l'appel de func() ?)
    
    - **R :**
        
        - **Avant l'appel de `func()` (dans `main`, après la ligne 6) :**
            
            - `i` (global) : A été initialisé à 4 (ligne 1), puis incrémenté à 5 (ligne 6). Donc `i = 5`.
                
            - `j` (local à `main`) : A été initialisé à 7 (ligne 4), puis incrémenté à 8 (ligne 5). Donc `j = 8`.
                
        - **Dans `func()` (pendant l'exécution) :**
            
            - `i` (global) : Est incrémenté (ligne 13). Il passe de 5 à 6.
                
            - `j` (local à `func`) : Est déclaré et initialisé à 10 (ligne 11), puis incrémenté à 11 (ligne 12). Ce `j` est différent du `j` de `main`.
                
        - **Après l'appel de `func()` (de retour dans `main`, après la ligne 7) :**
            
            - `i` (global) : Sa valeur est maintenant 6, car il a été modifié dans `func()`.
                
            - `j` (local à `main`) : Sa valeur est toujours 8. La variable `j` locale à `func()` a été détruite à la sortie de `func()`, et cela n'affecte pas la variable `j` locale à `main`.
                

## Diapositive 26 : Opérateurs logiques booléens

- **Q : Line 1, 2 & 4 will be printed, but not Line 3. Can you show why?** (Les lignes 1, 2 & 4 seront affichées, mais pas la ligne 3. Pouvez-vous montrer pourquoi ?)
    
    - **R :**
        
        - **Ligne 6 (`if (a && b)`):** Initialement, `a=5` (VRAI) et `b=20` (VRAI). `VRAI && VRAI` est VRAI. La ligne 1 est affichée.
            
        - **Ligne 7 (`if (a || b)`):** `a=5` (VRAI) et `b=20` (VRAI). `VRAI || VRAI` est VRAI. La ligne 2 est affichée.
            
        - **Ligne 11 (`if (a && b)`):** Après modification, `a=0` (FAUX) et `b=10` (VRAI). `FAUX && VRAI` est FAUX. La condition du `if` est fausse, donc le bloc `else` (ligne 12) est exécuté. La ligne 3 n'est pas affichée.
            
        - **Ligne 14 (`if (!(a && b))`):** On a vu que `a && b` est FAUX. `!(FAUX)` est VRAI. La condition est vraie. La ligne 4 est affichée.
            

## Diapositive 27 : Opérateurs booléens bit à bit

- **Q : The above is equivalent to multiply / divide by power 2 - Why?** (Ce qui précède est équivalent à multiplier / diviser par une puissance de 2 - Pourquoi ?)
    
    - **R :** En représentation binaire, un décalage à gauche (`<<`) d'une position multiplie la valeur par 2 (tout comme ajouter un zéro à la fin d'un nombre décimal le multiplie par 10). Chaque bit est déplacé vers une position de poids supérieur. Un décalage à droite (`>>`) d'une position divise la valeur entière par 2. Chaque bit est déplacé vers une position de poids inférieur ; le bit le moins significatif est perdu. Décaler de N positions équivaut à multiplier ou diviser par 2N.
        
- **Q : Can you show why?** (Pouvez-vous montrer pourquoi [c=67]?)
    
    - **R :** Il faut faire l'opération ET bit à bit entre `a=195` et `b=87`.
        
        - `a = 195` en décimal = `11000011` en binaire (sur 8 bits)
            
        - `b = 87` en décimal = `01010111` en binaire (sur 8 bits)
            
        - Effectuons l'opération ET (&) bit par bit :
            
            ```
              11000011  (a)
            & 01010111  (b)
            ----------
              01000011  (c)
            ```
            
        - `01000011` en binaire = 0128+164+032+016+08+04+12+11=64+2+1=67 en décimal. Donc `c = 67`.
            

## Diapositive 29 : Contrôle du flux d'instructions

- **Q : Can you explain "xor" in this sentence?** (Pouvez-vous expliquer "xor" dans cette phrase ?)
    
    - **R :** Dans la phrase " evaluates to a Boolean value of TRUE xor FALSE", "xor" (OU exclusif) signifie que l'expression évaluée par une structure de contrôle comme `if` doit aboutir _soit_ à VRAI, _soit_ à FAUX, mais pas les deux ni aucun des deux. C'est une manière de souligner que le résultat est strictement l'une ou l'autre des deux valeurs booléennes possibles.
        

## Diapositive 34 : Les fonctions permettent de structurer les programmes

- **Q : Who's providing arguments to the program above?** (Qui fournit les arguments au programme ci-dessus ?)
    
    - **R :** Les arguments `argc` (argument count) et `argv` (argument vector) de la fonction `main` sont fournis par le **système d'exploitation** (ou l'environnement d'exécution) lorsque le programme est lancé, généralement depuis une ligne de commande.
        
        - `argc` est un entier indiquant le nombre d'arguments passés sur la ligne de commande (incluant le nom du programme lui-même).
            
        - `argv` est un tableau de chaînes de caractères (pointeurs `char*`), où `argv[0]` est le nom du programme, `argv[1]` est le premier argument, et ainsi de suite jusqu'à `argv[argc-1]`.
            

## Diapositive 35 : Passer des valeurs aux fonctions

- **Q : What seams to be the problem here?** (Quel semble être le problème ici ?)
    
    - **R :** Le problème est dans l'appel à la fonction `F2` à la ligne 11 : `F2 (x,y);`. La fonction `F2` est déclarée comme `void F2 (unsigned char src, unsigned char dest);`, attendant donc deux arguments de type `unsigned char`. Cependant, l'appel utilise les variables `x` et `y`, qui sont déclarées comme `int` (ligne 8). Bien que le compilateur puisse effectuer une conversion implicite (troncation) de `int` vers `unsigned char`, cela peut entraîner une perte de données si les valeurs de `x` ou `y` dépassent la plage d'un `unsigned char` (0-255). De plus, cela peut ne pas correspondre à l'intention du programmeur si `F2` était censée travailler avec des `int`. L'appel à `F1(x,y)` est correct car `F1` attend des `int`.
        

## Diapositive 38 : Concepts de base (Pointeurs)

- **Q : Why do we need to define a data type for a pointer?** (Pourquoi devons-nous définir un type de données pour un pointeur ?)
    
    - **R :** Définir un type de données pour un pointeur (par ex., `int *`, `char *`) est crucial pour l'**arithmétique des pointeurs** et le **déréférencement**.
        
        - **Arithmétique :** Quand on incrémente un pointeur (`ptr++`), il ne s'incrémente pas de 1 octet, mais de la taille du type de données qu'il pointe (`sizeof(type)`). Par exemple, incrémenter un `int *` l'avance de 4 octets (sur un système où `sizeof(int)` est 4) pour pointer vers l'entier suivant dans un tableau.
            
        - **Déréférencement :** Quand on déréférence un pointeur (`*ptr`) pour accéder à la valeur pointée, le compilateur doit savoir combien d'octets lire depuis l'adresse mémoire (1 pour `char`, 4 pour `int`, etc.) et comment interpréter ces octets.
            
- **Q : What data type are the addresses?** (Quel type de données sont les adresses ?)
    
    - **R :** Les adresses elles-mêmes sont fondamentalement des **entiers non signés**. La taille de cet entier (le nombre de bits nécessaires pour représenter une adresse) dépend de l'architecture du système (par exemple, 32 bits pour une architecture 32 bits, 64 bits pour une architecture 64 bits). En C, bien qu'il n'y ait pas de type spécifique "adresse" garanti, on utilise souvent des types comme `uintptr_t` (un entier non signé ayant la taille d'un pointeur) ou `void *` (un pointeur générique) pour manipuler des adresses de manière abstraite, mais les pointeurs typés (`int *`, `char *`, etc.) sont utilisés pour stocker des adresses de données spécifiques.
        

## Diapositive 39 : Exemple (Pointeurs)

- **Q : Explain the reasoning for the above statement** (Expliquez le raisonnement de l'affirmation ci-dessus [lecture sur mauvaise adresse tolérée, écriture provoque crash])
    
    - **R :**
        
        - **Lecture sur une mauvaise adresse :** Lire depuis une adresse mémoire invalide (qui n'appartient pas à l'espace alloué au programme ou qui est protégée) peut ou non causer un crash immédiat. Le programme pourrait lire des données "poubelles" sans s'en rendre compte, menant potentiellement à un comportement incorrect plus tard. Dans certains cas (accès à une page mémoire non mappée ou protégée), le système d'exploitation interceptera l'accès invalide et provoquera une erreur de segmentation (crash). La tolérance n'est donc pas garantie, mais le résultat peut être simplement des données invalides.
            
        - **Écriture sur une mauvaise adresse :** Écrire sur une adresse mémoire invalide est beaucoup plus dangereux. Si l'écriture se fait sur une zone mémoire appartenant à une autre partie du programme (par exemple, écraser une autre variable, une instruction de code, ou des informations de la pile comme une adresse de retour), cela corrompra l'état du programme de manière imprévisible, menant très probablement à un crash ou à un comportement erratique sévère. Si l'écriture tente d'accéder à une zone protégée par le système d'exploitation, cela provoquera une erreur de segmentation immédiate (crash). L'écriture non contrôlée est une cause majeure de bugs et de failles de sécurité.
            

## Diapositive 40 : Variables & Pointeurs

- **Q : What do we see in the terminal after execution of the code above?** (Que voyons-nous dans le terminal après l'exécution du code ci-dessus ?)
    
    - **R :** Le terminal affichera (les adresses exactes varieront à chaque exécution) :
        
        1. `Variable var1: 20` (Affiche la valeur de `var1`)
            
        2. `Address of var1: <adresse_hex_de_var1>` (Affiche l'adresse mémoire où `var1` est stockée)
            
        3. `Content of the address: 20` (Affiche la valeur pointée par `ptr`, qui est la valeur de `var1`)
            
        4. `Address stored in pointer: <adresse_hex_de_var1>` (Affiche l'adresse stockée dans la variable `ptr`, qui est l'adresse de `var1`)
            
        5. `Address of the pointer: <adresse_hex_de_ptr>` (Affiche l'adresse mémoire où la variable `ptr` elle-même est stockée)
            
        
        - Notez que les adresses affichées aux lignes 2 et 4 seront identiques. L'adresse affichée à la ligne 5 sera différente.
            

## Diapositive 43 : Pointeurs, appels de fonctions et arguments (2/2)

- **Q : Can you explain?** (Pouvez-vous expliquer [comment passer un nombre arbitraire d'arguments via pointeurs] ?)
    
    - **R :** En passant un pointeur à une fonction, on ne passe pas les données elles-mêmes, mais seulement l'adresse où les données commencent en mémoire. Si ces données représentent une structure ou un tableau (une séquence contiguë d'éléments), la fonction peut accéder à autant d'éléments qu'elle le souhaite à partir de cette adresse de départ, à condition qu'elle sache comment interpréter la mémoire (par exemple, connaître la taille de la structure ou avoir un moyen de déterminer la fin du tableau, comme un marqueur de fin ou une taille passée séparément). On peut ainsi passer une référence à une grande quantité de données (potentiellement de taille inconnue au moment de la compilation) en ne passant qu'une seule adresse (un pointeur), ce qui est très efficace. C'est ainsi que l'on passe des tableaux ou des structures complexes aux fonctions sans copier toutes les données.
        

## Diapositive 44 : Exemple : Analyse des arguments de ligne de commande

- **Q : Imagine a concrete example of the execution for the code above?** (Imaginez un exemple concret d'exécution pour le code ci-dessus ?)
    
    - **R :** Supposons que le programme compilé s'appelle `myprog`. Si on l'exécute depuis la ligne de commande comme suit :
        
        ```
        ./myprog argument1 test 123
        ```
        
    - L'exécution affichera dans le terminal :
        
        ```
        cmdline args count=4 
        Executable name=./myprog 
        Arg1=argument1 
        Arg2=test 
        Arg3=123 
        
        ```
        
    - Explication :
        
        - `argc` (nombre d'arguments) est 4 : le nom du programme + les 3 arguments fournis.
            
        - `argv[0]` est le nom de l'exécutable : `./myprog`.
            
        - La boucle `for` itère de `i=1` à `argc-1` (donc 1, 2, 3).
            
        - `argv[1]` est "argument1".
            
        - `argv[2]` est "test".
            
        - `argv[3]` est "123".
            

## Diapositive 46 : Tableaux unidimensionnels

- **Q : What will happen in the above example if we add as line 5: myArray [100] = 59;** (Que se passera-t-il dans l'exemple ci-dessus si nous ajoutons comme ligne 5 : myArray [100] = 59;)
    
    - **R :** L'instruction `myArray[100] = 59;` tente d'écrire à l'indice 100 du tableau `myArray`. Cependant, `myArray` a été déclaré avec une taille de 100 (`int myArray[100];`), ce qui signifie que les indices valides vont de 0 à 99. L'accès à l'indice 100 est donc un **accès hors limites (out-of-bounds access)**.
        
        - En C, ce type d'accès n'est généralement pas vérifié au moment de la compilation ou de l'exécution. Le programme tentera d'écrire la valeur 59 dans la zone mémoire située _juste après_ la fin du tableau `myArray`.
            
        - Le résultat est **indéfini** et potentiellement dangereux :
            
            - Cela pourrait écraser la valeur d'une autre variable (par exemple, le début de `myArray2` si le compilateur les a placés consécutivement en mémoire).
                
            - Cela pourrait corrompre des données internes utilisées par le programme ou la pile.
                
            - Cela pourrait causer un crash immédiat (erreur de segmentation) si la mémoire accédée est protégée ou non valide.
                
            - Cela pourrait ne causer aucun problème apparent immédiatement, mais entraîner un comportement erroné plus tard.
                

## Diapositive 47 : Manipulation de tableaux 1D

- **Q : Explain the above for loops in details** (Expliquez les boucles for ci-dessus en détail)
    
    - **R :** Les deux boucles `for` effectuent la même opération : additionner les éléments correspondants des tableaux `A` et `B` et stocker le résultat dans le tableau `C` (C[i]=A[i]+B[i]) pour tous les éléments de 0 à 99.
        
        - **Option 1 (`for (i=0; i<100; i++)`) :**
            
            - Initialisation : `i` est initialisé à 0.
                
            - Condition : La boucle continue tant que `i` est strictement inférieur à 100.
                
            - Itération : À l'intérieur de la boucle, l'opération `C[i] = A[i] + B[i];` est effectuée.
                
            - Incrémentation : Après chaque itération, `i` est incrémenté de 1 (`i++`).
                
            - La boucle s'exécute pour `i` allant de 0, 1, 2, ..., jusqu'à 99. Lorsque `i` devient 100, la condition `i<100` devient fausse et la boucle se termine.
                
        - **Option 2 (`for (i=1; i<=100; i++)`) :**
            
            - Initialisation : `i` est initialisé à 1.
                
            - Condition : La boucle continue tant que `i` est inférieur ou égal à 100.
                
            - Itération : À l'intérieur de la boucle, l'opération `C[i-1] = A[i-1] + B[i-1];` est effectuée. Notez l'utilisation de `i-1` pour accéder aux indices corrects du tableau (de 0 à 99).
                
            - Incrémentation : Après chaque itération, `i` est incrémenté de 1 (`i++`).
                
            - La boucle s'exécute pour `i` allant de 1, 2, 3, ..., jusqu'à 100. Lorsque `i` devient 101, la condition `i<=100` devient fausse et la boucle se termine.
                
- **Q : Discuss index in both cases** (Discutez de l'indice dans les deux cas)
    
    - **R :**
        
        - **Option 1 :** L'indice `i` varie directement de 0 à 99. C'est la manière la plus courante et la plus naturelle d'itérer sur un tableau de 100 éléments en C, car les indices de tableau commencent à 0.
            
        - **Option 2 :** L'indice de boucle `i` varie de 1 à 100. Pour accéder aux éléments corrects du tableau (indices 0 à 99), on doit utiliser `i-1` à l'intérieur de la boucle. Bien que fonctionnellement correcte, cette approche est moins idiomatique en C et peut être légèrement plus source d'erreurs si l'on oublie de soustraire 1.
            
- **Q : What do we miss here?** (Que manque-t-il ici ?)
    
    - **R :** Il manque l'**initialisation** des tableaux `A` et `B`. Les tableaux sont déclarés, mais leurs éléments contiennent des valeurs indéterminées ("poubelles"). L'addition `A[i] + B[i]` utilisera ces valeurs aléatoires, et le contenu du tableau `C` sera donc également indéterminé et sans signification. Avant la boucle, il faudrait remplir les tableaux `A` et `B` avec des valeurs connues. Il manque aussi la déclaration de la variable `i` utilisée dans les boucles (par exemple `int i;`).
        

## Diapositive 49 : Plus sur la mémoire

- **Q : What is the size of the memory above assuming 1 byte per address?** (Quelle est la taille de la mémoire ci-dessus en supposant 1 octet par adresse [plage 0x00000000 à 0xFFFFFFFF] ?)
    
    - **R :** La plage d'adresses va de 0 à 232−1. Il y a donc 232 adresses uniques. Si chaque adresse correspond à 1 octet, la taille totale de la mémoire adressable est de 232 octets, soit 4 Go.
        
- **Q : You should be able by now to explain why.** (Vous devriez maintenant être capable d'expliquer pourquoi [1 octet par adresse est courant].)
    
    - **R :** L'adressage par octet (byte-addressable memory) est devenu la norme car l'octet (8 bits) est l'unité fondamentale pour représenter les caractères (par exemple, ASCII, UTF-8) et constitue une unité de base pratique pour construire des types de données plus grands (entiers, flottants). Adresser individuellement chaque octet offre la plus grande flexibilité pour manipuler des données de différentes tailles, y compris des caractères uniques ou des champs de bits au sein de structures plus larges, sans nécessiter de mécanismes complexes pour extraire des octets spécifiques d'unités adressables plus grandes (comme des mots de 32 ou 64 bits).
        

## Diapositive 52 : Considérations pratiques sur la pile (Stack)

- **Q : Assume memory of 16GB and stack of 1MB, how many nested functions calls you will be able to perform?** (Supposons une mémoire de 16 Go et une pile de 1 Mo, combien d'appels de fonctions imbriquées pourrez-vous effectuer ?)
    
    - **R :** Le nombre d'appels de fonctions imbriquées dépend de la quantité de mémoire de pile utilisée par chaque appel de fonction (taille du "stack frame"). Chaque appel de fonction pousse sur la pile : l'adresse de retour, les arguments passés à la fonction, les variables locales de la fonction, et potentiellement des registres sauvegardés. La taille de ce "frame" varie considérablement selon la fonction.
        
        - Si chaque appel de fonction utilise, disons, 100 octets en moyenne sur la pile, alors une pile de 1 Mo (1 048 576 octets) pourrait théoriquement contenir environ 1048576/100≈10485 appels imbriqués.
            
        - Si chaque appel utilise 1 Ko (1024 octets), on pourrait en faire environ 1048576/1024≈1024.
            
        - La quantité totale de mémoire système (16 Go) est largement non pertinente ici, car c'est la taille _limitée_ de la pile (1 Mo) qui détermine la profondeur maximale de la récursion ou des appels imbriqués.
            
- **Q : What will happen if we try to allocate more then what is made available by the stack definition?** (Que se passera-t-il si nous essayons d'allouer plus que ce qui est mis à disposition par la définition de la pile ?)
    
    - **R :** Si un programme tente d'utiliser plus d'espace sur la pile que ce qui lui a été alloué (par exemple, en déclarant une très grande variable locale ou par des appels de fonctions récursives trop profonds), cela provoque un **dépassement de pile (stack overflow)**. Le programme essaie d'écrire au-delà des limites de la mémoire réservée à la pile, écrasant potentiellement d'autres données importantes (comme les adresses de retour des fonctions précédentes ou des données d'autres parties du programme). Cela conduit presque invariablement à un **crash** du programme, souvent signalé par une "erreur de segmentation" (segmentation fault).
        

## Diapositive 58 : Exemple : mémoire, tableaux & types de données (3/3)

- **Q : Calculate manually and confirm with program execution the address of 11th element in each case?** (Calculez manuellement et confirmez avec l'exécution du programme l'adresse du 11ème élément dans chaque cas ?)
    
    - **R :** Le 11ème élément a l'indice 10 (car les indices commencent à 0). L'adresse de l'élément `array[10]` est calculée comme `adresse_de_base + 10 * sizeof(type_element)`. Supposons que l'adresse de base (`&array[0]`) soit `B`.
        
        - `unsigned char` (sizeof=1) : Adresse = `B + 10 * 1 = B + 10` (ou `B + 0xA` en hexadécimal).
            
        - `int` (sizeof=4) : Adresse = `B + 10 * 4 = B + 40` (ou `B + 0x28` en hexadécimal).
            
        - `long` (sizeof=8 sur ce système) : Adresse = `B + 10 * 8 = B + 80` (ou `B + 0x50` en hexadécimal).
            
        - `float` (sizeof=4) : Adresse = `B + 10 * 4 = B + 40` (ou `B + 0x28` en hexadécimal).
            
        - `double` (sizeof=8) : Adresse = `B + 10 * 8 = B + 80` (ou `B + 0x50` en hexadécimal).
            
    - L'exécution du programme (non fournie ici) confirmerait ces calculs en affichant les adresses mémoire réelles.
        
- **Q : What will happen if you modify SIZE=300000000000?** (Que se passera-t-il si vous modifiez SIZE=300000000000 ?)
    
    - **R :** `300,000,000,000` est un nombre extrêmement grand. Tenter d'allouer des tableaux de cette taille échouera très probablement.
        
        - **Échec de `malloc` :** La fonction `malloc` tentera de réserver une quantité énorme de mémoire (par exemple, pour `ptDouble`, ce serait 300×109×8 octets ≈2.4 Téraoctets par tableau !). À moins que le système n'ait une quantité extraordinaire de RAM et d'espace d'échange (swap), `malloc` retournera `NULL` car il ne pourra pas satisfaire la demande.
            
        - **Sortie du programme :** Le code vérifie si `malloc` retourne `NULL` (ligne 17) et, dans ce cas, affiche "No memory !!!" et quitte le programme (`exit(0)`). C'est ce qui se passerait.
            
        - **Dépassement d'entier (potentiel) :** Si la taille demandée à `malloc` (calculée comme `SIZE * sizeof(type)`) dépasse la capacité du type utilisé pour représenter la taille (souvent `size_t`, un entier non signé), un dépassement d'entier pourrait se produire _avant_ l'appel à `malloc`, demandant une taille incorrecte (potentiellement petite), mais étant donné la magnitude de SIZE, l'échec de l'allocation est le résultat le plus probable.
            
- **Q : What would be the max size of the array?** (Quelle serait la taille maximale du tableau ?)
    
    - **R :** La taille maximale d'un tableau alloué dynamiquement dépend de plusieurs facteurs :
        
        - **Mémoire disponible :** La quantité totale de mémoire virtuelle disponible pour le processus (RAM + espace d'échange).
            
        - **Limites du système d'exploitation :** L'OS peut imposer des limites sur la taille maximale d'une seule allocation mémoire ou sur la mémoire totale d'un processus.
            
        - **Architecture (32 vs 64 bits) :** Sur un système 32 bits, l'espace d'adressage total est limité à 4 Go, limitant intrinsèquement la taille totale des allocations. Sur un système 64 bits, la limite théorique est immense (264 octets), mais pratiquement limitée par la mémoire physique et les limites de l'OS.
            
        - **Type `size_t` :** La valeur maximale du type `size_t` (utilisé pour les tailles en C) définit la taille maximale théorique en octets d'un objet adressable (par exemple, 264−1 sur un système 64 bits).
            
    - Il n'y a pas de réponse unique, cela dépend fortement de l'environnement d'exécution.
        
- **Q : What will happen if i is int and SIZE=300000000000?** (Que se passera-t-il si i est int et SIZE=300000000000 ?)
    
    - **R :** La variable `i` est utilisée comme compteur dans la boucle `for` (ligne 21). Si `i` est déclaré comme un `int` standard (généralement 32 bits signés), sa valeur maximale est d'environ 2.14 milliards (231−1). La valeur `SIZE = 300,000,000,000` dépasse largement cette limite.
        
        - **Boucle infinie (ou comportement indéfini) :** Lorsque `i` atteindra sa valeur maximale et sera incrémenté, il subira un **dépassement d'entier signé (signed integer overflow)**. Le comportement en cas de dépassement signé est **indéfini** en C. Sur de nombreuses architectures utilisant la représentation en complément à deux, la valeur "bouclera" pour devenir négative (par exemple, 231−1+1 devient −231). La condition de la boucle `i < SIZE` resterait probablement toujours vraie (un nombre négatif est inférieur à 300 milliards), conduisant à une **boucle infinie** (en supposant que l'allocation mémoire ait réussi, ce qui est improbable comme vu précédemment).
            
- **Q : Explain why last element in unsigned char is 43?** (Expliquez pourquoi le dernier élément dans unsigned char est 43 ? [SIZE=300])
    
    - **R :** La boucle `for` (ligne 21) assigne `ptUnsChar[i] = i;`. Le type `unsigned char` ne peut stocker que des valeurs de 0 à 255. Lorsque la valeur de `i` (qui est `long`) dépasse 255, l'assignation à `ptUnsChar[i]` subit une troncature ou un "bouclage" modulo 256.
        
        - Le dernier élément est `ptUnsChar[SIZE-1]`, donc `ptUnsChar[299]`.
            
        - La valeur assignée est `i = 299`.
            
        - Pour trouver la valeur stockée dans un `unsigned char`, on calcule 299(mod256).
            
        - 299=1×256+43.
            
        - Donc, 299(mod256)=43. La valeur 43 est stockée dans le dernier élément `ptUnsChar[299]`.
            

## Diapositive 61 : Structures de données complexes (3/3)

- **Q : Explain what will happen if we enable line 15?** (Expliquez ce qui se passera si nous activons la ligne 15 [ligne 14 dans le code affiché] : `f2 = f1;`)
    
    - **R :** La ligne `f2 = f1;` copie la **valeur du pointeur** `f1` dans le pointeur `f2`.
        
        - Initialement, `f1` pointe vers une zone mémoire allouée pour une structure `fraction`, et `f2` pointe vers une _autre_ zone mémoire allouée pour une structure `fraction`.
            
        - Après `f2 = f1;`, les deux pointeurs (`f1` et `f2`) pointeront vers la **même** zone mémoire (celle initialement pointée par `f1`).
            
        - **Conséquences :**
            
            1. **Fuite mémoire :** La zone mémoire initialement allouée pour `f2` devient inaccessible (aucun pointeur ne la référence plus). Si on ne la libère pas avant l'assignation (ce qui n'est pas fait ici), c'est une fuite mémoire.
                
            2. **Modification partagée :** Toute modification faite via `f1` (par exemple `f1->numerator = 5;`) sera visible via `f2` (par exemple `f2->numerator` vaudra 5), et vice-versa, car ils pointent vers la même structure en mémoire.
                
            3. **Double libération (Double Free) :** À la fin (ligne 21), `free(f1); free(f2);` tentera de libérer deux fois la _même_ zone mémoire (puisque `f1` et `f2` pointent au même endroit après l'assignation). Tenter de libérer une zone mémoire déjà libérée est une erreur grave (comportement indéfini, souvent un crash).
                

## Diapositive 63 : Ouvrir un fichier en lecture

- **Q : What is missing & what happens during execution?** (Qu'est-ce qui manque & que se passe-t-il pendant l'exécution ?)
    
    - **R :**
        
        - **Ce qui manque :**
            
            1. **Déclaration/Allocation de `source` :** La variable `source` (utilisée comme buffer de destination pour `fread`) n'est ni déclarée ni allouée. `fread` essaiera d'écrire dans une zone mémoire invalide.
                
            2. **Déclaration/Initialisation de `size` :** La variable `size` (indiquant combien d'éléments lire) n'est ni déclarée ni initialisée. `fread` lira un nombre indéterminé (ou zéro) d'éléments.
                
            3. **Inclusion de `<stdlib.h>` :** Si `exit()` est utilisé, l'en-tête `<stdlib.h>` devrait être inclus.
                
        - **Ce qui se passe à l'exécution :**
            
            1. Le programme tente d'ouvrir le fichier "data.test" en mode lecture (`"r"`).
                
            2. **Si le fichier n'existe pas ou ne peut pas être ouvert :** `fopen` retourne `NULL`. La condition `fp!=NULL` (ligne 4) est fausse. Le bloc `else` est exécuté : "Can’t open specified file!" est affiché, et le programme se termine (`exit(0)`).
                
            3. **Si le fichier est ouvert avec succès :** `fopen` retourne un pointeur valide. La condition `fp!=NULL` est vraie. "File opened ... " est affiché.
                
            4. `fread(source, sizeof(unsigned char), size, fp);` est appelé. Comme `source` et `size` ne sont pas valides, le comportement est **indéfini**. Très probablement, cela causera une **erreur de segmentation (crash)** car `fread` tentera d'écrire à une adresse invalide (`source`) ou lira une taille invalide (`size`).
                
            5. Si par miracle `fread` ne crashe pas (par exemple si `size` vaut 0 par hasard), "File read! \n" serait affiché, puis `fclose(fp);` fermerait le fichier.
                

## Diapositive 68 : Vérifier si une chaîne est une voyelle

- **Q : How to do this better?** (Comment faire cela mieux ?)
    
    - **R :** La longue condition `if` avec de multiples `||` est lisible mais un peu répétitive. On pourrait améliorer de plusieurs façons :
        
        1. **`switch` statement :** Utiliser un `switch` sur le caractère, en regroupant les cas pour les voyelles majuscules et minuscules.
            
            ```
            switch (ch) {
                case 'a': case 'A':
                case 'e': case 'E':
                case 'i': case 'I':
                case 'o': case 'O':
                case 'u': case 'U':
                    printf("%c is a vowel.\n", ch);
                    break;
                default:
                    printf("%c is not a vowel.\n", ch);
            }
            ```
            
        2. **Fonction `strchr` :** Convertir le caractère en minuscule (avec `tolower` de `<ctype.h>`) puis chercher s'il appartient à une chaîne contenant les voyelles.
            
            ```
            #include <ctype.h>
            #include <string.h>
            // ...
            char lower_ch = tolower(ch);
            if (strchr("aeiou", lower_ch) != NULL) {
                printf("%c is a vowel.\n", ch);
            } else {
                printf("%c is not a vowel.\n", ch);
            }
            ```
            
        3. **Tableau de consultation (Lookup Table) :** Pour des vérifications très rapides, on pourrait utiliser un tableau booléen indexé par le code ASCII du caractère.
            

## Diapositive 69 : Imprimer un nombre décimal en binaire

- **Q : What needs to be changed if n was an unsigned char?** (Que faut-il changer si n était un unsigned char ?)
    
    - **R :** Un `unsigned char` a généralement 8 bits.
        
        1. **Limite de la boucle :** La boucle `for` actuelle va de `c = 31` à `0`, ce qui est adapté pour un entier de 32 bits. Pour un `unsigned char` de 8 bits, la boucle devrait aller de `c = 7` à `0`.
            
            ```
            for (c = 7; c >= 0; c--)
            ```
            
        2. **Type de `n` :** La variable `n` devrait être déclarée comme `unsigned char`.
            
            ```
            unsigned char n;
            // ...
            scanf("%hhu", &n); // Utiliser %hhu pour lire un unsigned char
            ```
            
        3. **Type de `k` :** La variable `k` utilisée pour le décalage peut rester `int`, car le résultat du décalage sera promu en `int`.
            

## Diapositive 70 : Fonctions récursives

- **Q : Explain why this is not that good?** (Expliquez pourquoi ce n'est pas si bien [la nécessité d'un if/else en récursion] ?)
    
    - **R :** La nécessité absolue d'une condition d'arrêt (`if/else` ou similaire) dans une fonction récursive n'est pas "mauvaise" en soi, c'est fondamental pour éviter une récursion infinie. La diapositive semble plutôt critiquer la récursion en général par rapport aux boucles. Les inconvénients potentiels de la récursion (auxquels la diapositive fait allusion) sont :
        
        - **Consommation de pile :** Chaque appel récursif consomme de l'espace sur la pile (pour l'adresse de retour, les variables locales, etc.). Une récursion très profonde peut entraîner un dépassement de pile (stack overflow).
            
        - **Performance :** Le surcoût lié aux appels de fonction (sauvegarde du contexte, saut, restauration) peut rendre une solution récursive plus lente qu'une solution itérative équivalente utilisant des boucles, surtout si la fonction est appelée très souvent.
            
        - **Lisibilité (parfois) :** Bien que la récursion puisse être élégante pour certains problèmes (par ex., parcours d'arbres), elle peut être plus difficile à suivre et à déboguer pour d'autres par rapport à une boucle explicite.
            
- **Q : You should know why** (Vous devriez savoir pourquoi [la récursion a une pénalité de performance])
    
    - **R :** Comme mentionné ci-dessus, la pénalité de performance de la récursion provient principalement du **surcoût des appels de fonction**. Chaque appel implique :
        
        - De sauvegarder l'état actuel (registres, adresse de retour) sur la pile.
            
        - De passer les arguments (potentiellement via la pile).
            
        - D'allouer de l'espace pour les variables locales sur la pile.
            
        - D'effectuer un saut vers le code de la fonction.
            
        - À la fin de l'appel, de restaurer l'état sauvegardé depuis la pile et de revenir à l'appelant.
            
    - Une boucle simple évite la plupart de ces opérations répétées, n'effectuant que des sauts conditionnels et des incrémentations de compteurs, ce qui est généralement plus rapide. (Note : Certains compilateurs peuvent optimiser la récursion terminale - tail recursion - en la transformant en boucle, éliminant ce surcoût).
        

## Diapositive 71 : Puissance d'un nombre avec fonction récursive

- **Q : What prevents infinite recursion in the code above?** (Qu'est-ce qui empêche la récursion infinie dans le code ci-dessus ?)
    
    - **R :** La condition d'arrêt (`base case`) de la récursion est la clause `else` (ligne 22-23) : `else return 1;`. Cette condition est atteinte lorsque l'argument `powerRaised` devient 0. Chaque appel récursif (ligne 21) se fait avec `powerRaised-1`. Comme `powerRaised` est supposé être un entier positif (selon le `printf` et `scanf`), il finira par atteindre 0 après un nombre fini d'appels, déclenchant le cas de base qui retourne 1 sans faire de nouvel appel récursif, mettant ainsi fin à la chaîne d'appels.
        

## Diapositive 73 : Vue d'ensemble (Assembleur)

- **Q : Can you explain decoding at circuit level?** (Pouvez-vous expliquer le décodage au niveau circuit ?)
    
    - **R :** Au niveau circuit, le décodage d'instruction (opcode) est réalisé par une unité de contrôle (Control Unit) au sein du CPU. L'opcode binaire, récupéré de la mémoire, est envoyé à un circuit logique combinatoire (le décodeur d'instruction). Ce décodeur interprète les bits de l'opcode pour générer une série de signaux de contrôle spécifiques. Ces signaux activent ou désactivent différentes parties du CPU (comme l'ALU, les registres, les bus de données, les unités de mémoire) dans une séquence précise pour exécuter l'opération demandée par l'instruction (par exemple, lire des opérandes depuis les registres, effectuer une addition dans l'ALU, écrire le résultat dans un registre). Dans les CPU modernes (CISC), cela peut impliquer une étape de microprogrammation où l'opcode est traduit en une séquence d'opérations plus simples (micro-instructions).
        
- **Q : You should be able to explain why** (Vous devriez être capable d'expliquer pourquoi [assembleur n'est pas la même chose que compilateur])
    
    - **R :** La différence principale réside dans le niveau d'abstraction de la traduction :
        
        - **Assembleur :** Traduit un langage d'assemblage (mnémoniques comme `MOV`, `ADD`) en code machine binaire (opcodes). La correspondance est généralement directe : une instruction assembleur correspond à une instruction machine. L'assembleur travaille à un niveau très bas, proche du matériel.
            
        - **Compilateur :** Traduit un langage de haut niveau (comme C, C++, Java) en un langage de plus bas niveau (souvent en langage d'assemblage ou directement en code machine/bytecode). Une seule instruction de haut niveau peut être traduite en de nombreuses instructions machine. Le compilateur effectue des analyses complexes, des optimisations, et gère des abstractions comme les variables, les types, les fonctions, les structures de contrôle, qui n'existent pas directement au niveau machine.
            

## Diapositive 80 : Fichier de registres dans les architectures x86

- **Q : How do you explain this?** (Comment expliquez-vous cela [les différentes tailles de registres dans le diagramme] ?)
    
    - **R :** Le diagramme illustre la **compatibilité ascendante** des architectures x86. Les registres ont évolué au fil des générations de processeurs, mais les nouvelles architectures ont conservé la capacité d'accéder aux parties plus petites des registres plus grands pour assurer la compatibilité avec le code plus ancien :
        
        - **8 bits (`*L`, `*H`) :** Viennent des processeurs 8 bits originaux (comme le 8080/8086). `AL`, `AH`, `BL`, `BH`, etc., permettent d'accéder aux octets bas et haut des registres 16 bits.
            
        - **16 bits (`*X`) :** Viennent des processeurs 16 bits (8086/80286). `AX`, `BX`, `CX`, `DX` sont les registres généraux 16 bits.
            
        - **32 bits (`E*X`) :** Introduits avec les processeurs 32 bits (IA-32, comme le 80386). `EAX`, `EBX`, etc., sont les extensions 32 bits des registres 16 bits. Accéder à `AX` accède aux 16 bits inférieurs de `EAX`.
            
        - **64 bits (`R*X`) :** Introduits avec les processeurs 64 bits (x86-64). `RAX`, `RBX`, etc., sont les extensions 64 bits. Accéder à `EAX` accède aux 32 bits inférieurs de `RAX`. De nouveaux registres 64 bits (R8-R15) ont également été ajoutés.
            
    - Cette superposition permet au code écrit pour des architectures plus anciennes de s'exécuter sur des processeurs plus récents sans modification, en accédant simplement aux sous-parties appropriées des registres plus grands.
        

## Diapositive 84 : Modes d'adressage (2/2)

- **Q : In case we modify the address, what do we need to verify?** (Au cas où nous modifions l'adresse, que devons-nous vérifier ?)
    
    - **R :** Lorsque l'on modifie une adresse utilisée pour l'adressage indirect (comme dans l'exemple avec `ebx`), il est crucial de vérifier que la **nouvelle adresse calculée reste dans les limites allouées** pour la structure de données (le tableau `my_table` dans l'exemple). Si l'adresse modifiée pointe en dehors des limites du tableau, une lecture ou (pire) une écriture à cette adresse entraînera un accès mémoire invalide (out-of-bounds), avec les risques de corruption de données ou de crash du programme décrits précédemment. Il faut s'assurer que l'offset ajouté ou l'adresse calculée ne dépasse pas la taille de la zone mémoire réservée.
        

## Diapositive 87 : Boucles (2/2)

- **Q : Interpret the code example below:** (Interprétez l'exemple de code ci-dessous :)
    
    - **R :** Ce code utilise l'instruction `LOOPNE` (Loop if Not Equal) pour répéter un bloc de code tant que deux conditions sont remplies : le compteur `CX` n'est pas zéro ET le Zero Flag (ZF) du registre de drapeaux est à 0 (indiquant que la comparaison précédente n'a pas résulté en égalité).
        
        1. `mov cx, 256` : Le registre compteur `CX` est initialisé à 256. Cela limite le nombre maximal d'itérations à 256.
            
        2. `wend:` : C'est l'étiquette marquant le début de la boucle.
            
        3. `... ; Some statements that change AX` : Le corps de la boucle contient des instructions (non montrées) qui sont supposées modifier le contenu du registre `AX` (ou au moins `AL`, sa partie basse).
            
        4. `cmp al, 'Y'` : Le contenu du registre `AL` est comparé à la valeur ASCII du caractère 'Y'. Cette comparaison met à jour le Zero Flag (ZF) : ZF=1 si `AL == 'Y'`, ZF=0 sinon.
            
        5. `loopne wend` : C'est l'instruction de boucle conditionnelle. Elle fait deux choses :
            
            - Décrémente `CX`.
                
            - Vérifie si `CX != 0` ET si `ZF == 0` (c'est-à-dire si `AL != 'Y'`).
                
            - Si les deux conditions sont vraies, le programme saute à l'étiquette `wend` pour une nouvelle itération.
                
            - Si l'une des conditions est fausse (`CX` atteint 0 OU `AL` est égal à 'Y'), la boucle se termine et l'exécution continue après l'instruction `loopne`.
                
        
        - En résumé : la boucle s'exécute au maximum 256 fois, mais s'arrête plus tôt si le registre `AL` contient la valeur 'Y' après l'exécution du corps de la boucle.