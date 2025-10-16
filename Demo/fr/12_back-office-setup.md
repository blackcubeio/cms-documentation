# Configuration Back-Office
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Page Contact

---

## Objectif

Configurer le back-office Blackcube en créant les 9 BlocTypes, les 5 Types, les 3 templates d'administration personnalisés, les 8 paramètres et les 4 menus de navigation.

---

## Créer les BlocTypes

**Accès** : Menu latéral → **Paramètres → Types de bloc** → Bouton **Ajouter un type de bloc**

> **Attention**
> Note les IDs de chaque BlocType après création. Ces IDs seront nécessaires pour créer les Paramètres.

### 01. Hero (H1)

**Nom** : `01. Hero (H1)`  
**Vue** : `bloc_hero`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "lang": {
      "type": "string",
      "title": "Associated content",
      "description": "Content for the other language"
    },
    "title": {
      "type": "string",
      "title": "H1 title",
      "description": "Hero title"
    },
    "breadcrumbTitle": {
      "type": "string",
      "title": "Breadcrumb title",
      "description": "Title for the breadcrumb, if not set title will be used"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}],
            ["link"]
          ]
        },
        "formats": ["bold", "italic", "link", "underline", "list"]
      },
      "title": "Description",
      "description": "Not too long, it's for the Hero"
    },
    "image": {
      "type": "string",
      "format": "file",
      "fileType": "png,jpg",
      "title": "Image",
      "description": "Image should be 1200x*"
    },
    "ctaTitle": {
      "type": "string",
      "title": "CTA title",
      "description": "Title of the primary CTA"
    },
    "ctaTarget": {
      "type": "string",
      "title": "Target content",
      "description": "Select the content/page which will be the target of the CTA"
    },
    "ctaSecondaryTitle": {
      "type": "string",
      "title": "CTA title",
      "description": "Title of the secondary CTA"
    },
    "ctaSecondaryTarget": {
      "type": "string",
      "title": "Target content",
      "description": "Select the content/page which will be the target of the CTA"
    }
  },
  "required": ["title", "description"]
}
```

---

### 02. Feature (H3)

**Nom** : `02. Feature (H3)`  
**Vue** : `bloc_feature`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "H3 title",
      "description": "Title for th Hero"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}]
          ]
        },
        "formats": ["bold", "italic", "underline", "list"]
      },
      "title": "Description",
      "description": "Not too long, it's for the feature"
    },
    "icon": {
      "type": "string",
      "format": "file",
      "fileType": "svg",
      "title": "Svg icon",
      "description": "Get the icon from Heroicon"
    }
  },
  "required": ["icon", "title", "description"]
}
```

---

### 03. Titre (H2 - H4)

**Nom** : `03. Titre (H2 - H4)`  
**Vue** : `bloc_title`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "hn": {
      "type": "string",
      "multiple": false,
      "format": "dropdownlist",
      "items": [
        {"title": "H2", "value": "h2"},
        {"title": "H3", "value": "h3"},
        {"title": "H4", "value": "h4"}
      ],
      "title": "Niveau",
      "description": "Niveau du titre"
    },
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Intermediate title"
    }
  },
  "required": ["title", "hn"]
}
```

---

### 04. Card

**Nom** : `04. Card`  
**Vue** : `bloc_card`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Titre",
      "description": "Titre de la section (h1)"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"]
          ]
        },
        "formats": ["bold", "italic", "underline"]
      },
      "title": "Description",
      "description": "Description de la section"
    },
    "icon": {
      "type": "string",
      "format": "file",
      "fileType": "svg",
      "title": "Icône",
      "description": "Icône svg"
    },
    "link": {
      "type": "string",
      "title": "Cta",
      "description": "Titre du lien"
    },
    "linkRoute": {
      "type": "string",
      "title": "Cible",
      "description": "Elément cible du lien"
    },
    "linkUrl": {
      "type": "string",
      "title": "Cible (URL)",
      "description": "Lien brut (sera pris si il n'ya pas de route)"
    }
  },
  "required": ["title", "description", "icon"]
}
```

