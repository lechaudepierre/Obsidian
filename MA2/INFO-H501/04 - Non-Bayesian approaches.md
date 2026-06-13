# 04 — Non-Bayesian approaches

> [!abstract] Le fil rouge
> Plutôt que d'estimer des probabilités, ces méthodes décident à partir de la **géométrie locale** des exemples. Le chapitre introduit deux idées : le **k plus proches voisins** (décision par vote du voisinage) et le **arbre de décision** (partition récursive de l'espace), ce dernier étant développé à part dans [[05 - Decision tree]].

## 1. La règle des $k$ plus proches voisins (k-NN)

$\mathbf{x}$ est affecté à la **classe majoritaire** parmi les $k$ exemples les plus proches de $\mathbf{x}$, déterminés selon une **métrique** (distance) donnée.

> [!note] Forme du voisinage
> Le voisinage défini par les $k$-NN peut **varier fortement en taille et en forme**, surtout dans les régions peu denses de l'espace des features.

> [!success] Lien avec Bayes
> Pour $N$ grand et $k$ grand (mais restant une petite portion de $N$), k-NN tend **asymptotiquement vers l'optimum bayésien** (minimisation de $P_e$). C'est un pont direct avec l'estimation non-paramétrique de densité vue dans [[03 - Bayesian approach]] (cas « fixer $k$, calculer $V$ »).

> [!tip] Intuition
> k-NN ne « construit » aucun modèle pendant l'entraînement : il stocke les exemples et décide au moment de la prédiction en regardant qui est autour. C'est puissant mais coûteux à grande échelle, et sensible au choix de $k$ et de la distance.

---

## 2. L'arbre de décision (aperçu)

Partition **récursive** de l'espace des features en régions de plus en plus pures en classes.

> [!info] Renvoi
> Cette méthode est centrale et détaillée dans une fiche dédiée : voir [[05 - Decision tree]] (croissance de l'arbre, critères d'impureté, élagage, forêts).

---

> [!quote] À retenir
> Les approches non-bayésiennes décident sans modéliser explicitement des probabilités. **k-NN** = vote du voisinage, asymptotiquement optimal mais coûteux ; **arbre de décision** = partition récursive interprétable. Toutes deux sont *non-paramétriques* : aucune hypothèse sur la distribution des données.

Voir aussi : [[03 - Bayesian approach]] · [[05 - Decision tree]] · [[00 - Index]]
