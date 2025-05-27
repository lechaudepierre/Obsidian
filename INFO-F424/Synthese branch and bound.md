# Résumé : Recherche Opérationnelle - Chap. III: Optimisation en Nombres Entiers

Ce document aborde l'optimisation en nombres entiers, une branche de la recherche opérationnelle. Il se divise en deux grandes parties : les heuristiques et les méthodes exactes pour résoudre les problèmes en nombres entiers.

## 1. Heuristiques

Les heuristiques sont des méthodes qui visent à trouver rapidement une "bonne" solution, sans garantir l'optimalité.

### 1.1. Heuristiques pour le Problème du Sac à Dos (Knapsack)

Le problème du sac à dos consiste à sélectionner un sous-ensemble d'objets, chacun ayant un poids et une valeur, de manière à maximiser la valeur totale sans dépasser une capacité de poids donnée.

* **Exemple Numérique :** Illustration avec une capacité $b=3$ et trois objets avec différents coûts ($c_j$) et poids ($a_j$).
* **Relaxation Linéaire (LP) & Arrondi :**
    * On résout d'abord le problème en autorisant des quantités fractionnaires d'objets (relaxation LP).
    * La solution obtenue est ensuite arrondie à l'entier le plus proche.
    * Exemple : Solution LP $\overline{x}=(1, 2/3, 0)$ avec objectif $20\alpha$. Solution arrondie $\dot{x}=(1,0,0)$ avec objectif $10\alpha$. L'arrondi peut être inférieur ou supérieur.
* **Approche Gloutonne (Greedy) :**
    * Les objets sont triés (généralement par rapport $c_j/a_j$).
    * On essaie d'ajouter les objets en entier dans cet ordre tant que la capacité le permet.
    * Exemple : Solution gloutonne $\hat{x}=(1,0,1)$ avec objectif $14\alpha$.
* **Comparaison des Solutions :**
    * Solution arrondie : $10\alpha$
    * Solution gloutonne : $14\alpha$
    * Solution optimale entière : $x^{*}=(0,1,0)$ avec objectif $15\alpha$
    * Solution de la relaxation LP : $20\alpha$

### 1.2. Heuristique d'Arrondi Généralisée

Pour un problème en nombres entiers général $min_{x\in\mathbb{Z}^{n}}c^{\top}x$ s.t. $Ax\ge b$:
1.  Résoudre la relaxation linéaire : $min_{x\in\mathbb{R}^{n}}c^{\top}x$ s.t. $Ax\ge b$ pour obtenir $\overline{x}$.
2.  Arrondir $\overline{x}$ pour obtenir une solution entière $\tilde{x}$.

