# Optimisation des courses d’une flotte de véhicules autonomes

**Affectation et ordonnancement de courses sous contraintes temporelles.**

Projet universitaire d’optimisation combinatoire réalisé par
**Yannick ASSI et Hoang Viet Vu**, dans le cadre du Master MIASHS
à l’Université Catholique de l’Ouest.

**Année universitaire : 2025–2026**

## 1. 🎯 Présentation de l’étude

### Problématique

Comment affecter des courses pré-réservées à une flotte de véhicules
autonomes et déterminer leur ordre d’exécution pour maximiser
le score total ?

La ville est représentée par une grille. Les véhicules doivent
se déplacer jusqu’au départ de chaque course, attendre si nécessaire,
puis transporter le passager jusqu’à sa destination.

Le problème combine :

- L’affectation des courses aux véhicules.
- L’ordonnancement des courses.
- Le respect des fenêtres temporelles.
- La gestion des déplacements à vide.
- La maximisation des distances rémunérées et des bonus.

### Modèle de déplacement

Les intersections sont identifiées par leurs coordonnées `(r, c)`.

Le temps de déplacement correspond à la distance de Manhattan :

`distance = |r1 - r2| + |c1 - c2|`

Tous les véhicules commencent à l’intersection **(0, 0)**.

### Caractéristiques d’une course

| Paramètre | Description |
|---|---|
| `(a, b)` | Point de départ |
| `(x, y)` | Point d’arrivée |
| `s` | Date de départ au plus tôt |
| `f` | Date de fin au plus tard |

Une course rapporte :

- Sa distance si elle est terminée à temps.
- Un bonus `B` si elle commence exactement à sa date de départ au plus tôt, dans les conditions du scoring.

### Contraintes principales

- Une course peut être affectée à **un seul véhicule au maximum**.
- Il n’est pas obligatoire de réaliser toutes les courses.
- Un véhicule réalise ses courses successivement.
- Les déplacements entre les courses doivent être pris en compte.
- Une course ne commence pas avant sa date de départ au plus tôt.
- Les courses retenues doivent respecter leur date limite et l’horizon `T`.
- Plusieurs véhicules peuvent occuper une même intersection.

## 2. 📋 Travail demandé

Le projet est organisé en trois phases.

### Phase 1 - Formalisation mathématique

Construire une formulation en **programmation linéaire en nombres entiers — PLNE** :

- Définir les ensembles et les paramètres.
- Définir les variables de décision.
- Modéliser l’affectation et l’ordre des courses.
- Intégrer les contraintes temporelles.
- Représenter les courses non retenues.
- Définir le calcul du score.
- Formuler l’objectif de maximisation.

### Phase 2 - Conception des méthodes approchées

- Concevoir plusieurs heuristiques constructives.
- Comparer leurs règles de sélection.
- Choisir une méthode pour construire une solution initiale.
- Proposer une métaheuristique d’amélioration.
- Définir les voisinages et les contrôles de faisabilité.
- Identifier les paramètres à tester.

### Phase 3 - Implémentation et tests

- Implémenter les méthodes retenues.
- Lire les instances.
- Construire et améliorer les solutions.
- Mesurer les scores et les temps d’exécution.
- Comparer les résultats avant et après amélioration.
- Exporter les affectations au format demandé.

## 3. 🗂️ Format des instances et des solutions

### Entrée

La première ligne contient :

`R C F N B T`

| Paramètre | Signification |
|---|---|
| `R` | Nombre de lignes de la grille |
| `C` | Nombre de colonnes |
| `F` | Nombre de véhicules |
| `N` | Nombre de courses |
| `B` | Bonus de départ à l’heure |
| `T` | Horizon de simulation |

Les `N` lignes suivantes décrivent les courses :

`a b x y s f`

### Sortie

Une ligne est produite pour chaque véhicule :

`M ride_1 ride_2 ... ride_M`

`M` est le nombre de courses affectées au véhicule.
Les identifiants sont donnés dans leur ordre d’exécution.

## 4. 🧩 Formalisation étudiée

Les documents de phase 1 présentent une première formulation,
puis une version révisée dans le rapport regroupant les phases 1 et 2.

La modélisation étudiée comprend notamment :

- Des variables d’affectation ou de rejet des courses.
- Des variables de chaînage entre courses.
- Des dates de début et de fin.
- Des rangs pour représenter les séquences.
- Des variables de contrôle du score et du bonus.
- Des contraintes visant à empêcher les circuits parasites.

L’objectif est de maximiser la somme des scores des courses.

Cette partie constitue le cadre de modélisation du projet.
Les documents fournis ne présentent pas de résultats de résolution
par un solveur PLNE ni de preuve d’optimalité.

## 5. ⚙️ Heuristiques proposées en phase 2

### Heuristique 1 - Plus proche course faisable

Choisir une course réalisable dont le départ est le plus proche
du véhicule.

**Avantage :** simplicité.

**Limite :** la proximité ne reflète pas nécessairement
le gain de la course.

### Heuristique 2 - Meilleur profit immédiat

Choisir une course selon le rapport :

`gain / (1 + distance d’accès)`

