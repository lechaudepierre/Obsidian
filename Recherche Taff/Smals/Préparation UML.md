# Préparation épreuve technique Smals — UML, ERD & Modélisation

---

## Ce qu'on sait du test Smals (Glassdoor)

D'après les retours de candidats sur Glassdoor, le processus chez Smals se déroule généralement en plusieurs étapes :

1. **Entretien RH** : premier contact, parcours, motivations
2. **Tests en ligne** : compréhension, logique, mathématiques (3 exercices)
3. **Test technique de modélisation** : c'est celui qui nous intéresse ici — il porte sur **UML (use case, activité, séquence), ERD**, et la modélisation de situations
4. **Entretien final** avec le client (institution publique détachée)

Le contexte Smals est important : ils travaillent pour la sécurité sociale belge, l'e-gouvernement et l'e-santé. Les systèmes qu'ils modélisent sont des processus administratifs (demandes de prestations, gestion de dossiers, flux de validation, etc.). Garde ça en tête quand tu fais les exercices.

---

## 1. Diagramme de cas d'utilisation (Use Case Diagram)

### Concept

Le diagramme de cas d'utilisation montre **ce que le système fait** du point de vue de ses utilisateurs. Il ne montre PAS comment le système le fait — il reste au niveau fonctionnel.

### Éléments clés

**Acteur** : une entité externe qui interagit avec le système. Ça peut être un humain (ex: Citoyen, Agent ONSS, Médecin) ou un autre système (ex: Système bancaire, Base de données nationale). On le dessine avec un bonhomme bâton.

**Cas d'utilisation** : une fonctionnalité ou un service offert par le système à un acteur. On le dessine avec une ellipse contenant le nom de l'action (verbe à l'infinitif). Exemples : "Soumettre une demande d'allocations", "Consulter un dossier", "Valider une prescription".

**Frontière du système** : un rectangle qui délimite ce qui fait partie du système et ce qui est externe.

**Relations entre cas d'utilisation** :

- **`<<include>>`** : le cas d'utilisation A inclut TOUJOURS le cas d'utilisation B. C'est une factorisation de comportement commun. Flèche pointillée de A vers B.
    
    - Exemple : "Soumettre une demande" `<<include>>` "S'authentifier" → on doit toujours s'authentifier pour soumettre.
- **`<<extend>>`** : le cas d'utilisation B étend OPTIONNELLEMENT le cas d'utilisation A. C'est un comportement conditionnel. Flèche pointillée de B vers A.
    
    - Exemple : "Consulter un dossier" `<<extend>>` "Imprimer le dossier" → l'impression est facultative.
- **Généralisation (héritage)** : un acteur ou un cas d'utilisation spécialisé hérite d'un plus général. Flèche pleine avec triangle vide.
    
    - Exemple : "Agent senior" hérite de "Agent" → l'agent senior peut tout faire comme un agent normal, plus des choses supplémentaires.

### Erreurs fréquentes à éviter

- Ne pas confondre `<<include>>` et `<<extend>>`. Retiens : include = obligatoire, extend = optionnel.
- Ne pas mettre de flèches entre acteurs et cas d'utilisation pour montrer un flux de données — la ligne simple (association) suffit.
- Ne pas décomposer en trop de sous-cas d'utilisation. Reste au niveau fonctionnel utile pour l'utilisateur.
- Les acteurs sont TOUJOURS en dehors de la frontière du système.

### Exercice type Smals

> **Énoncé** : Un citoyen peut consulter son dossier de sécurité sociale en ligne. Pour cela, il doit s'authentifier via eID (carte d'identité électronique). Une fois connecté, il peut visualiser ses données personnelles, télécharger ses attestations, et éventuellement soumettre une réclamation. Un agent de l'organisme peut consulter n'importe quel dossier, valider ou rejeter les réclamations. Un administrateur a les mêmes droits qu'un agent, plus la gestion des comptes utilisateurs.

**Résolution** :

