# Système de blocs

Composition modulaire de pages avec contrôle total du rendu

Le système de blocs de Blackcube permet de composer des pages de manière flexible tout en gardant un contrôle total sur le rendu. Chaque contenu (Node, Composite, Tag, Category) peut contenir plusieurs blocs de différents types définis par le développeur.

Concrètement, un bloc est une instance d'un BlocType : le BlocType définit la structure des données via JSON Schema, et le bloc contient les données réelles. Cette architecture permet une grande flexibilité tout en maintenant la cohérence et la validation automatique du contenu. Chaque Type définit quels BlocTypes sont autorisés, offrant ainsi un contrôle précis sur la composition des pages.

## Qu'est-ce qu'un bloc ?

Un **bloc** est une instance concrète d'un **BlocType**. C'est un morceau de contenu structuré qui peut être ajouté à un élément.

### Analogie avec la construction

Si on compare à la construction :

- **BlocType** = Le plan d'un module (ex: "fenêtre standard 120x80")
- **Bloc** = Une fenêtre installée dans une maison spécifique

Conséquences :

- Un bloc appartient à **un seul contenu**
- Créer 10 pages avec le BlocType "heading" = créer 10 blocs différents
- Modifier un bloc n'affecte **que ce contenu**
- **Pas de blocs partagés** entre contenus

## Architecture de stockage

### Tables impliquées

```sql
-- Table des blocs (contenu réel)
CREATE TABLE blocs (
    id          BIGINT AUTO_INCREMENT PRIMARY KEY,
    blocTypeId  BIGINT NOT NULL,              -- Référence au BlocType
    active      TINYINT(1) DEFAULT 0,         -- Validité du bloc
    data        BLOB,                         -- JSON contenant les données
    dateCreate  DATETIME NOT NULL,
    dateUpdate  DATETIME NULL,
    FOREIGN KEY (blocTypeId) REFERENCES blocTypes(id)
);

-- Tables de liaison (many-to-many)
CREATE TABLE nodes_blocs (
    nodeId      BIGINT NOT NULL,
    blocId      BIGINT NOT NULL,
    `order`     INT NOT NULL,                 -- Ordre d'affichage
    dateCreate  DATETIME NOT NULL,
    dateUpdate  DATETIME NULL,
    PRIMARY KEY (nodeId, blocId),
    FOREIGN KEY (nodeId) REFERENCES nodes(id),
    FOREIGN KEY (blocId) REFERENCES blocs(id)
);

-- Tables similaires pour composites_blocs, tags_blocs, categories_blocs
```

### Pourquoi cette architecture ?

Séparation des responsabilités :

