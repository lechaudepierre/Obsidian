# Résumé Détaillé : Algorithmes Heuristiques pour l'Optimisation Combinatoire Multi-objectifs (MCOP)

Ce document détaille les concepts clés et les approches heuristiques pour résoudre les problèmes d'optimisation combinatoire multi-objectifs (MCOP), en se basant sur les diapositives de la présentation.

**1. Introduction aux MCOP**

* **Définition Fondamentale :** Les MCOP concernent l'optimisation simultanée de $Q$ (où $Q \ge 2$) fonctions objectifs $f_1, ..., f_Q$ sur un ensemble $X$ de solutions réalisables. Ces solutions ont une structure combinatoire (ex: permutations, sous-ensembles, chemins dans un graphe). L'objectif est de trouver l'ensemble des solutions offrant les meilleurs compromis possibles entre ces objectifs souvent conflictuels.
    * Formulation : $min_{x\in X} f(x) = (f_1(x), ..., f_Q(x))$
* **Exemple Illustratif (Itinéraire en Train) :** La recherche du "meilleur" itinéraire entre des villes implique un compromis entre minimiser le temps total de trajet et minimiser le coût total des billets. L'itinéraire le plus rapide (ex: que des trains à grande vitesse) est rarement le moins cher, et vice-versa. Entre ces extrêmes existent des solutions intermédiaires (mix de types de trains) offrant différents équilibres temps/coût.
* **Optimalité de Pareto :**
    * **Dominance :** Un vecteur objectif $u$ domine un vecteur $v$ ($u \le v$) si $u_i \le v_i$ pour tout $i=1,...,Q$ et $u \ne v$ (au moins une composante est strictement meilleure).
    * **Solution Efficace (Pareto-optimale) :** Une solution $x \in X$ est efficace si son vecteur objectif $f(x)$ n'est dominé par aucun autre vecteur objectif $f(x')$ pour $x' \in X$. Autrement dit, on ne peut améliorer aucun objectif sans en dégrader au moins un autre.
    * **Ensemble Efficace ($X_E$) :** L'ensemble de toutes les solutions efficaces.
    * **Ensemble Non-dominé / Front de Pareto ($Y_N$) :** L'image de l'ensemble efficace dans l'espace des objectifs ($Y_N = f(X_E)$). C'est cet ensemble que l'on cherche typiquement à trouver ou à approximer.
* **Complexité :**
    * La plupart des MCOP sont NP-difficiles.
    * Le problème de décision associé (MCOP-D : existe-t-il une solution $x$ telle que $f(x) \le z$ pour un vecteur $z$ donné ?) est NP-complet si la version mono-objectif l'est.
    * **Important :** MCOP-D peut être NP-complet même si le problème mono-objectif est résoluble en temps polynomial (ex: problème du plus court chemin bi-objectif).

**2. Méthodes de Résolution pour MCOP**

* **Méthodes d'Énumération (Exactes) :**
    * Visent à trouver l'ensemble efficace exact ($X_E$).
    * **Branch & Bound Multi-objectifs :** Adapte le principe de séparation et évaluation en utilisant des bornes sur les vecteurs objectifs et des règles d'élagage basées sur la dominance.
    * **Programmation Dynamique Multi-objectifs :** Adapte la programmation dynamique en conservant des ensembles de solutions partielles non-dominées à chaque étape.
    * Limitées par la taille potentiellement exponentielle de $X_E$ et la complexité intrinsèque.
* **Méthodes Scalarisées :**
    * **Principe :** Transformer le MCOP en un ou plusieurs problèmes mono-objectifs en agrégeant les objectifs.
    * **Somme Pondérée :** $min_{x\in X} \sum_{i=1}^{Q} \lambda_i f_i(x)$ avec $\lambda_i \ge 0$ et $\sum \lambda_i = 1$. Chaque vecteur $\lambda$ définit une direction de recherche. Une solution optimale pour $\lambda > 0$ est garantie d'être efficace.
    * **Méthode $\epsilon$-contrainte :** Optimise un objectif $f_k(x)$ tout en ajoutant des contraintes sur les autres objectifs $f_j(x) \le \epsilon_j$ pour $j \ne k$. En variant les $\epsilon_j$, on peut générer différentes solutions efficaces.
    * **Limites :** La somme pondérée ne peut pas trouver les solutions efficaces situées dans les parties concaves du front de Pareto. Le choix approprié des poids ou des $\epsilon$ est crucial et pas toujours évident.