Acteurs : Citoyen, Agent, Administrateur (hérite d'Agent), Système eID (acteur secondaire)

Cas d'utilisation :

- Consulter son dossier (`<<include>>` S'authentifier via eID)
- Visualiser données personnelles
- Télécharger attestation
- Soumettre une réclamation (`<<extend>>` depuis Consulter son dossier)
- Consulter un dossier (pour Agent)
- Valider/Rejeter réclamation
- Gérer comptes utilisateurs (pour Administrateur uniquement)

---

## 2. Diagramme de séquence (Sequence Diagram)

### Concept

Le diagramme de séquence montre **comment** les objets/acteurs interagissent dans le temps pour réaliser un scénario précis. Le temps s'écoule de haut en bas. C'est le diagramme le plus couramment demandé pour montrer le détail d'un cas d'utilisation.

### Éléments clés

**Ligne de vie (lifeline)** : une barre verticale pointillée sous un rectangle contenant le nom du participant. La syntaxe est `nomObjet : NomClasse` (ou juste `: NomClasse` si anonyme).

**Message** : une flèche horizontale d'une ligne de vie à une autre. Représente un appel de méthode, un signal, ou une requête.

Types de messages :

- **Synchrone** (flèche pleine avec pointe pleine ▶) : l'émetteur attend la réponse.
- **Asynchrone** (flèche avec pointe ouverte >) : l'émetteur n'attend pas.
- **Retour** (flèche pointillée ← ) : la réponse à un message synchrone.
- **Création** (flèche pointillée avec `<<create>>`) : crée un nouvel objet.
- **Destruction** (X sur la ligne de vie) : l'objet est détruit.

**Barre d'activation** : rectangle fin sur la ligne de vie, indiquant que l'objet est en train de traiter un message.

### Fragments combinés (UML 2)

Ce sont les structures de contrôle dans un diagramme de séquence :

- **`alt`** (alternative) : équivalent d'un if/else. Deux zones séparées par une ligne pointillée, chacune avec une condition entre crochets `[condition]`.
- **`opt`** (optionnel) : équivalent d'un if sans else. Une seule zone avec condition.
- **`loop`** : boucle. Condition entre crochets. Ex: `loop [tant que panier non vide]`.
- **`par`** : exécution parallèle.
- **`ref`** : référence à un autre diagramme de séquence (pour éviter de surcharger).

### Exercice type Smals

> **Énoncé** : Modélisez le scénario suivant : un citoyen se connecte au portail de la sécurité sociale, s'authentifie avec son eID, consulte ses données de carrière, et si des erreurs sont détectées, soumet une demande de correction.

**Résolution** (description textuelle du diagramme) :

Participants : `Citoyen`, `: PortailWeb`, `: ServiceAuth`, `: BaseDonnéesCarrière`

```
Citoyen → PortailWeb : accéder au portail()
PortailWeb → ServiceAuth : demanderAuthentification()
ServiceAuth → Citoyen : demander insertion eID
Citoyen → ServiceAuth : insérer eID + PIN
ServiceAuth → ServiceAuth : vérifier certificat
ServiceAuth → PortailWeb : authentificationOK(identité)
PortailWeb → BaseDonnéesCarrière : getDonnéesCarrière(identité)
BaseDonnéesCarrière → PortailWeb : données carrière
PortailWeb → Citoyen : afficher données carrière

--- fragment alt ---
[erreur détectée par citoyen]
  Citoyen → PortailWeb : soumettreDemandeCorrectionerreur()
  PortailWeb → BaseDonnéesCarrière : enregistrerDemande(correction)
  BaseDonnéesCarrière → PortailWeb : confirmationEnregistrement
  PortailWeb → Citoyen : confirmation soumission
[pas d'erreur]
  (rien — fin du scénario)
--- fin fragment alt ---
```

### Points importants pour le test

- Chaque message doit avoir un nom clair (idéalement un nom de méthode).
- Les messages de retour doivent correspondre logiquement au message envoyé.
- Utilise les fragments `alt`, `opt` et `loop` quand l'énoncé mentionne des conditions ou des répétitions.
- N'oublie pas les barres d'activation.

---

## 3. Diagramme d'activité (Activity Diagram)

### Concept

Le diagramme d'activité modélise un **flux de travail** (workflow) ou un **processus métier**. C'est un peu comme un organigramme (flowchart) amélioré avec des possibilités de parallélisme et de partitionnement par acteur.

### Éléments clés

- **Nœud initial** : cercle plein noir (point de départ)
- **Nœud final** : cercle plein noir entouré d'un cercle (point d'arrivée)
- **Action** : rectangle arrondi contenant une description de l'action
- **Décision / Fusion** : losange. Pour une décision, une flèche entre et plusieurs sortent (avec conditions entre crochets). Pour une fusion, plusieurs entrent, une sort.
- **Fork / Join** : barre épaisse horizontale. Le fork sépare un flux en plusieurs parallèles. Le join attend que tous les flux parallèles terminent.
- **Couloirs d'activité (swimlanes)** : partitions verticales ou horizontales qui assignent les actions à des acteurs/rôles.

### Quand utiliser les swimlanes

Dans le contexte Smals (processus administratifs), les swimlanes sont très pertinents. Par exemple, pour un processus de demande d'allocations :

- Couloir "Citoyen" : soumet la demande
- Couloir "Système" : vérifie automatiquement les données
- Couloir "Agent" : traite manuellement les cas complexes
- Couloir "Direction" : approuve les montants élevés

### Exercice type Smals

> **Énoncé** : Modélisez le processus de traitement d'une demande d'allocation de chômage. Le citoyen soumet sa demande en ligne. Le système vérifie automatiquement l'éligibilité (derniers emplois, durée de cotisation). Si les conditions automatiques sont remplies, la demande est approuvée automatiquement. Sinon, elle est envoyée à un agent pour examen manuel. L'agent peut approuver ou rejeter. En cas de rejet, le citoyen est notifié et peut faire appel.

**Résolution** (description) :

```
[Nœud initial]
   ↓
Citoyen : Soumettre demande en ligne
   ↓
Système : Vérifier éligibilité automatique
   ↓
<Décision> — [éligible automatiquement?]
   |                    |
[oui]                [non]
   ↓                    ↓
Système :           Agent : Examiner
Approuver              le dossier
automatiquement         ↓
   ↓              <Décision>
   |            [approuvé?]
   |             |         |
   |           [oui]     [non]
   |             ↓         ↓
   |          Agent:    Agent:
   |          Approuver Rejeter
   |             ↓         ↓
   |             |    Citoyen: Recevoir
   |             |    notification rejet
   |             |         ↓
   |             |    <Décision>
   |             |    [fait appel?]
   |             |     |       |
   |             |   [oui]   [non]
   |             |     ↓       ↓
   |             |   (retour  [Nœud
   |             |   vers      final]
   |             |   Examiner)
   ↓             ↓
  Fusion ← ← ← ←
   ↓
Système : Envoyer confirmation au citoyen
   ↓
[Nœud final]
```

---

## 4. Diagramme de classes (Class Diagram)

### Concept

Le diagramme de classes montre la **structure statique** du système : les classes, leurs attributs, leurs méthodes, et les relations entre elles.

### Éléments clés

**Classe** : rectangle divisé en 3 parties :

1. Nom de la classe (en gras)
2. Attributs : `visibilité nom : type`
3. Méthodes : `visibilité nom(paramètres) : typeRetour`

Visibilités : `+` public, `-` privé, `#` protégé, `~` package

### Relations

**Association** : ligne simple entre deux classes. Peut avoir un nom, des rôles aux extrémités, et des **multiplicités** :

- `1` : exactement un
- `0..1` : zéro ou un
- `*` ou `0..*` : zéro ou plusieurs
- `1..*` : un ou plusieurs
- `n..m` : entre n et m

**Agrégation** (losange vide ◇) : relation "a un" faible. La partie peut exister sans le tout.

- Exemple : une Équipe ◇— Joueur (un joueur peut exister sans équipe)

**Composition** (losange plein ◆) : relation "a un" forte. La partie ne peut PAS exister sans le tout.

- Exemple : une Commande ◆— LigneDeCommande (pas de ligne sans commande)

**Héritage / Généralisation** (flèche avec triangle vide △) : relation "est un".

- Exemple : Employé △— Agent, Employé △— Manager

**Dépendance** (flèche pointillée) : une classe utilise temporairement une autre.

**Classe abstraite** : nom en _italique_ ou avec `<<abstract>>`.

**Interface** : stéréotype `<<interface>>` au-dessus du nom.

### Exercice type Smals

> **Énoncé** : Modélisez le système de gestion des dossiers d'un organisme de sécurité sociale. Un dossier est ouvert pour un assuré social (identifié par son numéro national, nom, prénom, date de naissance). Un dossier peut contenir plusieurs documents (date d'ajout, type de document, fichier). Chaque dossier est assigné à un agent traitant. Un agent peut traiter plusieurs dossiers. Un dossier passe par plusieurs états (ouvert, en traitement, clôturé). Chaque changement d'état est historisé (date, ancien état, nouvel état, commentaire).