1. **Table blocs** : Stocke le contenu réel (indépendant de l'élément)
2. **Tables de liaison** : Associent les blocs aux contenus et gèrent l'ordre

Avantages :

- Contenus homogènes
- Requêtes optimisées
- Gestion simple de l'ordre
- Suppression en cascade facilitée

## Cycle de vie d'un bloc

### Création dans le backoffice

> **Attention**
> 
> Le code suivant est donné à titre d'illustration.

Étapes :

1. Le contributeur édite un Node (par exemple)
2. Sélectionne un BlocType dans la liste déroulante
3. Clique sur **"+"** pour ajouter le bloc
4. Le formulaire s'affiche avec les champs définis par le JSON Schema
5. Le contributeur remplit les champs
6. Clique sur **"Sauvegarder"**

Ce qui se passe en base :

```sql
-- 1. Insertion dans blocs
INSERT INTO blocs (blocTypeId, active, data)
VALUES (5, 0, '{}');  -- active = 0 car pas encore valide

-- 2. Liaison avec le node
INSERT INTO nodes_blocs (nodeId, blocId, `order`)
VALUES (42, 123, 1);  -- ordre = 1 (premier bloc)
```

### Validation automatique

Blackcube valide le bloc selon le JSON Schema :

Si valide :

```sql
UPDATE blocs SET active = 1, data = '{"title":"Mon titre", ...}'
WHERE id = 123;
```

Si invalide :

- Le bloc reste active = 0
- Les erreurs sont affichées dans le backoffice

Le contributeur doit corriger avant de pouvoir sauvegarder.

> **Important**
> 
> Un bloc invalide (`active = 0`) n'apparaît **jamais** sur le site public, même si le contenu parent est actif.

### Affichage sur le site

Dans votre controller :

```php
public function actionView()
{
    $node = $this->element;
    
    // Les blocs sont automatiquement :
    // - Filtrés (active = 1 uniquement)
    // - Triés (par order ASC)
    // - Chargés avec leur BlocType
    foreach ($node->blocs as $bloc) {
        // $bloc est un ElasticModel avec les propriétés du JSON Schema
        echo $bloc->title;
        echo $bloc->content;
    }
}
```

Requête SQL générée :

```sql
SELECT b.*, bt.* 
FROM blocs b
INNER JOIN nodes_blocs nb ON nb.blocId = b.id
INNER JOIN blocTypes bt ON bt.id = b.blocTypeId
WHERE nb.nodeId = 42
  AND b.active = 1
ORDER BY nb.order ASC;
```

Une seule requête. Tous les blocs. Déjà triés.

## Gestion de l'ordre

### Dans le backoffice

Les blocs s'affichent dans l'ordre où ils ont été créés. L'interface permet de réorganiser les blocs via des contrôles dédiés.

Fonctionnement :

Ordre initial :

- 1. Bloc Heading
- 2. Bloc Text
- 3. Bloc Image

Après réorganisation :

- 1. Bloc Heading
- 2. Bloc Image ← Monte
- 3. Bloc Text ← Descend

### Numérotation automatique

À la création :

```sql
-- Nouveau bloc ajouté après 3 blocs existants
INSERT INTO nodes_blocs (nodeId, blocId, `order`)
VALUES (42, 124, 4);  -- order = 4 automatiquement
```

Après suppression et sauvegarde :

Ordre initial :

- 1. Bloc A
- 2. Bloc B
- 3. Bloc C

Suppression de Bloc B :

- 1. Bloc A
- 3. Bloc C ← Temporairement

Lors de la sauvegarde du contenu :

- 1. Bloc A ← Recalculé
- 2. Bloc C ← Recalculé

Pourquoi recalculer ?

- Ordre toujours cohérent : 1, 2, 3, 4... (pas de trous)
- Facilite l'affichage et le debugging
- Évite les problèmes de numérotation

> **Important**
> 
> Le recalcul se fait automatiquement lors de la sauvegarde du contenu dans le backoffice. Vous n'avez rien à gérer.

## Relation Type → BlocType

### Configuration dans le Type

Chaque Type définit quels BlocTypes sont autorisés.

Exemple :

Type "page-standard"

BlocTypes autorisés :

- ☑ heading
- ☑ text-block
- ☑ image-gallery
- ☐ video-embed
- ☐ contact-form

Résultat : Quand un contributeur crée un Node avec ce Type, il ne peut ajouter **que** les BlocTypes cochés.

### Table de liaison

```sql
CREATE TABLE types_blocTypes (
    typeId      BIGINT NOT NULL,
    blocTypeId  BIGINT NOT NULL,
    allowed     TINYINT(1) DEFAULT 1,         -- 1 = autorisé, 0 = interdit
    dateCreate  DATETIME NOT NULL,
    dateUpdate  DATETIME NULL,
    PRIMARY KEY (typeId, blocTypeId),
    FOREIGN KEY (typeId) REFERENCES types(id),
    FOREIGN KEY (blocTypeId) REFERENCES blocTypes(id)
);
```

Requête pour obtenir les BlocTypes autorisés :

```sql
SELECT bt.*
FROM blocTypes bt
INNER JOIN types_blocTypes tbt ON tbt.blocTypeId = bt.id
WHERE tbt.typeId = 1
  AND tbt.allowed = 1;
```

### Pourquoi cette contrainte ?

Avantages :

1. **Contrôle du design** : Éviter les "sapins de Noël" où tout et n'importe quoi est ajouté
2. **Cohérence** : Une page "Actualité" a des blocs différents d'une page "Contact"
3. **Simplification** : Le contributeur voit uniquement les blocs pertinents
4. **Maintenance** : Désactiver un BlocType pour un Type n'affecte pas les autres

Exemple concret :

```
Type "landing-page"
→ BlocTypes : hero, features, testimonials, cta

Type "blog-post"
→ BlocTypes : heading, text, image, quote, code

Type "product-page"
→ BlocTypes : product-info, gallery, specs, related-products
```

Chaque Type a des blocs adaptés à son usage.

## Manipulation dans le code

### Récupérer les blocs

Automatique via la relation :

```php
$node = $this->element;

// Relation définie dans Node::getBlocs()
foreach ($node->blocs as $bloc) {
    // Accès aux propriétés ElasticModel
    echo $bloc->title;
    echo $bloc->content;
    echo $bloc->image;
}
```

Les blocs sont :

- Filtrés (`active = 1`)
- Triés (`order ASC`)
- Chargés avec leur BlocType
- Prêts à l'emploi

### Filtrer par BlocType

```php
// Récupérer uniquement les blocs d'un type spécifique
foreach ($node->blocs as $bloc) {
    if ($bloc->blocType->name === 'heading') {
        // Traitement spécifique pour les headings
    }
}
```

## Rendu des blocs

### Approche 1 : Rendu simple

```php
// views/page/view.php
<?php 
use blackcube\core\helpers\Html;
use blackcube\core\helpers\Quill;
?>

<?php foreach ($node->blocs as $bloc): ?>
    <div class="bloc bloc-<?= $bloc->blocType->name ?>">
        <h2><?= Html::encode($bloc->title) ?></h2>
        <div><?= Quill::cleanHtml($bloc->content) ?></div>
    </div>
<?php endforeach; ?>
```

### Approche 2 : Vues partielles

```php
// views/page/view.php
<?php foreach ($node->blocs as $bloc): ?>
    <?= $this->render('_bloc-' . $bloc->blocType->name, [
        'bloc' => $bloc
    ]) ?>
<?php endforeach; ?>
```

Avantage : Code organisé, maintenance facile.

## Bonnes pratiques

### À faire

Nommer clairement les BlocTypes :

- `heading`, `text-block`, `image-gallery`, `cta`, `testimonial`
- Éviter : `bloc1`, `bloc2`, `bloc3`

Réutiliser les BlocTypes :

- Un seul `heading` réutilisé dans plusieurs Types
- Éviter : `heading-page`, `heading-article`, `heading-landing`

### À éviter

Blocs trop complexes :

- Éviter : Un bloc avec 30 champs
- Préférer : Plusieurs blocs simples et réutilisables

## Conclusion

### Le système de blocs en résumé

1. **Modulaire** : Composez des pages bloc par bloc
2. **Flexible** : Chaque Type définit ses blocs autorisés
3. **Isolé** : Un bloc = un contenu (pas de partage)
4. **Ordonné** : Gestion simple de l'ordre
5. **Validé** : Validation automatique via JSON Schema
6. **Filtré** : Seuls les blocs valides sont affichés

### Le trio complet

Type + BlocType + Bloc = Page personnalisée

- **Type** : Définit le controller/action et les blocs autorisés
- **BlocType** : Définit la structure des données (JSON Schema)
- **Bloc** : Instance concrète avec les données réelles

Le développeur garde le contrôle total. Le contributeur crée du contenu riche sans contrainte technique.