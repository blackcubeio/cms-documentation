# Redirect Pages
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Common Foundations

---

## Objective

Create CmsController with redirect actions to handle root Node (language detection) and container Nodes (redirect to first article).

---

## Create CmsController.php

**Role**: Main controller handling all CMS site pages.

**File**: `webapp/controllers/CmsController.php`

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
    
    // Actions defined below
}
```

---

## actionRedirectLanguage() Action

**Usage**: Root node (level 1) for language detection

**Operation**:
- Gets browser's preferred language among FR/EN
- Finds direct child matching this language
- Redirects to this child
- Throws 404 if no child matches

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

**Example**:
```
/ (Root node, Type: Redirect Language)
├── /fr (FR Node)
└── /en (EN Node)

Visitor with FR browser → Redirects to /fr
Visitor with EN browser → Redirects to /en
```

---

## actionRedirectFirstComposite() Action

**Usage**: Nodes (Sections without own content)

**Operation**:
- Gets first active Composite of Node
- Redirects to this Composite
- Throws 404 if no active Composite

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

**Example**:
```
/fr/guide (Guide Node, Type: Redirect First Composite)
├── /fr/guide/installation (Composite)
├── /fr/guide/configuration (Composite)
└── /fr/guide/usage (Composite)

Visit to /fr/guide → Redirects to /fr/guide/installation
```

---

## Type Configuration

These actions will be associated with Types in the back-office:

**Type "Redirect Language"**:
- Route: `cms/redirect-language`
- Usage: Root node only

**Type "Redirect First Composite"**:
- Route: `cms/redirect-first-composite`
- Usage: Nodes (Guide, About)

---

## Validation

Before continuing:

- CmsController.php created with HrefLangBehavior
- actionRedirectLanguage() implemented (language detection)
- actionRedirectFirstComposite() implemented (first article)