---

### 04. Content

**Nom** : `04. Content`  
**Vue** : `bloc_content`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "content": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}],
            ["link"]
          ]
        },
        "formats": ["bold", "italic", "link", "underline", "list"]
      },
      "title": "Content",
      "description": "Content bloc"
    }
  },
  "required": ["content"]
}
```

---

### 04. Cta

**Nom** : `04. Cta`  
**Vue** : `bloc_cta`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Title for the CTA"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}]
          ]
        },
        "formats": ["bold", "italic", "underline", "list"]
      },
      "title": "Description",
      "description": "Not too long, it's for the CTA"
    },
    "ctaTitle": {
      "type": "string",
      "title": "CTA title",
      "description": "Title of the primary CTA"
    },
    "ctaTarget": {
      "type": "string",
      "title": "Target content",
      "description": "Select the content/page which will be the target of the CTA"
    }
  },
  "required": ["title", "description", "ctaTitle", "ctaTarget"]
}
```

---

### 05. Info

**Nom** : `05. Info`  
**Vue** : `bloc_info`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "type": {
      "title": "Type",
      "description": "Kind of alert",
      "type": "string",
      "format": "dropdownlist",
      "items": [
        {"title": "Info", "value": "info"},
        {"title": "Warning", "value": "warning"},
        {"title": "Error", "value": "error"}
      ]
    },
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Title of the infobox"
    },
    "content": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}]
          ]
        },
        "formats": ["bold", "italic", "underline", "list"]
      },
      "title": "Content",
      "description": "Content of the infobox"
    }
  },
  "required": ["type", "title", "content"]
}
```

---

### 06. Code

**Nom** : `06. Code`  
**Vue** : `bloc_code`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "language": {
      "title": "Langage",
      "description": "Langage used for display",
      "type": "string",
      "format": "dropdownlist",
      "items": [
        {"title": "PHP", "value": "php"},
        {"title": "SQL", "value": "sql"},
        {"title": "JSON", "value": "json"},
        {"title": "Typescript", "value": "typescript"},
        {"title": "HTML", "value": "html"},
        {"title": "SCSS", "value": "scss"},
        {"title": "Javascript", "value": "javascript"},
        {"title": "CSS", "value": "css"},
        {"title": "Bash", "value": "bash"},
        {"title": "Texte", "value": "text"},
        {"title": "GraphQL", "value": "graphql"}
      ]
    },
    "code": {
      "type": "string",
      "format": "textarea",
      "title": "Code",
      "description": "Bloc of code"
    }
  },
  "required": ["language", "code"]
}
```

---

### 07. Contact

**Nom** : `07. Contact`  
**Vue** : `bloc_contact`