* **Algorithmes SLS (Stochastic Local Search) / Heuristiques :**
    * Nécessaires car les méthodes exactes sont souvent trop lentes pour les problèmes réels.
    * Adaptent des heuristiques mono-objectifs (Recherche Locale, Recuit Simulé, Algorithmes Évolutionnaires, Coloniaux, etc.).
    * **Défis Spécifiques aux MCOP :**
        1.  **Génération d'un Ensemble :** Comment l'algorithme peut-il produire un *ensemble* de solutions (approximation de $X_E$) plutôt qu'une seule ?
        2.  **Guidage de la Recherche :** Comment guider la recherche vers le front de Pareto ?
        3.  **Maintien de la Diversité :** Comment s'assurer que les solutions trouvées sont bien réparties le long du front et ne se concentrent pas sur une seule région ?

**3. Modèles de Recherche SLS pour MCOP**

Ces modèles proposent différentes stratégies pour relever les défis ci-dessus.

* **Modèle SAC (Scalarized Acceptance Criterion) :**
    * **Idée :** Utiliser une fonction de scalarisation (souvent la somme pondérée ou Tchebycheff pondérée) comme *unique* critère d'évaluation et d'acceptation au sein d'un algorithme SLS standard (ex: Descente Locale, Recuit Simulé).
    * **Processus :** On choisit un ensemble de vecteurs de poids $\Lambda$. Pour chaque $\lambda \in \Lambda$, on lance l'algorithme SLS configuré avec la fonction scalarisée $f_\lambda(x)$. La solution finale $x'_\lambda$ obtenue est ajoutée à une archive externe.
    * **Archive :** Après avoir testé tous les $\lambda$, l'archive contient un ensemble de solutions potentiellement bonnes. Elle est ensuite filtrée pour ne conserver que les solutions mutuellement non-dominées.
    * **Avantage :** Relativement simple à implémenter si on dispose d'un bon solveur SLS mono-objectif.
    * **Inconvénient :** La performance dépend fortement du choix des $\lambda$ et de la capacité de la fonction de scalarisation à bien guider la recherche vers toutes les parties du front.
* **Modèle EMO (Evolutionary Multi-Objective Optimization) :**
    * **Idée :** Appliquer les principes de l'évolution (population, sélection, croisement, mutation) directement avec la notion de dominance de Pareto.
    * **Processus :** Maintient une population $X_n$ de solutions. À chaque génération :
        1.  Crée une nouvelle population $X_r$ par reproduction/mutation à partir de $X_n$.
        2.  Combine $X_n$ et $X_r$.
        3.  Classe (Rank) les solutions combinées en fonction de leur dominance (fronts de Pareto successifs) et/ou d'indicateurs de diversité.
        4.  Sélectionne les meilleures solutions (selon le rang et la diversité) pour former la nouvelle population $X_{n+1}$.
    * **Avantage :** Conçu nativement pour gérer plusieurs objectifs et maintenir une population diversifiée. Des algorithmes populaires comme NSGA-II ou SPEA2 entrent dans cette catégorie.
    * **Inconvénient :** Peut être plus complexe à paramétrer (taille de population, opérateurs génétiques, etc.).
* **Modèle CWAC (Component-Wise Acceptance Criterion) / Recherche Locale Multi-objectifs :**
    * **Idée :** Utiliser directement la dominance de Pareto pour guider la recherche locale.
    * **Processus :** Maintient une archive des solutions non-dominées trouvées. À partir d'une solution $x$ de l'archive, explore son voisinage $N(x)$. Un voisin $x'$ est ajouté à l'archive s'il n'est dominé par aucune solution déjà présente. L'archive est constamment filtrée pour ne garder que les solutions non-dominées. La recherche peut continuer à partir de différentes solutions de l'archive.
    * **Variantes :** Peut utiliser des critères d'acceptation plus souples (accepter un voisin même s'il n'est pas strictement meilleur mais non-dominé) ou des techniques pour limiter la taille de l'archive (ex: $\epsilon$-dominance, où on ne garde qu'un nombre limité de solutions dans chaque "boîte" $\epsilon$ de l'espace objectif).
    * **Avantage :** Travaille directement avec la dominance, pas besoin de scalarisation.
    * **Inconvénient :** La gestion de l'archive et le choix de la prochaine solution à explorer peuvent être complexes.
