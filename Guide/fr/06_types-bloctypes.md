# Types et BlocTypes

Les concepts essentiels de Blackcube : Types et BlocTypes

Blackcube repose sur quelques concepts clés qui, une fois maîtrisés, permettent de construire n'importe quel type de site. Le plus important : bien comprendre la notion Type vs BlocType, la séparation entre "comment afficher" et "quoi saisir".

Cette séparation fondamentale permet au développeur de garder le contrôle total du front-end tout en offrant une flexibilité maximale aux contributeurs. Le Type définit la logique d'affichage via un controller Yii2, tandis que le BlocType définit la structure des données saisies via JSON Schema. Les deux sont indépendants mais connectés : un Type autorise certains BlocTypes, permettant ainsi de contrôler précisément quels types de contenu peuvent utiliser quels champs.

> **Info**
>
> Le développeur définit la structure : crée les Types (controllers/actions), crée les BlocTypes (champs disponibles), lie Types et BlocTypes, puis code les vues et la logique métier.

## Type vs BlocType : la distinction essentielle

Point de blocage fréquent pour les développeurs venant de WordPress ou Drupal : bien comprendre cette distinction est essentiel.

### Type : "Comment afficher ?"

Un Type définit comment un contenu sera rendu dans le front-end.

Concrètement, un Type c'est :

1. Un controller/action Yii2 (ex: PageController::actionView())
2. Des contraintes sur les entités autorisées (Node, Composite, Tag, Category)
3. Une liste de BlocTypes autorisés pour ce Type

Exemple : Type "page-standard"

Configuration dans le backoffice :

```text
Nom : page-standard
Route : page/view
Utilisable pour : Node uniquement
BlocTypes autorisés : heading, text-editor, image-gallery
```

### BlocType : "Quoi saisir ?"

Un BlocType définit quels champs sont disponibles pour la saisie de contenu.

Concrètement, un BlocType c'est :

1. Un JSON Schema qui décrit la structure des données
2. Une validation automatique des données saisies
3. Des propriétés accessibles comme un ActiveRecord Yii2

Exemple : BlocType "text-block"

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Titre"
    },
    "content": {
      "type": "string",
      "format": "wysiwyg",
      "title": "Contenu"
    }
  },
  "required": ["title", "content"]
}
```

### La relation Type ↔ BlocType

Un Type autorise certains BlocTypes. Cette relation donne la flexibilité tout en gardant le contrôle.

Représentation visuelle :

```text
Type "page-standard"
├── Autorise : text-block
├── Autorise : image-gallery
└── Autorise : video-embed

Type "actualite"
├── Autorise : text-block
└── Autorise : image-gallery
(pas de video-embed pour les actualités)
```

Résultat concret :

1. Le contributeur qui crée une "page-standard" peut ajouter des vidéos
2. Le contributeur qui crée une "actualité" ne peut PAS ajouter de vidéos
3. Le même BlocType text-block est réutilisé dans les deux Types

### Analogie

Analogie avec un restaurant :

1. **Type** = Type de plat (Entrée, Plat, Dessert)
2. **BlocType** = Ingrédients disponibles (Tomate, Poulet, Chocolat)
3. **Relation** = Quels ingrédients vont dans quel type de plat

Vous ne mettez pas du chocolat dans une entrée salée, même si le chocolat existe dans votre cuisine.

## Créer vos premiers Types

> **Important**
>
> Créer au moins un Type avant de pouvoir créer du contenu. Sans Type, impossible de créer une page, un article ou tout autre contenu.

Les Types sont la fondation de Blackcube.

Un Type définit :

1. Le controller/action qui va rendre le contenu
2. Les BlocTypes disponibles pour ce type de contenu
3. Si le Type peut être utilisé pour des Nodes, Composites, Tags ou Categories

Exemple : Type "Page Standard"

Dans le backoffice :

1. Allez dans Paramètres → Types
2. Créez un nouveau Type :
   - Nom : page-standard
   - Route : page/view (correspond à PageController::actionView())
   - Node autorisé : ✓ Oui
   - Composite autorisé : ✗ Non
   - Tag autorisé : ✗ Non
   - Category autorisé : ✗ Non
3. Sauvegardez

Créer ensuite le controller correspondant :

```php
<?php
namespace app\controllers;

use blackcube\core\components\BlackcubeController;

class PageController extends BlackcubeController
{
    public function actionView()
    {
        $node = $this->element; // Injecté automatiquement
        
        return $this->render('view', [
            'node' => $node,
        ]);
    }
}
```

## Créer vos premiers BlocTypes

Les BlocTypes définissent les champs que vos contributeurs pourront remplir.

Exemple : BlocType "Texte avec titre"

Dans le backoffice :

1. Allez dans Paramètres → BlocTypes
2. Créez un nouveau BlocType :
   - Nom : text-block
   - Template : JSON Schema définissant les champs
   - Vue (optionnel) : Nom de la vue pour le rendu

Template JSON Schema :

```json
{
    "type": "object",
    "properties": {
        "title": {
            "type": "string",
            "title": "Titre"
        },
        "content": {
            "type": "string",
            "format": "wysiwyg",
            "options": {
                "theme": "snow",
                "modules": {
                    "toolbar": [
                        [
                            "bold",
                            "italic",
                            "underline"
                        ],
                        [
                            {
                                "list": "bullet"
                            }
                        ],
                        [
                            "link"
                        ]
                    ]
                },
                "formats": [
                    "bold",
                    "italic",
                    "link",
                    "underline",
                    "list"
                ]
            },
            "title": "Contenu",
            "description": "Bloc de contenu"
        }
    },
    "required": ["title", "content"]
}
```

Explication du template :

1. `type: "object"` = Le BlocType contient plusieurs propriétés
2. `properties` = Définition de chaque champ
3. `title` = Champ texte simple obligatoire
4. `content` = Éditeur WYSIWYG avec options de formatage
5. `required` = Liste des champs obligatoires

Sauvegardez.

## Lier Types et BlocTypes

Une fois vos Types et BlocTypes créés, définir quels BlocTypes sont disponibles pour chaque Type.

Dans le backoffice :

1. Allez dans Paramètres → Types
2. Éditez le Type page-standard
3. Dans la section BlocTypes autorisés, cochez text-block
4. Sauvegardez

> **Info**
>
> Vous pouvez aussi faire l'inverse depuis Paramètres → BlocTypes en éditant un BlocType et en cochant les Types autorisés.

Vous pouvez maintenant créer du contenu.

## Points clés à retenir

1. **Type** = Comment afficher (controller/action)
2. **BlocType** = Quoi saisir (champs/données)
3. Un Type autorise certains BlocTypes
4. Cette séparation permet flexibilité et contrôle
5. Sans Type, pas de contenu possible
6. Les BlocTypes sont réutilisables entre différents Types
7. La création des Types et BlocTypes se fait dans le backoffice
8. Le code des controllers doit être créé manuellement