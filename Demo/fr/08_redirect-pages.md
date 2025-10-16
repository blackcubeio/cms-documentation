# Pages de Redirection
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Fondations Communes

---

## Objectif

Créer le CmsController avec les actions de redirection pour gérer le Node racine (détection de langue) et les Nodes containers (redirection vers le premier article).

---

## Créer CmsController.php

**Rôle** : Controller principal gérant toutes les pages du site CMS.

**Fichier** : `webapp/controllers/CmsController.php`

```php
<?php

namespace webapp\controllers;

use blackcube\core\web\controllers\BlackcubeController;
use webapp\behaviors\HrefLangBehavior;
use Exception;
use Yii;
use yii\web\NotFoundHttpException;

class CmsController extends BlackcubeController
{
    public function behaviors()
    {
        $behaviors = parent::behaviors();
        $behaviors['lang'] = [
            'class' => HrefLangBehavior::class
        ];
        return $behaviors;
    }
    
    // Actions définies ci-dessous
}
```

---

## Action actionRedirectLanguage()

**Usage** : Node racine (niveau 1) pour détection de langue

**Fonctionnement** :
- Récupère la langue préférée du navigateur parmi FR/EN
- Recherche l'enfant direct correspondant à cette langue
- Redirige vers cet enfant
- Lève une 404 si aucun enfant ne correspond

```php
public function actionRedirectLanguage()
{
    try {
        Yii::debug('Trace :'.__METHOD__, __METHOD__);
        $node = $this->element;
        $lang = Yii::$app->request->getPreferredLanguage(['fr','en']);
        $targetNode = $node->getChildren()
            ->andWhere(['languageId' => $lang])
            ->active()
            ->one();
        if ($targetNode !== null) {
            return $this->redirect([$targetNode->getRoute()]);
        } else {
            throw new NotFoundHttpException('No home node found');
        }
    } catch (Exception $e) {
        Yii::error($e->getMessage(), __METHOD__);
        throw $e;
    }
}
```

**Exemple** :
```
/ (Node racine, Type: Redirect Language)
├── /fr (Node FR)
└── /en (Node EN)

Visiteur avec navigateur FR → Redirige vers /fr
Visiteur avec navigateur EN → Redirige vers /en
```

---

## Action actionRedirectFirstComposite()

**Usage** : Nodes (Rubriques sans contenu propre)

**Fonctionnement** :
- Récupère le premier Composite actif du Node
- Redirige vers ce Composite
- Lève une 404 si aucun Composite actif

```php
public function actionRedirectFirstComposite()
{
    try {
        Yii::debug('Trace :'.__METHOD__, __METHOD__);
        $node = $this->element;
        $targetComposite = $node->getComposites()
            ->active()
            ->one();
        if ($targetComposite !== null) {
            return $this->redirect([$targetComposite->getRoute()]);
        } else {
            throw new NotFoundHttpException('No content found');
        }
    } catch (Exception $e) {
        Yii::error($e->getMessage(), __METHOD__);
        throw $e;
    }
}
```

**Exemple** :
```
/fr/guide (Node Guide, Type: Redirect First Composite)
├── /fr/guide/installation (Composite)
├── /fr/guide/configuration (Composite)
└── /fr/guide/utilisation (Composite)

Visite de /fr/guide → Redirige vers /fr/guide/installation
```

---

## Configuration des Types

Ces actions seront associées aux Types dans le back-office :

**Type "Redirect Language"** :
- Route : `cms/redirect-language`
- Usage : Node racine uniquement

**Type "Redirect First Composite"** :
- Route : `cms/redirect-first-composite`
- Usage : Nodes (Guide, À propos, About)

---

## Validation

Avant de continuer :

- CmsController.php créé avec HrefLangBehavior
- actionRedirectLanguage() implémentée (détection langue)
- actionRedirectFirstComposite() implémentée (premier article)