**JSON Schema** :

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Titre",
      "description": "Titre de la section (h2)"
    },
    "successTitle": {
      "type": "string",
      "title": "Titre en cas de succès",
      "description": "Titre en cas de succès de la section (h2)"
    },
    "success": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"]
          ]
        },
        "formats": ["bold", "italic", "underline"]
      },
      "title": "Succès",
      "description": "Message en cas de succès"
    }
  },
  "required": ["title", "successTitle", "success"]
}
```

---

## Créer les Types

**Accès** : Menu latéral → **Paramètres → Types** → Bouton **Ajouter un type**

### Type Redirect Lang

**Nom** : `Redirect Lang`  
**Route** : Sélectionner `cms/redirect-language`

**Autorisations** :
- Rubrique : Coché
- Article : Non coché
- Catégorie : Non coché
- Tag : Non coché

**BlocTypes autorisés** : Aucun

---

### Type Home

**Nom** : `Home`  
**Route** : Sélectionner `cms/home`

**Autorisations** :
- Rubrique : Coché
- Article : Non coché
- Catégorie : Non coché
- Tag : Non coché

**BlocTypes autorisés** :
- 01. Hero (H1)
- 02. Feature (H3)
- 04. Cta

---

### Type Documentation

**Nom** : `Documentation`  
**Route** : Sélectionner `cms/documentation`

**Autorisations** :
- Rubrique : Non coché
- Article : Coché
- Catégorie : Non coché
- Tag : Non coché

**BlocTypes autorisés** :
- 01. Hero (H1)
- 03. Titre (H2 - H4)
- 04. Content
- 04. Cta
- 05. Info
- 06. Code

---

### Type Contact

**Nom** : `Contact`  
**Route** : Sélectionner `cms/contact`

**Autorisations** :
- Rubrique : Non coché
- Article : Coché
- Catégorie : Non coché
- Tag : Non coché

**BlocTypes autorisés** :
- 01. Hero (H1)
- 04. Card
- 05. Info
- 07. Contact

---

### Type Redirect First Composite

**Nom** : `Redirect First Composite`  
**Route** : Sélectionner `cms/redirect-first-composite`

**Autorisations** :
- Rubrique : Coché
- Article : Non coché
- Catégorie : Non coché
- Tag : Non coché

**BlocTypes autorisés** : Aucun

---

## Créer les Templates d'Administration

Les templates d'administration personnalisent l'interface de saisie dans le back-office pour certains BlocTypes. Ils permettent de remplacer des champs texte par des dropdowns intelligents.

> **Info**
> `CmsHelper::getTargets()` retourne la liste de tous les Nodes et Composites du site, groupés par type (Rubriques/Articles), pour alimenter les dropdowns de sélection.

### Template bloc_hero.php

**Rôle** : Remplace les champs `lang`, `ctaTarget` et `ctaSecondaryTarget` par des dropdowns de sélection.

**Fichier** : `common/admin/bloc_hero.php`

```php
<?php
/**
 * bloc_hero.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $element \blackcube\core\models\Tag|\blackcube\core\models\Composite|\blackcube\core\models\Category|\blackcube\core\models\Node
 * @var $bloc \blackcube\core\models\Bloc
 * @var $this \yii\web\View
 * @var $i
 */

use app\helpers\CmsHelper;
use blackcube\admin\helpers\Bo;
use blackcube\admin\helpers\BlackcubeHtml;
use blackcube\admin\helpers\Html;
use yii\helpers\ArrayHelper;
use yii\helpers\Url;

Bo::registerExternalAssets($element, $this);


foreach($bloc->elasticAttributes as $attribute => $value) {

    $structure = $bloc->structure[$attribute];
    $field = $structure['field'];
    switch ($field) {
        case 'file':
        case 'files':
            echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute, [
                'upload-url' => Url::to(['file-upload']),
                'preview-url' => Url::to(['file-preview', 'name' => '__name__']),
                'delete-url' => Url::to(['file-delete', 'name' => '__name__'])
            ]);
            break;
        default:
            if (in_array($attribute, ['lang', 'ctaTarget', 'ctaSecondaryTarget'])) {
                $dropData = CmsHelper::getTargets();
                $dropData = ArrayHelper::map($dropData, 'route', 'title', 'group');
                echo BlackcubeHtml::activeDropDownList($bloc, '[' . $i . ']' . $attribute, $dropData);
            } else {
                echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute);
            }
            break;
    }
}
```

**Explications** :

- Parcourt tous les attributs élastiques du bloc
- Détecte le type de champ via `$structure['field']`
- Pour les fichiers : affiche l'uploader avec URLs d'upload/preview/delete
- Pour `lang`, `ctaTarget`, `ctaSecondaryTarget` : remplace par un dropdown alimenté par `CmsHelper::getTargets()`
- Pour les autres champs : affiche le champ standard

---

### Template bloc_cta.php

**Rôle** : Remplace le champ `ctaTarget` par un dropdown de sélection.

**Fichier** : `common/admin/bloc_cta.php`

```php
<?php
/**
 * bloc_cta.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $element \blackcube\core\models\Tag|\blackcube\core\models\Composite|\blackcube\core\models\Category|\blackcube\core\models\Node
 * @var $bloc \blackcube\core\models\Bloc
 * @var $this \yii\web\View
 * @var $i
 */