**Résolution** :

```
+---------------------+      1    * +-------------------+
|   AssuréSocial      |———————————|     Dossier        |
|---------------------|            |-------------------|
| - numNational: String|            | - idDossier: int  |
| - nom: String       |            | - dateOuverture: Date|
| - prénom: String    |            | - état: ÉtatDossier|
| - dateNaissance: Date|            |-------------------|
|---------------------|            | + changerÉtat()   |
| + getInfos(): String|            | + ajouterDocument()|
+---------------------+            +-------------------+
                                        |  1     * |
                          +-------------+          +------------------+
                          |                        |                  |
                    * |   | 1                   ◆  |                  |
              +------------+              +------------------+  +------------------+
              |   Agent    |              |    Document      |  | HistoriqueÉtat   |
              |------------|              |------------------|  |------------------|
              | - matricule|              | - dateAjout: Date|  | - date: Date     |
              | - nom      |              | - typeDoc: String|  | - ancienÉtat     |
              | - service  |              | - fichier: Blob  |  | - nouvelÉtat     |
              |------------|              +------------------+  | - commentaire    |
              | + valider()|                                    +------------------+
              +------------+

<<enumeration>>
ÉtatDossier
-----------
OUVERT
EN_TRAITEMENT
CLÔTURÉ
```