* **Modèle Hybride :**
    * **Idée :** Combiner les forces des approches précédentes.
    * **Exemple :** Lancer une recherche SAC avec un $\lambda$ donné pour trouver rapidement une bonne solution $x'$ dans cette direction. Ensuite, utiliser $x'$ comme point de départ pour une recherche CWAC afin d'explorer finement le voisinage de $x'$ et trouver d'autres solutions non-dominées proches sur le front de Pareto. Répéter pour différents $\lambda$.

**4. Évaluation de la Performance des Algorithmes MCOP**

Évaluer un algorithme MCOP est plus complexe que pour le mono-objectif, car le résultat est un *ensemble* de solutions.

* **Critères Qualitatifs :** Un bon ensemble d'approximation $A$ du front de Pareto $Y_N$ doit être :
    * **Proche de $Y_N$ :** Les solutions de $A$ doivent être proches des vraies solutions efficaces.
    * **Bien distribué :** Les solutions de $A$ doivent couvrir uniformément l'étendue du front $Y_N$.
    * **Large :** L'étendue des solutions de $A$ doit correspondre à celle de $Y_N$.
* **Indicateurs Quantitatifs :**
    * **Relations de Supériorité ("Better") :** Comparaison directe entre deux ensembles $A$ et $B$. $A$ est "meilleur" que $B$ si toute solution dans $B$ est dominée par au moins une solution dans $A$. Permet un classement partiel mais robuste.
    * **Indicateur Unaire (Hypervolume - $I_H$) :**
        * Calcule le volume (ou surface en 2D) de l'espace objectif qui est dominé par l'ensemble de solutions $A$, délimité par un point de référence $r$.
        * $I_H(A) = \text{Volume}(\cup_{y \in A} \{z \in \mathbb{R}^Q \mid y \le z \le r\})$
        * Un $I_H$ plus grand indique un meilleur ensemble (à la fois plus proche et/ou plus diversifié). C'est l'un des indicateurs les plus utilisés car il est compatible avec la dominance de Pareto (si $A$ est meilleur que $B$, alors $I_H(A) \ge I_H(B)$).
        * Le choix du point de référence peut influencer la valeur absolue.
    * **Fonctions d'Atteinte (Attainment Functions) :**
        * Approche empirique et statistique basée sur les résultats de multiples exécutions indépendantes d'un algorithme.
        * La fonction d'atteinte $\alpha_A(z)$ donne la probabilité qu'un point objectif $z$ soit dominé par l'ensemble $A$ produit par une exécution de l'algorithme.
        * Permet de visualiser la performance moyenne et la variabilité de l'algorithme sur l'espace objectif.
        * Permet des tests statistiques (comme Kolmogorov-Smirnov) pour comparer rigoureusement les performances de deux algorithmes sur la base de leurs fonctions d'atteinte empiriques (EAF).

**5. Autres Sujets et Perspectives**

* L'adaptation d'autres métaheuristiques comme ILS, ACO, PSO aux MCOP est un domaine actif.
* Les techniques de gestion de la diversité (sharing, niching) sont cruciales dans les EMO.
* L'hybridation avec des méthodes exactes (ex: utiliser des heuristiques pour trouver de bonnes bornes dans un Branch & Bound) est prometteuse.
* La structure de l'ensemble efficace (est-il connexe ?) peut influencer la difficulté de la recherche.
* Les problèmes "Many-Objective" ($Q \gg 2$) posent des défis supplémentaires car la dominance de Pareto devient moins discriminante (presque toutes les solutions deviennent non-dominées).

**Références :** La présentation originale fournit une liste de ressources bibliographiques importantes pour approfondir ces sujets.
