# Common Foundations
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: BlocTypes Planning

---

## Objective

Create base components used throughout the project: helpers, behavior, and systematic layout widgets (Header, Footer).

---

## Create Parameters.php

**Role**: Helper to access centralized parameters created in the back-office. Avoids hardcoding BlocType IDs in code.

**File**: `common/helpers/Parameters.php`

**Mechanism**: Loads all parameters in memory once in a private static array.

**Usage**:
```php
// Get Hero BlocType ID
$heroBlocTypeId = Parameters::get('BLOC', 'HERO');
```

**Main method**:

```php
public static function get($domain, $name)
{
    $parameters = self::getParameters();
    return $parameters[$domain][$name] ?? null;
}
```

---

## Create CmsHelper.php

**Role**: Helper providing two essential functions:
1. `getTargets()`: Lists all Nodes and Composites for dropdowns in admin forms
2. `groupBlocs()`: Groups consecutive blocks of same type

**File**: `common/helpers/CmsHelper.php`

### getTargets() Method

Generates an array with all available Nodes and Composites, organized hierarchically for dropdowns.

**Operation**:
- Adds empty option first
- Loops through Nodes adding prefix based on level
- For each Node, lists its Composites in subgroup
- Result directly usable in dropdown with groups

### groupBlocs() Method

Groups consecutive blocks with same blocTypeId.

**Operation**:
- `$onId`: Array of BlocTypeIds to group (e.g., Features)
- If block is in list AND identical to previous: add to group
- If different or not in list: finalize group
- Group of 1 block remains a Block, 2+ blocks become Array

---

## Create HrefLangBehavior.php

**Role**: SEO behavior that automatically generates `<link rel="alternate" hreflang="...">` tags for multilingual SEO. Uses Hero block's `lang` field.

**File**: `webapp/behaviors/HrefLangBehavior.php`

**events method**:

```php
public function events(): array
{
    return [
        BlackcubeController::EVENT_AFTER_ELEMENT => 'registerHrefLang',
    ];
}
```

The behavior attaches to `EVENT_AFTER_ELEMENT` to execute once element is ready.

**registerHrefLang method**:

```php
public function registerHrefLang($event)
{
    $element = $event->element;
    $view = $this->owner->getView();
    $hrefLangs = [];

    // Add current language
    $hrefLangs[] = [
        'rel' => 'alternate',
        'hreflang' => $element->languageId,
        'href' => Url::toRoute($element->getRoute(), true),
    ];

    // Search for linked content via Hero's lang field
    $heroBlocTypeId = Parameters::get('BLOC', 'HERO');
    $bloc = $element->getBlocs()->andWhere(['blocTypeId' => $heroBlocTypeId])->one();
    if ($bloc !== null && empty($bloc->lang) === false) {
        $langElement = Element::instanciate($bloc->lang);
        if ($langElement !== null) {
            $hrefLangs[] = [
                'rel' => 'alternate',
                'hreflang' => $langElement->languageId,
                'href' => Url::toRoute($langElement->getRoute(), true),
            ];
        }
    }
    
    // Register only if multiple languages
    if (count($hrefLangs) > 1) {
        foreach ($hrefLangs as $hrefLang) {
            $view->registerLinkTag($hrefLang);
        }
    }
}
```

**Explanation**:
- Attaches to BlackcubeController's `EVENT_AFTER_ELEMENT`
- Retrieves current element's Hero block
- If `lang` field filled: instantiates linked element
- Registers tags only if multiple languages available

---

## Create Header.php

**Role**: Widget generating header with logo, navigation menu, and language switcher.

**File**: `webapp/widgets/Header.php`

```php
<?php
/**
 * File Header.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use blackcube\core\models\Menu;
use blackcube\core\models\Node;
use yii\base\Widget;
use Yii;

/**
 * Class Header
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class Header extends Widget
{
    public $element;

    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: ' . __METHOD__, __METHOD__);
        $language = $this->element->languageId ?? Yii::$app->language;
        $homes = Node::find()
            ->andWhere([
                'level' => 2,
            ])
            ->andWhere(
                ['in', 'languageId', ['fr', 'en']]
            )
            ->limit(2)
            ->all();
        foreach ($homes as $home) {
            if ($home->languageId === $language) {
                $homeCurrentRoute = $home->getRoute();
            } else {
                $homeAlternateRoute = $home->getRoute();
            }
        }
        $hero = $this->element?->getBlocs()
            ->andWhere(['blocTypeId' => 1])
            ->one();
        $altLanguage = ($language === 'fr') ? 'en' : 'fr';
        $route = $this->element->getRoute();
        $altRoute = $hero?->lang;
        if ($altRoute === null || empty($altRoute)) {
            $altRoute = $homeAlternateRoute;
        }

        $menu = Menu::find()
            ->andWhere([
                'name' => 'header',
                'languageId' => $language
            ])->one();

        return $this->render('header', [
            'homeRoute' => $homeCurrentRoute ?? '/',
            'route' => $route,
            'language' => $language,
            'altRoute' => $altRoute ?? '/',
            'altLanguage' => $altLanguage,
            'menu' => $menu,
        ]);
    }
}
```

**View**: `webapp/widgets/views/header.php`