---

## 5. ERD — Entity Relationship Diagram (Diagramme Entité-Relation)

### Concept

L'ERD modélise la **structure des données** et leurs relations, indépendamment de l'implémentation technique. C'est la base de la conception de bases de données relationnelles. Chez Smals, c'est directement lié à la modélisation des données des institutions de sécurité sociale.

### Notation

Il existe plusieurs notations (Chen, Crow's Foot / Patte d'oie, UML). En contexte de recrutement, la notation **Crow's Foot** (patte d'oie) est la plus répandue dans l'industrie.

**Entité** : rectangle contenant le nom de l'entité (en majuscules ou PascalCase). Les attributs sont listés en dessous, avec la clé primaire soulignée ou marquée PK.

**Relation** : ligne entre deux entités, avec les cardinalités aux extrémités :

- `||` : exactement un (obligatoire)
- `|O` : zéro ou un (optionnel)
- `>|` ou patte d'oie + trait : un ou plusieurs
- `>O` ou patte d'oie + rond : zéro ou plusieurs

### Concepts essentiels

**Clé primaire (PK)** : identifiant unique d'une entité.

**Clé étrangère (FK)** : attribut dans une entité qui référence la PK d'une autre entité.

**Relation 1-1** : chaque occurrence d'une entité correspond à au plus une occurrence de l'autre. Rare. Exemple : un Citoyen a un et un seul NuméroNational.

**Relation 1-N** : une occurrence d'un côté correspond à plusieurs de l'autre. Exemple : un Agent traite plusieurs Dossiers, mais un Dossier a un seul Agent traitant.

**Relation N-M** : plusieurs-à-plusieurs. Nécessite une **table d'association** (table de jonction) pour être implémentée. Exemple : un Dossier peut concerner plusieurs Prestations, et une Prestation peut apparaître dans plusieurs Dossiers → table DossierPrestation.

