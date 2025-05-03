
# Réponses aux Questions des Diapositives (ELEC-H-473 Th03)

Voici les réponses aux questions identifiées dans les diapositives du document `ELECH473_Th03.pdf`.

## Diapositive 4 : Qu'est-ce qui définit vraiment un ordinateur ?

- **Q : What is specific to electronic calculators?** (Qu'est-ce qui est spécifique aux calculatrices électroniques ?)
    
    - **R :** La diapositive définit l'ordinateur par opposition à la calculatrice électronique en indiquant que les ordinateurs stockent électroniquement l'information qui contrôle le processus de calcul (c'est-à-dire le programme). Par déduction, ce qui est spécifique aux calculatrices électroniques (selon ce contexte) est qu'elles **ne stockent pas** électroniquement le programme ou les instructions qui contrôlent leurs calculs de la même manière flexible qu'un ordinateur. Leurs opérations sont généralement plus figées ou moins programmables au sens large.
        

## Diapositive 5 : Architecture Von Neumann : Vue HW

- **Q : What is the consequence of it?** (Quelle en est la conséquence [du fait que la mémoire stocke à la fois les instructions et les données] ?)
    
    - **R :** La conséquence principale du stockage unifié des instructions et des données dans la même mémoire est que les **instructions peuvent être traitées comme des données**. Cela permet potentiellement la compilation à l'exécution (run-time compilation) et même l'écriture de code auto-modifiable (bien que ce dernier soit qualifié d'exotique). Cela simplifie également le contrôle matériel (HW) car il n'y a qu'un seul système de mémoire à gérer.
        

## Diapositive 9 : Mémoire système principale

- **Q : Accidental modifications of program data will most likely cause the system to crash; Can you explain why?** (Les modifications accidentelles des données du programme provoqueront très probablement le crash du système ; Pouvez-vous expliquer pourquoi ?)
    
    - **R :** La diapositive n'explique pas _directement_ pourquoi cela provoque un crash, mais on peut le déduire du contexte général des cours précédents et de celui-ci. Si les données d'un programme sont modifiées accidentellement (par exemple, par un accès mémoire erroné), cela peut corrompre :
        
        - **Les variables critiques :** Des variables utilisées pour les calculs, les pointeurs, les compteurs de boucle, etc., peuvent prendre des valeurs incorrectes, menant à des opérations erronées ou des accès mémoire invalides.
            
        - **Les structures de contrôle :** Des données utilisées pour contrôler le flux du programme (par exemple, des indicateurs d'état, des adresses de saut) peuvent être altérées, entraînant des sauts vers des emplacements incorrects ou des boucles infinies.
            
        - **Les données de la pile :** Si la corruption affecte la pile d'exécution, des adresses de retour de fonction ou des variables locales peuvent être écrasées, provoquant un crash lors du retour d'une fonction ou l'utilisation de données corrompues.
            
    - Essentiellement, la corruption des données conduit à un état incohérent du programme, rendant une exécution correcte impossible et menant souvent à une tentative d'opération illégale détectée par le matériel ou le système d'exploitation, résultant en un crash (par exemple, une erreur de segmentation).
        

## Diapositive 26 : B. Décodage d'instruction

- **Q : In the simplest form this is just a x : y decoder (What are x, y?)** (Dans la forme la plus simple, c'est juste un décodeur x : y (Que sont x, y ?))
    
    - **R :** Dans le contexte d'un décodeur d'instruction, le circuit prend l'opcode en entrée et génère des signaux de contrôle en sortie. Si l'opcode a x bits, il peut représenter 2x instructions différentes. Le décodeur génère y signaux de contrôle distincts nécessaires pour piloter les différentes unités fonctionnelles (ALU, RF, MUX, etc.) afin d'exécuter l'instruction. Donc :
        
        - **x :** Est le nombre de bits de l'opcode (l'entrée du décodeur).
            
        - **y :** Est le nombre de signaux de contrôle générés (la sortie du décodeur). Le nombre y n'est pas directement 2x, mais dépend de la complexité du contrôle requis par l'architecture. Un décodeur x-vers-2x pourrait être une partie du système de décodage global, mais le nombre total de signaux de contrôle y est généralement différent.
            

## Diapositive 29 : Différentes architectures selon les R/W mémoire

- **Q : According to you, what these differences really mean, and what are the trade-offs?** (Selon vous, que signifient réellement ces différences et quels sont les compromis [entre architectures Load/Store, Register/Memory, Register+Memory] ?)
    
    - **R :** Ces différences définissent où les instructions peuvent trouver leurs opérandes et où elles peuvent écrire leurs résultats.
        
        - **Load/Store (ex: RISC typique) :**
            
            - **Signification :** Les opérations de calcul (comme ADD, MUL) ne peuvent travailler _que_ sur des données présentes dans les registres (RF). Pour traiter une donnée en mémoire, il faut explicitement la charger dans un registre (`LOAD`), effectuer l'opération, puis explicitement la stocker de nouveau en mémoire si nécessaire (`STORE`).
                
            - **Compromis :**
                
                - **Avantages :** Simplifie la conception du CPU (l'ALU n'a besoin d'accéder qu'aux registres rapides), facilite le pipelining car les étapes d'exécution ont des accès prévisibles et rapides. Peut conduire à une fréquence d'horloge plus élevée.
                    
                - **Inconvénients :** Augmente le nombre d'instructions nécessaires pour une tâche donnée (plus de `LOAD`/`STORE`), ce qui peut augmenter la taille du code et potentiellement le temps d'exécution si les accès mémoire sont fréquents.
                    
        - **Register/Memory & Register+Memory (ex: CISC typique) :**
            
            - **Signification :** Les instructions de calcul peuvent accéder directement à des opérandes en mémoire, ou combiner un opérande en registre et un en mémoire (Register+Memory étant le cas le plus général).
                
            - **Compromis :**
                
                - **Avantages :** Réduit le nombre d'instructions nécessaires pour une tâche (moins de `LOAD`/`STORE` explicites), peut conduire à un code plus compact. Peut être plus pratique pour le programmeur assembleur ou le compilateur dans certains cas.
                    
                - **Inconvénients :** Complexifie la conception du CPU (l'unité d'exécution doit gérer les accès mémoire, qui sont lents et de durée variable), rend le pipelining beaucoup plus difficile car la durée de l'étape d'exécution devient imprévisible. Peut limiter la fréquence d'horloge maximale.
                    

## Diapositive 32 : Classes d'instructions CPU

- **Q : How do we encode floating point / integer?** (Comment encodons-nous les flottants / entiers ?)
    
    - **R :** La diapositive ne détaille pas les encodages, mais mentionne que les types de données ont des encodages différents. Les encodages standards sont :
        
        - **Entiers (Integers) :** Généralement encodés en **complément à deux** pour les entiers signés, permettant de représenter des nombres positifs et négatifs avec des opérations arithmétiques simples. Les entiers non signés utilisent une représentation binaire directe. La taille (nombre de bits : 8, 16, 32, 64) détermine la plage de valeurs.
            
        - **Flottants (Floating Point) :** Généralement encodés selon la **norme IEEE 754**. Cette norme définit des formats (simple précision - 32 bits, double précision - 64 bits) qui divisent les bits entre un signe, un exposant (qui détermine la magnitude) et une mantisse ou significande (qui détermine la précision). Cela permet de représenter une très large gamme de valeurs, y compris des nombres très petits et très grands, ainsi que des fractions.
            

## Diapositive 35 : Classification des jeux d'instructions

- **Q : Can you guess why?** (Pouvez-vous deviner pourquoi [les fabricants CISC font leur propre silicium] ?)
    
    - **R :** Les architectures CISC sont intrinsèquement plus complexes à concevoir et à fabriquer en matériel (HW) en raison de leur jeu d'instructions étendu et de la complexité des instructions individuelles (qui peuvent prendre plusieurs cycles d'horloge et nécessiter une logique de contrôle sophistiquée, voire du microcode). Les entreprises qui développent des CPU CISC (historiquement comme Intel, AMD) investissent massivement dans la conception de circuits très complexes et dans les technologies de fabrication de pointe (leur propre "silicium" ou des partenariats très étroits avec des fondeurs). Cet investissement lourd est plus facile à justifier et à contrôler en interne, et la complexité de la conception crée une barrière à l'entrée plus élevée, favorisant les entreprises capables de maîtriser l'ensemble du processus, de l'architecture à la fabrication. Les architectures RISC, étant plus simples, sont plus faciles à concevoir et à licencier, ce qui explique leur adoption plus large par des entreprises qui ne possèdent pas nécessairement leurs propres usines de fabrication (modèle "fabless").
        

## Diapositive 60 : Analogie avec le CPU (Chaîne de montage Ford)

- **Q : Explain the concept of latency in this picture** (Expliquez le concept de latence dans cette image)
    
    - **R :** Dans l'analogie de la chaîne de montage (pipeline), la **latence** correspond au **temps total** nécessaire pour qu'**une seule pièce** (ou une voiture) traverse **toutes les étapes** de la chaîne, depuis son entrée à la première étape (Fetch) jusqu'à sa sortie de la dernière étape (Write). Même si une nouvelle voiture sort de la chaîne à chaque intervalle de temps une fois la chaîne pleine (haut débit/throughput), le temps passé par _chaque_ voiture individuelle sur la chaîne reste constant et correspond à la somme des temps de chaque poste de travail. De même, dans un pipeline CPU, la latence d'une instruction est le nombre total de cycles d'horloge nécessaires pour que cette instruction spécifique passe par toutes les étapes du pipeline (F, D, Ex, W), même si une instruction différente termine son exécution à chaque cycle une fois le pipeline rempli.
        

## Diapositive 64 : Pipeline & le modèle d'exécution (2/2)

- **Q : Why loops with many iterations?** (Pourquoi les boucles avec de nombreuses itérations ?)
    
    - **R :** Il est particulièrement important de vérifier l'utilisation du pipeline dans les boucles avec de nombreuses itérations car ce sont ces sections de code qui dominent souvent le temps d'exécution total d'un programme. Une mauvaise utilisation du pipeline (causée par des dépendances de données, des sauts conditionnels mal prédits, etc.) à l'intérieur d'une boucle qui s'exécute des milliers ou des millions de fois aura un impact significatif sur la performance globale. Optimiser le remplissage du pipeline dans ces boucles critiques est donc essentiel pour obtenir de bonnes performances. Les phases d'initialisation ou de finalisation du programme, ou les sections de code exécutées rarement, ont un impact moindre, même si leur pipeline n'est pas optimal.