Le gain prend en compte la distance de la course et le bonus éventuel.

**Avantage :** combine gain et coût d’accès.

**Limite :** ne pénalise pas explicitement l’attente.

### Heuristique 3 - Glouton pondéré

Utiliser le critère :

`distance de la course + bonus - α × déplacement à vide - β × attente`

Cette règle cherche un compromis entre :

- Le gain de la course.
- Le bonus de départ.
- La distance nécessaire pour rejoindre le départ.
- Le temps d’attente.

Elle est retenue comme base de construction des solutions.

### Contrôle de faisabilité

Pour chaque affectation envisagée :

1. Calculer le déplacement jusqu’au départ.
2. Calculer l’heure d’arrivée.
3. Attendre si le véhicule arrive avant la date autorisée.
4. Calculer la fin de la course.
5. Vérifier la date limite et l’horizon de simulation.

## 6. 🔄 Métaheuristiques : conception et implémentation

### Méthode proposée en phase 2 — Recuit simulé

Le rapport de conception propose un recuit simulé.

Les voisinages envisagés comprennent :

- Échanges de courses au sein d’un véhicule.
- Échanges entre véhicules.
- Déplacement d’une course vers un autre véhicule.
- Insertion ou suppression de courses.

Une solution moins bonne peut être acceptée avec une probabilité
dépendant de la température pour favoriser l’exploration.

### Méthode rapportée en phase 3 — LNS

Le rapport d’implémentation indique une approche
**Large Neighborhood Search — LNS**, fondée sur le principe
**destroy / repair** :

1. Construire une solution initiale par multi-start glouton.
2. Retirer une partie des courses.
3. Reconstruire les affectations par réinsertion.
4. Évaluer les solutions obtenues.
5. Exporter la solution finale.

La méthode utilisée dans le benchmark diffère donc du recuit simulé
initialement proposé.

## 7. 📊 Instances testées

| Paramètre | Instance 01 | Instance 02 |
|---|---:|---:|
| Lignes de la grille | 800 | 10 000 |
| Colonnes de la grille | 1 000 | 10 000 |
| Véhicules | 100 | 400 |
| Courses | 300 | 10 000 |
| Bonus | 25 | 2 |
| Horizon | 25 000 | 50 000 |

## 8. 📈 Résultats du benchmark

Les résultats suivants sont rapportés dans le rapport de phase 3
et le fichier CSV associé.

| Instance | Score initial | Score final | Courses initiales | Courses finales | Temps rapporté |
|---|---:|---:|---:|---:|---:|
| 01 | 176 852 | 176 852 | 294 | 294 | 2,13 s |
| 02 | 4 692 863 | 5 946 305 | 845 | 1 704 | 5,056 s |

### Instance 01

- **294 courses sur 300** sont retenues, soit **98 %**.
- Le score reste identique après la phase d’amélioration.
- Aucun gain n’est observé sur cette exécution.

L’absence d’amélioration ne prouve pas que la solution est optimale.

### Instance 02

- Le score augmente de **1 253 442 points**.
- Cela représente une progression d’environ **26,71 %**.
- Le nombre de courses retenues passe de **845 à 1 704**.
- La solution finale retient **17,04 %** des 10 000 courses.

La phase d’amélioration apporte donc un gain important
sur cette instance.

### Interprétation

Le nombre de courses réalisées et le score mesurent deux aspects
différents : les courses ont des distances différentes
et peuvent rapporter un bonus.

L’objectif est de maximiser le score, pas uniquement
le nombre de courses affectées.

## 9. 🔎 Limites de l’évaluation

- Le benchmark fourni présente deux instances.
- Les résultats ne constituent pas une preuve d’optimalité.
- Les paramètres complets et les graines aléatoires ne sont pas précisés dans le rapport de benchmark.
- L’environnement matériel n’est pas documenté.
- Les temps rapportés ne suffisent pas à comparer les méthodes sur d’autres machines.
- Plusieurs répétitions seraient nécessaires pour évaluer la stabilité des résultats.
- La faisabilité des solutions doit être contrôlée à partir des fichiers de sortie.

## 10. 🚀 Perspectives

- Comparer expérimentalement les trois heuristiques constructives.
- Étudier les poids de déplacement et d’attente.
- Comparer LNS et recuit simulé.
- Répéter les tests avec plusieurs graines aléatoires.
- Mesurer séparément les temps de construction et d’amélioration.
- Tester davantage d’instances.
- Comparer les solutions à une résolution exacte sur de petites instances.
- Ajouter un validateur indépendant des solutions.
- Documenter les instructions d’exécution.

## 11. 🛠️ Compétences mobilisées

- Modélisation mathématique.
- Programmation linéaire en nombres entiers.
- Affectation et ordonnancement.
- Gestion de fenêtres temporelles.
- Heuristiques constructives.
- Recuit simulé : conception.
- Large Neighborhood Search : approche rapportée au benchmark.
- Analyse expérimentale.
- Optimisation de décisions sous contraintes.

## 12. 👥 Auteurs

**Yannick ASSI et Hoang Viet Vu**

Master MIASHS — Université Catholique de l’Ouest  
Année universitaire 2025–2026