**Entité faible** : entité qui ne peut exister sans une autre entité. Exemple : une LigneDeCommande n'existe pas sans sa Commande.

### Exercice type Smals

> **Énoncé** : Concevez un ERD pour le système de gestion des prescriptions médicales électroniques. Un médecin peut créer plusieurs prescriptions. Chaque prescription est destinée à un seul patient. Une prescription peut contenir plusieurs médicaments (avec posologie et quantité). Un pharmacien délivre les prescriptions. Une même prescription peut être délivrée en plusieurs fois (délivrance partielle). Chaque délivrance est horodatée.

**Résolution** :

Entités et attributs :

```
MÉDECIN (PK: numINAMI, nom, prénom, spécialité)
   |
   | 1——crée——N
   |
PRESCRIPTION (PK: idPrescription, FK: numINAMI, FK: numNational, 
              dateCréation, statut)
   |                    |
   | 1                  | N
   |                    |
   N                    |
PATIENT             LIGNE_PRESCRIPTION (PK: idLigne, FK: idPrescription,
(PK: numNational,       FK: codeMédicament, posologie, quantité)
nom, prénom,             |
dateNaissance,           N
adresse)                 |
                    MÉDICAMENT (PK: codeMédicament, nomCommercial, 
                               DCI, dosage)

PRESCRIPTION ——1——N—— DÉLIVRANCE (PK: idDélivrance, FK: idPrescription,
                                   FK: numAPB, dateHeure, 
                                   quantitéDélivrée)
                                        |
                                        N
                                        |
                                   PHARMACIEN (PK: numAPB, nom,
                                              prénom, officine)
```

Points clés de cet exercice :

- La relation Prescription-Médicament est N-M, donc on crée la table LIGNE_PRESCRIPTION
- La délivrance partielle implique une relation 1-N entre Prescription et Délivrance
- Les identifiants reflètent le domaine belge : numINAMI (médecins), numNational (citoyens), numAPB (pharmaciens)

---

## 6. Diagramme d'états (State Machine Diagram)

### Concept

Modélise les **états** possibles d'un objet et les **transitions** entre ces états en réponse à des événements. Très utile pour modéliser le cycle de vie d'un dossier administratif.

### Éléments

- **État** : rectangle arrondi avec le nom de l'état
- **Transition** : flèche d'un état à l'autre, étiquetée : `événement [condition] / action`
- **État initial** : point noir
- **État final** : point noir cerclé

### Exercice type Smals

> Cycle de vie d'une demande d'allocation

```
[●] → SOUMISE
      → evt: vérification auto [données complètes] / notifier agent
         → EN_EXAMEN
            → evt: décision agent [approuvée] / calculer montant
               → APPROUVÉE
                  → evt: paiement effectué
                     → CLÔTURÉE → [◉]
            → evt: décision agent [rejetée] / notifier citoyen
               → REJETÉE
                  → evt: appel citoyen [dans délai]
                     → EN_APPEL → EN_EXAMEN
                  → evt: délai expiré
                     → CLÔTURÉE → [◉]
      → evt: vérification auto [données incomplètes] / notifier citoyen
         → EN_ATTENTE_COMPLÉMENT
            → evt: complément reçu
               → SOUMISE (retour au début)
            → evt: délai expiré
               → ANNULÉE → [◉]
```

---

## 7. Conseils de modélisation pour le test

### Méthodologie générale quand tu reçois un énoncé

1. **Lis tout l'énoncé** une première fois sans rien écrire.
2. **Identifie les noms** → ce sont tes entités/classes (Citoyen, Dossier, Agent, Prescription...).
3. **Identifie les verbes** → ce sont tes actions/méthodes/cas d'utilisation (soumettre, valider, consulter...).
4. **Identifie les adjectifs et états** → ce sont tes attributs ou tes états (ouvert, en traitement, approuvé...).
5. **Identifie les quantificateurs** → ce sont tes multiplicités/cardinalités (un, plusieurs, au plus un...).
6. **Dessine un brouillon** avant de faire la version propre.

### Pièges classiques dans les tests de modélisation

