# Hazel Tree

Arborescence ultra performante

Hazel Tree est le système d'arborescence utilisé par Blackcube pour organiser les contenus de manière hiérarchique : menus, fils d'Ariane, navigation dans les rubriques, etc. Il repose sur une approche mathématique qui privilégie les performances en lecture plutôt qu'en écriture.

Concrètement, Blackcube utilise une structure basée sur les fractions rationnelles plutôt que l'approche classique par références parent-enfant.

> **Attribution**
>
> Hazel Tree est une implémentation des travaux de recherche de **Dan Hazel** publiés en 2008 : "Using rational numbers to key nested sets". Cette approche innovante résout les limitations des Nested Sets traditionnels en utilisant des fractions rationnelles plutôt que des entiers, rendant les opérations d'insertion beaucoup plus efficaces.
>
> [Lire le papier original sur arXiv](https://arxiv.org/abs/0806.3115)

## Le problème des arbres classiques

### Arbres traditionnels (Adjacency List)

Les arbres classiques stockent simplement une référence vers le parent de chaque élément. Cette approche présente des avantages et inconvénients :

1. Insertion simple et rapide
2. Lecture nécessitant des requêtes récursives multiples
3. Cache obligatoire pour obtenir des performances acceptables

### Hazel Tree

L'approche Hazel Tree inverse cette logique en optimisant la lecture au détriment de l'écriture :

1. Insertion légèrement plus complexe, mais ces opérations sont rares
2. Lecture ultra-rapide avec une seule requête SQL
3. Pas de cache nécessaire pour les opérations de lecture

## Performance

### Afficher un menu à 3 niveaux

Avec une approche classique, il faut environ 50 requêtes SQL pour charger tous les éléments et leurs relations. Avec Hazel Tree, une seule requête suffit.

### Générer un fil d'Ariane de 5 niveaux

L'approche classique nécessite 5 requêtes SQL consécutives pour remonter la hiérarchie. Hazel Tree récupère tout en une seule requête.

### Récupérer tous les descendants

Avec les arbres classiques, il faut des requêtes récursives dont le nombre dépend de la profondeur de l'arbre. Hazel Tree récupère l'ensemble de la branche en une seule requête, quelle que soit la profondeur.

## En pratique

Vous bénéficiez automatiquement de Hazel Tree lorsque vous utilisez les fonctionnalités suivantes :

1. Affichage d'un menu de navigation
2. Génération d'un fil d'Ariane
3. Liste des enfants d'une rubrique
4. Récupération d'une branche complète de l'arborescence

Aucune action spécifique n'est requise de votre part. Blackcube gère automatiquement la structure Hazel Tree en arrière-plan lors de la création, modification ou suppression de nodes.

## Points clés à retenir

1. **Optimisé pour la lecture** : Hazel Tree est conçu pour les sites où l'affichage de contenus hiérarchiques est fréquent. Chaque visiteur qui navigue sur votre site bénéficie de performances optimales.
2. **Gestion automatique** : Vous n'avez rien à configurer ni à gérer. Blackcube s'occupe automatiquement de maintenir la structure Hazel Tree cohérente.

> **Attention**
>
> L'ajout, le déplacement ou la suppression de nodes est légèrement plus coûteux en ressources qu'avec une approche classique. Cependant, ces opérations d'administration sont rares comparées aux millions d'affichages que génèrent vos visiteurs.