* **Problèmes de l'Arrondi :**
    * Comment arrondir si $\overline{x}_j$ a une partie fractionnaire de 0.5 ?
    * La solution arrondie est-elle toujours réalisable ? (Souvent non, comme illustré par des exemples graphiques).
    * Peut-on garantir la qualité de la solution arrondie ? (Souvent non, la solution arrondie peut être loin de l'optimum entier).
* **Problèmes "Flexibles" où l'Arrondi Fonctionne Bien :**
    * Knapsack, Set Covering, Bin Packing, Facility Location, Open Pit Mining Scheduling.
    * Généralement pour les problèmes où il est facile de trouver des solutions réalisables manuellement et qui ont des formulations PL en nombres entiers de taille raisonnable.
* **Exemple : Facility Location (Localisation d'Installations) :**
    * **Problème :** Minimiser les coûts d'ouverture de dépôts et les coûts de fourniture aux clients, en respectant les demandes des clients et les capacités des dépôts.
        $min_{x,y}\sum_{i=1}^{n}f_{i}y_{i}+\sum_{i=1}^{n}\sum_{j=1}^{m}c_{ij}x_{ij}$
        s.t. $\sum_{i=1}^{n}x_{ij}=d_{j}, \forall j$; $\sum_{j=1}^{m}x_{ij}\le S_{i}y_{i}, \forall i$; $y\in\{0,1\}^{n},x\ge0$.
    * **Idées d'Heuristiques :**
        1.  Arrondir la solution LP pour obtenir les dépôts ouverts ($\tilde{y}$).
        2.  Assigner les clients de manière gloutonne pour obtenir les flux ($x$).

### 1.3. Heuristiques de Recherche Locale (Local Search)

Ces algorithmes visent à améliorer une solution existante via des mouvements simples et peu coûteux, appliqués de manière répétée.

* **Exemple : Problème du Voyageur de Commerce (TSP) & Routage de Véhicules (VRP) :**
    * **VRP :** Étant donné $n$ clients avec des demandes $d_j$, un dépôt, et des véhicules avec une capacité $K_v$. Quels véhicules utiliser et par où les faire passer ?
* **Mouvements de Recherche Locale pour TSP/VRP :**
    * **2-opt :** Choisir 2 arêtes et échanger leurs nœuds de tête. $O(n^2)$ possibilités.
    * **k-opt (Lin-Kernighan '73) :** Choisir $k$ arêtes et échanger leurs nœuds de tête. $O(n^k)$ ou $O(k!)$ possibilités. En pratique, $k=2$ ou $3$.
    * **Or-opt (Or '76) :** Choisir deux routes $R_1, R_2$. Choisir un point d'insertion dans $R_1$ et une séquence de $k$ nœuds dans $R_2$. Insérer la séquence dans $R_1$ et refermer $R_2$. $O(n(n-k)^2)$ possibilités.
    * **Autres mouvements pour VRP :** $\lambda$-interchange (Osman '93), Unstring-String (Gendreau et al. '92), Generalized Insertion Procedure (Gendreau et al. '92).
    * **Méta-heuristiques :** Combinaisons de ces mouvements (Early Tabu search, Simulated Annealing, Tabu Search, GENI).
* **Solutions Initiales pour VRP :**
    * **Clarke-Wright ('64) :** Commencer avec une route par nœud, puis fusionner les routes en se basant sur les économies réalisées.
    * **Sweep (Groer '10) :** Pour chaque véhicule, ajouter les clients à mesure qu'un "radar" les atteint, passer au véhicule suivant si la capacité est dépassée.
* **Recherche Locale pour d'Autres Problèmes :**
    * **Knapsack Entier :**
        1.  INITIALISATION : Trouver la solution gloutonne.
        2.  DESTRUCTION : Enlever des objets.
        3.  RÉPARATION : Remettre des objets.
    * **Facility Location :**
        1.  INITIALISATION : Résoudre la relaxation LP et l'arrondir.
        2.  DESTRUCTION : Fermer des dépôts.
        3.  RÉPARATION : Rouvrir des dépôts.
* **Large Neighborhood Search (LNS) :**
    * **Concept :** Trouver une solution initiale. Répéter : briser la solution courante (destruction), puis réparer la solution endommagée (réparation).
    * Permet de dégrader la valeur pour sortir d'optima locaux (similaire au recuit simulé).
    * [Image de Fonction Objective avec Optima Locaux et Global]
* **Variable Neighborhood Search (VNS) / Variable Depth Neighborhood Search (VDNS) :**
    * Utilisation de différents types de voisinages (diversification, intensification).
    * **VNS :** Explore différents voisinages $N_1, N_2, ..., N_k$ à partir de la solution courante.
    * **VDNS :** Explore séquentiellement des voisinages de plus en plus "profonds".
* **Choix des Voisinages (dans LNS/VNS) :**
    * Favoriser les voisinages qui ont récemment amélioré la solution (meilleure globale, locale, ou acceptée).
    * Défavoriser ceux dont la solution n'a pas été retenue.
    * Ajuster les probabilités de sélection des mouvements $p_l$ après chaque utilisation.
* **Framework LNS :**
    1.  Initialiser $\overline{x}=x$, $p^{+}$, $p^{-}$.
    2.  Répéter :
        a.  Sélectionner `destroy` $d$ et `repair` $r$ selon $p^{-}, p^{+}$.
        b.  $\hat{x}=r(d(x))$.
        c.  Si `accept`($\hat{x},x$), alors $x=\hat{x}$.
        d.  Si $obj(\hat{x})<obj(\overline{x})$, alors $\overline{x}=\hat{x}$.
        e.  Actualiser $p^{-}, p^{+}$.
    3.  Jusqu'à critère d'arrêt.
    4.  Retourner $\overline{x}$.
* **Efficacité et Calibration :**
    * Simples à implémenter, rapides, donnent des solutions raisonnables à excellentes.
    * Parfois la seule option.
    * Calibration ("fine tuning") cruciale et peut être complexe.

## 2. Résoudre des Problèmes en Nombres Entiers (Méthodes Exactes)

### 2.1. Dual Lagrangien = Dual LP

* **Meilleure Borne Duale du Knapsack Entier :**
    * Problème primal : $\omega:=max_{x\in\{0,1\}^{n}}\{c^{\top}x:a^{\top}x\le b\}$.
    * En relaxant la contrainte de capacité dans l'objectif avec un multiplicateur $\lambda_0 \ge 0$:
        $\omega(\lambda_{0}):=max_{x\in\{0,1\}^{n}}\{\sum c_{j}x_{j}+\lambda_{0}(b-\sum a_{j}x_{j})\}$.
        Ceci est égal à $\lambda_{0}b+max_{x\in\{0,1\}^{n}}\sum (c_{j}-\lambda_{0}a_{j})x_{j}$.
    * Remplacer $x\in\{0,1\}^{n}$ par $x\in[0,1]^{n}$ ne change rien pour ce problème relaxé.
    * La meilleure borne duale $\omega_{D}:=min_{\lambda_{0}\ge0}\omega(\lambda_{0})$ est égale à la valeur optimale du dual de la relaxation linéaire du problème du sac à dos.
* **PL Générique en Nombres Entiers :**
    * Forme standard : $\omega:=min_{x\in\{0,1\}^{n}}c^{\top}x$ s.t. $Ax\ge b$.
* **Problème Dual :**
    * La meilleure borne duale (plus grande) $\omega_D$ vient du PL : $max_{\lambda,\pi\ge0} \lambda^{\top}b-e^{\top}\pi$ s.t. $A^{\top}\lambda-\pi\le c$.
    * En re-dualisant ce problème, on retombe sur la relaxation linéaire du problème entier : $min_{x\in[0,1]^{n}}c^{\top}x$ s.t. $Ax\ge b$.
* **Relaxation Linéaire :**
    * Dual du problème entier = dual de sa relaxation linéaire.
    * Généralement, pas de dualité forte pour les problèmes en nombres entiers (i.e., $\omega_D < \omega$).
    * Pas de simplexe directement applicable au problème entier.
    * MAIS, $\omega_D$ fournit une borne (inférieure pour minimisation, supérieure pour maximisation) et peut guider les heuristiques.

### 2.2. Énumération

* **Énumération "Force Brute" :** Tester toutes les $2^n$ combinaisons pour $n$ variables binaires. Coûteux.
* **Énumération Intelligente :**
    * Y a-t-il des zones de l'espace de recherche qui ne valent pas la peine d'être explorées ?
    * L'ordre d'énumération importe-t-il ?
    * Si, après avoir fixé une variable, le sous-problème devient irréalisable ou si une solution entière est trouvée, on peut "élaguer" (prune) cette branche de l'arbre d'énumération.

### 2.3. Diviser pour Régner : Branch & Bound (Séparation et Évaluation)

Méthode exacte pour résoudre les problèmes d'optimisation en nombres entiers.

* **Exemple Numérique (Presses et Tours) :**
    * Une entreprise veut maximiser son bénéfice en achetant des presses ($x_1$) et des tours ($x_2$) avec un budget et un espace limités.
        $max \ 100x_{1}+150x_{2}$
        s.t. $8000x_{1}+4000x_{2}\le40000$ (budget)
             $15x_{1}+30x_{2}\le200$ (espace)
             $x_{1},x_{2}\ge0$ et entiers.
    * **Solution de la Relaxation Linéaire :** $(x_1, x_2) = (20/9, 50/9) \approx (2.22, 5.56)$, objectif $\approx 1055.55$.
    * **Brancher (Diviser) :** Puisque $x_2 = 5.56$ n'est pas entier, on crée deux sous-problèmes :
        1.  Sous-problème 1 : Contrainte additionnelle $x_2 \le 5$.
        2.  Sous-problème 2 : Contrainte additionnelle $x_2 \ge 6$.
    * On résout la relaxation linéaire pour chaque sous-problème. Si une solution est fractionnaire, on branche à nouveau sur une variable fractionnaire.
    * **Arbre de Branchage :** Visualisation du processus de division.
        * Nœud Racine : Relaxation LP initiale, $z^*=1055.56$, $(x_1,x_2)=(2.22,5.56)$.
        * Branche $x_2 \le 5$: $z^*=1000$, $(x_1,x_2)=(2.5,5)$. On branche sur $x_1$.
            * $x_1 \le 2$ ($+ x_2 \le 5$): $z^*=950$, $(x_1,x_2)=(2,5)$ -> **Solution entière, borne inférieure candidate.**
            * $x_1 \ge 3$ ($+ x_2 \le 5$): $z^*=900$, $(x_1,x_2)=(3,4)$ -> **Solution entière.** (Moins bonne que 950).
        * Branche $x_2 \ge 6$: $z^*=1033.33$, $(x_1,x_2)=(1.33,6)$. On branche sur $x_1$.
            * $x_1 \le 1$ ($+ x_2 \ge 6$): $z^*=1025$, $(x_1,x_2)=(1, 6.17)$. On branche sur $x_2$.
                * $x_2 \le 6$ ($+ x_1 \le 1, x_2 \ge 6 \implies x_2=6$): $z^*=1000$, $(x_1,x_2)=(1,6)$ -> **Solution entière, nouvelle meilleure borne inférieure (1000).**
                * $x_2 \ge 7$ ($+ x_1 \le 1, x_2 \ge 6$): Pas de solution réalisable (élagage).
            * $x_1 \ge 2$ ($+ x_2 \ge 6$): Pas de solution réalisable (élagage).
    * La meilleure solution entière trouvée est $(1,6)$ avec un objectif de $1000$.
* **Créateurs :** Alison G. Harcourt (née Doig) et Ailsa H. Land (née Dicken) en 1960.
* **Concept Général :**
    * On explore un arbre de sous-problèmes.
    * **Borne Primale (Lower Bound pour max, Upper Bound pour min) :** Meilleur objectif d'une solution entière rencontrée jusqu'à présent.
    * **Borne Duale (Upper Bound pour max, Lower Bound pour min) à un nœud :** Objectif de la relaxation linéaire du problème de ce nœud.
    * **Élagage (Pruning) :** On ignore définitivement une branche si :
        1.  Le sous-problème (sa relaxation LP) n'a pas de solution (irréalisable).
        2.  La borne duale du nœud est pire que la borne primale actuelle (ex: pour une maximisation, si $z_{dual\_noeud} < z_{primal\_courant}$).
        3.  La solution de la relaxation LP du nœud est entière. C'est une solution candidate pour le problème entier. On met à jour la borne primale si elle est meilleure.
    * **Arrêt :** Quand tous les nœuds ont été explorés ou élagués. La solution correspondant à la meilleure borne primale est l'optimum. Ou si un nœud donne une solution entière optimale dont l'objectif est meilleur ou égal aux bornes duales de tous les autres nœuds actifs.

### 2.4. Problèmes Combinatoires "Faciles"

Certains problèmes en nombres entiers ont des propriétés structurelles qui les rendent "faciles" à résoudre, souvent parce que leur relaxation linéaire a des solutions optimales entières.

* **Formulations Idéales :**
    * Si la résolution de la relaxation linéaire sur l'enveloppe convexe des points entiers ($\text{conv}(X \cap \mathbb{Z}^n)$) donne une solution entière, c'est une formulation idéale.
    * [Image de l'Enveloppe Convexe]
* **Exemples de Problèmes avec Formulations Idéales/Compactes et Algorithmes Efficaces :**

    * **Plus Court Chemin (Shortest Path) :**
        * Formulation PL en variables binaires. Si coûts $c_e \ge 0$, on peut relâcher $x_e \in \{0,1\}$ en $x_e \ge 0$, et les solutions basiques seront entières.
        * Algorithmes : Dijkstra (1959), Bellman-Ford (1958), Floyd-Warshall (1962).
    * **Flot Maximum (Max Flow) :**
        * Formulation PL. Si capacités $u_e \in \mathbb{Z}_+$, la formulation est idéale et compacte.
        * Exemple : Réseau ferroviaire URSS/Europe de l'Est (Harris & Ross, '55).
        * Algorithmes : Ford-Fulkerson (1955), Dinic (1970), Push-Relabel (Goldberg-Tarjan 1986), Pseudoflow (Hochbaum 2008).
    * **Coupe Minimum (Min Cut) :**
        * Problème dual du flot maximum. Admet aussi une formulation idéale.
        * Les algorithmes de flot maximum donnent gratuitement une coupe minimale (Théorème Max-Flow Min-Cut).
    * **Flot à Coût Minimum (Min Cost Flow) :**
        * Généralise Shortest Path et Max Flow.
        * Formulation idéale et compacte si coûts $c_e \ge 0$, capacités $u_e \in \mathbb{Z}_+^m$, et demandes/offres $d_j \in \mathbb{Z}^n$.
        * Algorithmes : Minimum mean-cycle cancelling, Successive shortest paths, Network simplex, Out-of-Kilter.
    * **Couplage de Poids Maximum en Graphes Bipartis (Bipartite Max Matching) :**
        * Formulation idéale et compacte.
        * [Image d'un Couplage Biparti]
        * Algorithmes : Successive augmenting paths, Algorithme Hongrois. Réductible à Max Flow.
    * **Arbre Couvrant de Poids Minimum (Minimum Weight Spanning Tree - MST) :**
        * Trouver un arbre qui connecte tous les nœuds avec un poids total minimum.
        * Algorithmes gloutons optimaux : Kruskal (1956), Prim (1959).
        * Pas de formulation PL idéale de taille polynomiale connue. Il existe des formulations compactes en nombres entiers ou des formulations idéales de taille exponentielle (via les contraintes de coupe).