```php
<?php
/**
 * header.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $homeRoute string
 * @var $route string
 * @var $language string
 * @var $altRoute string
 * @var $altLanguage string
 * @var $menu blackcube\core\models\Menu|null
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Header -->
<header class="border-b border-gray-200 bg-white sticky top-0 z-50">
    <nav class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8" mobile-menu="">
        <div class="flex justify-between items-center h-16">
            <div class="flex-shrink-0">
                <?php echo Html::beginTag('a', [
                    'class' => 'text-2xl font-bold text-accent',
                    'href' => Url::to([$homeRoute])
                ]); ?>

                <svg class="block h-12 w-12 text-gray-900 hover:text-gray-600 hover:scale-110 transition" xmlns="http://www.w3.org/2000/svg" viewBox="0 -5 180 203.9253574150115">
                    <g
                            transform="matrix(3.6,0,0,3.6,-25.2,-37.4)"
                            fill="currentColor">
                        <g xmlns="http://www.w3.org/2000/svg">
                            <path d="M56,9H8c-0.552,0-1,0.448-1,1v44c0,0.553,0.448,1,1,1h48c0.553,0,1-0.447,1-1V10C57,9.448,56.553,9,56,9z M30.707,32.707 l-10,10C20.512,42.902,20.256,43,20,43s-0.512-0.098-0.707-0.293c-0.391-0.391-0.391-1.023,0-1.414L28.586,32l-9.293-9.293 c-0.391-0.391-0.391-1.023,0-1.414s1.023-0.391,1.414,0l10,10C31.098,31.684,31.098,32.316,30.707,32.707z M44,43H34 c-0.553,0-1-0.447-1-1s0.447-1,1-1h10c0.553,0,1,0.447,1,1S44.553,43,44,43z"></path>
                        </g>
                    </g>
                </svg>
                <?php echo Html::endTag('a'); ?>
            </div>
            <div class="hidden md:flex items-center space-x-8">
                <?php foreach ($menu->getChildren()->each() as $menuItem): ?>
                    <?php echo Html::a(Html::encode($menuItem->name), Url::to([$menuItem->route]), [
                        'class' => 'text-gray-700 hover:text-primary transition',
                        'target' => preg_match('/redirect/', $menuItem->route) ? '_blank' : null,
                    ]); ?>
                <?php endforeach; ?>
                <div class="flex items-center space-x-2 border-l border-gray-300 pl-6">
                    <?php echo Html::a('FR', [
                        ($language === 'fr') ? $route : $altRoute
                    ], [
                        'class' => ($language === 'fr') ? 'text-primary font-medium' : 'text-gray-500 hover:text-primary transition'
                    ]); ?>
                    <span class="text-gray-400">|</span>
                    <?php echo Html::a('EN', [
                        ($language === 'en') ? $route : $altRoute
                    ], [
                        'class' => ($language === 'en') ? 'text-primary font-medium' : 'text-gray-500 hover:text-primary transition'
                    ]); ?>
                </div>
            </div>
            <button class="md:hidden p-2 cursor-pointer" data-menu="hamburger">
                <svg data-menu="open-icon" class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
                </svg>
                <svg data-menu="close-icon" class="w-6 h-6 hidden" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12"/>
                </svg>
            </button>
        </div>
        <div class="hidden md:hidden pb-4" data-menu="panel">
            <div class="flex flex-col space-y-4">
                <?php foreach ($menu->getChildren()->each() as $menuItem): ?>
                    <?php echo Html::a(Html::encode($menuItem->name), Url::to([$menuItem->route]), [
                        'class' => 'text-gray-700 hover:text-primary transition',
                        'target' => preg_match('/redirect/', $menuItem->route) ? '_blank' : null,
                    ]); ?>
                <?php endforeach; ?>
                <div class="flex items-center space-x-2 px-4 py-2 border-t border-gray-300 pt-4">
                    <?php echo Html::a('FR', [
                        ($language === 'fr') ? $route : $altRoute
                    ], [
                        'class' => ($language === 'fr') ? 'text-primary font-medium' : 'text-gray-500 hover:text-primary transition'
                    ]); ?>
                    <span class="text-gray-400">|</span>
                    <?php echo Html::a('EN', [
                        ($language === 'en') ? $route : $altRoute
                    ], [
                        'class' => ($language === 'en') ? 'text-primary font-medium' : 'text-gray-500 hover:text-primary transition'
                    ]); ?>
                </div>
            </div>
        </div>
    </nav>
</header>
```

---

## Create Footer.php

**Role**: Widget generating footer with copyright and navigation menu.

**File**: `webapp/widgets/Footer.php`

```php
<?php
/**
 * File Footer.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use blackcube\core\models\Menu;
use yii\base\Widget;
use Yii;

/**
 * Class Footer
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class Footer extends Widget
{
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: ' . __METHOD__, __METHOD__);
        $menu = Menu::find()
            ->andWhere([
                'name' => 'footer',
                'languageId' => Yii::$app->language
            ])->one();
        return $this->render('footer', [
            'menu' => $menu
        ]);
    }
}
```

**View**: `webapp/widgets/views/footer.php`

```php
<?php
/**
 * footer.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $menu blackcube\core\models\Menu|null
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Footer -->
<footer class="bg-white border-t border-gray-200 py-12">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex flex-col md:flex-row justify-between items-center">
            <div class="text-gray-600 mb-4 md:mb-0">
                ©<?php echo date('Y'); ?> Blackcube
            </div>
            <div class="flex space-x-6">
                <?php foreach ($menu->getChildren()->each() as $menuItem): ?>
                    <?php echo Html::a(Html::encode($menuItem->name), Url::to([$menuItem->route]), [
                        'class' => 'text-gray-600 hover:text-primary transition',
                        'target' => preg_match('/redirect/', $menuItem->route) ? '_blank' : null,
                    ]); ?>
                <?php endforeach; ?>
            </div>
        </div>
    </div>
</footer>
```

---

## Validation

Before continuing:

- Parameters.php created (parameters helper)
- CmsHelper.php created (getTargets + groupBlocs)
- HrefLangBehavior.php created (hreflang tags)
- Header.php + view created
- Footer.php + view created