- **Oublier un acteur** : relis l'énoncé en cherchant tous les rôles mentionnés.
- **Confondre association et dépendance** : une association est structurelle (les objets se connaissent), une dépendance est temporaire.
- **Mauvaises cardinalités** : "un client peut avoir plusieurs commandes" → 1 (côté client) — * (côté commande).
- **Relations N-M non décomposées** dans un ERD : toujours créer une table d'association.
- **Diagramme trop détaillé ou trop vague** : adapte le niveau de détail à ce qui est demandé.

### Vocabulaire Smals à connaître

Puisque Smals travaille pour la sécurité sociale et l'e-santé belge, familiarise-toi avec :

- **Numéro national** : identifiant unique d'un citoyen belge
- **NISS** : Numéro d'Identification de Sécurité Sociale
- **eID** : carte d'identité électronique belge
- **ONSS** : Office National de Sécurité Sociale
- **ONEM** : Office National de l'Emploi
- **eHealth** : plateforme d'échange de données de santé
- **BCSS** : Banque Carrefour de la Sécurité Sociale (hub central de données)
- **Prestation** : avantage ou service fourni par la sécurité sociale
- **Affiliation** : lien entre un assuré et un organisme

---

## 8. Mini-QCM d'entraînement

**Q1.** Quelle est la différence entre `<<include>>` et `<<extend>>` ?

→ `<<include>>` : la relation est obligatoire, le cas d'utilisation de base appelle toujours le cas inclus. `<<extend>>` : la relation est optionnelle, le cas d'utilisation étendu est appelé sous condition.

**Q2.** Dans un diagramme de séquence, quel fragment utilisez-vous pour un if/else ?

→ Le fragment `alt` (alternative), avec des conditions entre crochets pour chaque branche.

**Q3.** Quelle est la différence entre agrégation et composition ?

→ L'agrégation (losange vide) est une relation faible : la partie peut exister sans le tout. La composition (losange plein) est forte : la partie est détruite si le tout est détruit. Exemple : un Chapitre (composition) appartient à un Livre — si on supprime le Livre, les Chapitres disparaissent. Un Étudiant (agrégation) fait partie d'un Club — si le Club est dissous, l'Étudiant existe encore.

**Q4.** Comment modélise-t-on une relation N-M dans un ERD ?

→ En créant une table d'association (table de jonction) qui contient les clés étrangères des deux entités, plus éventuellement des attributs propres à la relation.

**Q5.** Quelle est la différence entre un diagramme d'activité et un diagramme de séquence ?

→ Le diagramme d'activité montre le flux de travail (qui fait quoi et dans quel ordre), c'est un point de vue processus. Le diagramme de séquence montre les interactions entre objets dans le temps pour un scénario précis, c'est un point de vue communication.

**Q6.** Dans un diagramme de classes, comment représente-t-on qu'un attribut est dérivé (calculé) ?

→ On le préfixe avec `/`. Exemple : `/âge : int` (calculé à partir de la date de naissance).

**Q7.** Qu'est-ce qu'une entité faible dans un ERD ?

→ C'est une entité dont l'existence dépend d'une autre entité (son propriétaire). Sa clé primaire inclut la clé étrangère vers l'entité propriétaire. Exemple : une LigneDeCommande dépend de la Commande.

**Q8.** Dans un diagramme de séquence, quelle est la différence entre un message synchrone et asynchrone ?

→ Synchrone (flèche pleine) : l'émetteur attend la réponse avant de continuer. Asynchrone (flèche ouverte) : l'émetteur continue sans attendre.

---

## 9. Exercice intégrateur — Style Smals

> **Énoncé complet** : L'ONEM souhaite un nouveau système de gestion des demandes d'allocations de chômage en ligne.
> 
> Le citoyen se connecte via eID et soumet une demande qui inclut ses informations personnelles et ses justificatifs d'emploi (C4). Le système vérifie automatiquement les données auprès de la BCSS (Banque Carrefour). Si les données sont cohérentes, la demande passe en examen. Sinon, le citoyen est invité à corriger.
> 
> Un agent ONEM examine la demande, peut demander des compléments, et prend une décision (approbation ou rejet). Si approuvée, le montant est calculé et le paiement planifié. Le citoyen peut consulter l'état de sa demande à tout moment.
> 
> Modélisez : (a) le diagramme de cas d'utilisation, (b) le diagramme de séquence pour le scénario "soumettre une demande", (c) l'ERD du système.