use app\helpers\CmsHelper;
use blackcube\admin\helpers\Bo;
use blackcube\admin\helpers\BlackcubeHtml;
use blackcube\admin\helpers\Html;
use yii\helpers\ArrayHelper;
use yii\helpers\Url;

Bo::registerExternalAssets($element, $this);


foreach($bloc->elasticAttributes as $attribute => $value) {

    $structure = $bloc->structure[$attribute];
    $field = $structure['field'];
    switch ($field) {
        case 'file':
        case 'files':
            echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute, [
                'upload-url' => Url::to(['file-upload']),
                'preview-url' => Url::to(['file-preview', 'name' => '__name__']),
                'delete-url' => Url::to(['file-delete', 'name' => '__name__'])
            ]);
            break;
        default:
            if (in_array($attribute, ['ctaTarget'])) {
                $dropData = CmsHelper::getTargets();
                $dropData = ArrayHelper::map($dropData, 'route', 'title', 'group');
                echo BlackcubeHtml::activeDropDownList($bloc, '[' . $i . ']' . $attribute, $dropData);
            } else {
                echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute);
            }
            break;
    }
}
```

**Explications** :

- Même logique que `bloc_hero.php`
- Remplace uniquement le champ `ctaTarget` par un dropdown

---

### Template bloc_card.php

**Rôle** : Remplace le champ `linkRoute` par un dropdown de sélection.

**Fichier** : `common/admin/bloc_card.php`

```php
<?php
/**
 * bloc_card.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $element \blackcube\core\models\Tag|\blackcube\core\models\Composite|\blackcube\core\models\Category|\blackcube\core\models\Node
 * @var $bloc \blackcube\core\models\Bloc
 * @var $this \yii\web\View
 * @var $i
 */

use app\helpers\CmsHelper;
use blackcube\admin\helpers\Bo;
use blackcube\admin\helpers\BlackcubeHtml;
use blackcube\admin\helpers\Html;
use yii\helpers\ArrayHelper;
use yii\helpers\Url;

Bo::registerExternalAssets($element, $this);