### (a) Use Case

Acteurs : Citoyen, Agent ONEM, Système BCSS (acteur secondaire)

Cas d'utilisation dans la frontière du système :

- Soumettre une demande (`<<include>>` S'authentifier via eID)
- Consulter état de la demande (`<<include>>` S'authentifier via eID)
- Corriger les données de la demande
- Vérifier données auprès BCSS (acteur : Système BCSS)
- Examiner une demande
- Demander compléments
- Prendre décision (Approuver / Rejeter)
- Calculer montant
- Planifier paiement

Relations : Agent effectue Examiner, Demander compléments, Prendre décision. Calculer montant et Planifier paiement sont `<<include>>` dans Approuver.

### (b) Séquence — Soumettre une demande

```
Participants: Citoyen, :PortailONEM, :ServiceAuth, :ServiceBCSS, :BaseDonnées

Citoyen → PortailONEM : accéderPortail()
PortailONEM → ServiceAuth : demanderAuth()
ServiceAuth → Citoyen : demanderEID()
Citoyen → ServiceAuth : fournirEID_PIN()
ServiceAuth → PortailONEM : authOK(identité)

PortailONEM → Citoyen : afficherFormulaireDemande()
Citoyen → PortailONEM : soumettreFormulaire(données, justificatifs)
PortailONEM → ServiceBCSS : vérifierDonnées(numNational)
ServiceBCSS → PortailONEM : résultatVérification

--- fragment alt ---
[données cohérentes]
    PortailONEM → BaseDonnées : enregistrerDemande(données, statut="EN_EXAMEN")
    BaseDonnées → PortailONEM : confirmationEnregistrement
    PortailONEM → Citoyen : confirmerSoumission(numéroréférence)
[données incohérentes]
    PortailONEM → Citoyen : demanderCorrection(erreursIdentifiées)
--- fin alt ---
```

### (c) ERD

```
CITOYEN (PK: numNational, nom, prénom, dateNaissance, adresse, email)
   |
   | 1 ——— soumet ——— N
   |
DEMANDE (PK: idDemande, FK: numNational, dateSoumission, statut, 
         montantCalculé, dateDécision)
   |              |
   | 1            | 1
   |              |
   N              N
   |              |
JUSTIFICATIF     EXAMEN (PK: idExamen, FK: idDemande, FK: matriculeAgent,
(PK: idJustif,          dateExamen, décision, commentaire)
FK: idDemande,            |
type, fichier,            N
dateAjout)                |
                    AGENT_ONEM (PK: matricule, nom, prénom, 
                               service, dateEntrée)

DEMANDE —— 1 ——— N —— PAIEMENT (PK: idPaiement, FK: idDemande, 
                                 montant, datePlanifiée, 
                                 dateEffective, statut)

DEMANDE —— N ——— N —— VÉRIFICATION_BCSS (PK: idVérif, FK: idDemande,
                                          dateVérif, résultat, 
                                          détailsErreur)
```

---

## 10. Résumé visuel — Les diagrammes en un coup d'œil

|Diagramme|Question à laquelle il répond|Quand l'utiliser|
|---|---|---|
|Use Case|**Quoi ?** Que fait le système ?|Début d'analyse, recenser les besoins|
|Séquence|**Comment ?** Comment les objets interagissent ?|Détailler un scénario précis|
|Activité|**Flux ?** Quel est le workflow ?|Modéliser un processus métier|
|Classes|**Structure ?** Quelles sont les entités et relations ?|Conception orientée objet|
|ERD|**Données ?** Comment stocker l'information ?|Conception de base de données|
|États|**Cycle de vie ?** Par quels états passe un objet ?|Objets avec cycle de vie complexe|

---

Bonne chance pour demain, Pierre.