foreach($bloc->elasticAttributes as $attribute => $value) {

    $structure = $bloc->structure[$attribute];
    $field = $structure['field'];
    switch ($field) {
        case 'file':
        case 'files':
            echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute, [
                'upload-url' => Url::to(['file-upload']),
                'preview-url' => Url::to(['file-preview', 'name' => '__name__']),
                'delete-url' => Url::to(['file-delete', 'name' => '__name__'])
            ]);
            break;
        default:
            if (in_array($attribute, ['linkRoute'])) {
                $dropData = CmsHelper::getTargets();
                $dropData = ArrayHelper::map($dropData, 'route', 'title', 'group');
                echo BlackcubeHtml::activeDropDownList($bloc, '[' . $i . ']' . $attribute, $dropData);
            } else {
                echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute);
            }
            break;
    }
}
```

**Explications** :

- Même logique que `bloc_hero.php`
- Remplace uniquement le champ `linkRoute` par un dropdown

> **Info**
> Les templates d'administration sont optionnels. Sans eux, les champs s'affichent en texte libre. Avec eux, le contributeur sélectionne les cibles dans des dropdowns plutôt que de saisir des routes manuellement.

---

## Créer les Paramètres

**Accès** : Menu latéral → **Paramètres → Paramètres** → Bouton **Ajouter un paramètre**

Les paramètres centralisent les IDs des BlocTypes pour éviter le hardcoding dans le code PHP.

> **Attention**
> Utilise les IDs notés lors de la création des BlocTypes.

### Paramètre BLOC / HERO

**Domain** : `BLOC`  
**Name** : `HERO`  
**Value** : *ID du BlocType "Hero (H1)"*

### Paramètre BLOC / TITLE

**Domain** : `BLOC`  
**Name** : `TITLE`  
**Value** : *ID du BlocType "Titre (H2 - H4)"*

### Paramètre BLOC / CONTENT

**Domain** : `BLOC`  
**Name** : `CONTENT`  
**Value** : *ID du BlocType "Content"*

### Paramètre BLOC / CODE

**Domain** : `BLOC`  
**Name** : `CODE`  
**Value** : *ID du BlocType "Code"*

### Paramètre BLOC / INFO

**Domain** : `BLOC`  
**Name** : `INFO`  
**Value** : *ID du BlocType "Info"*

### Paramètre BLOC / FEATURE

**Domain** : `BLOC`  
**Name** : `FEATURE`  
**Value** : *ID du BlocType "Feature (H3)"*

### Paramètre BLOC / CTA

**Domain** : `BLOC`  
**Name** : `CTA`  
**Value** : *ID du BlocType "Cta"*

### Paramètre BLOC / CARD

**Domain** : `BLOC`  
**Name** : `CARD`  
**Value** : *ID du BlocType "Card"*

> **Info**
> Ces paramètres sont utilisés dans le code PHP via `Parameters::get('BLOC', 'HERO')` pour récupérer dynamiquement l'ID du BlocType Hero.

---

## Créer les Menus

**Accès** : Menu latéral → **Paramètres → Menus** → Bouton **Ajouter un menu**

### Menu Header FR

**Nom** : `header`  
**Langue** : `Français`  
**Actif** : Coché

**Items** (Bouton **Ajouter un élément de menu**) :

**Item Pourquoi Blackcube**
- Nom : `Pourquoi Blackcube`
- Route : Dropdown avec tous les contenus du site - Sélectionner l'article "Pourquoi Blackcube FR"

**Item Documentation**
- Nom : `Documentation`
- Route : Dropdown avec tous les contenus du site - Sélectionner la rubrique "Guide FR"

**Item Github**
- Nom : `Github`
- Route : Dropdown avec tous les contenus du site - Sélectionner `redirect/github`

---

### Menu Header EN

**Nom** : `header`  
**Langue** : `English`  
**Actif** : Coché

**Items** :

**Item Why Blackcube**
- Nom : `Why Blackcube`
- Route : Dropdown avec tous les contenus du site - Sélectionner l'article "Why Blackcube EN"

**Item Documentation**
- Nom : `Documentation`
- Route : Dropdown avec tous les contenus du site - Sélectionner la rubrique "Guide EN"

**Item Github**
- Nom : `Github`
- Route : Dropdown avec tous les contenus du site - Sélectionner `redirect/github`

---

### Menu Footer FR

**Nom** : `footer`  
**Langue** : `Français`  
**Actif** : Coché

**Items** :

**Item Documentation**
- Nom : `Documentation`
- Route : Dropdown avec tous les contenus du site - Sélectionner la rubrique "Guide FR"

**Item Contactez-nous**
- Nom : `Contactez-nous`
- Route : Dropdown avec tous les contenus du site - Sélectionner l'article "Contact FR"

**Item Github**
- Nom : `Github`
- Route : Dropdown avec tous les contenus du site - Sélectionner `redirect/github`

---

### Menu Footer EN

**Nom** : `footer`  
**Langue** : `English`  
**Actif** : Coché

**Items** :

**Item Documentation**
- Nom : `Documentation`
- Route : Dropdown avec tous les contenus du site - Sélectionner la rubrique "Guide EN"

**Item Contact us**
- Nom : `Contact us`
- Route : Dropdown avec tous les contenus du site - Sélectionner l'article "Contact EN"

**Item Github**
- Nom : `Github`
- Route : Dropdown avec tous les contenus du site - Sélectionner `redirect/github`

---

## Validation

Avant de continuer :

- 9 BlocTypes créés avec leurs JSON Schema
- 5 Types créés avec leurs BlocTypes autorisés
- 3 Templates d'administration créés dans `common/admin/`
- 8 Paramètres créés avec les IDs des BlocTypes
- 4 Menus créés avec leurs items