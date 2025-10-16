# Fondations Communes
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Réflexion sur les BlocTypes

---

## Objectif

Créer les composants de base utilisés par l'ensemble du projet : helpers, behavior et widgets de layout systématiques (Header, Footer).

---

## Créer Parameters.php

**Rôle** : Helper pour accéder aux paramètres centralisés créés dans le back-office. Évite le hardcoding des IDs de BlocTypes dans le code.

**Fichier** : `common/helpers/Parameters.php`

**Mécanisme** : Charge tous les paramètres en mémoire une seule fois dans un array statique privé.

**Utilisation** :
```php
// Récupère l'ID du BlocType Hero
$heroBlocTypeId = Parameters::get('BLOC', 'HERO');
```

**Méthode principale** :

```php
public static function get($domain, $name)
{
    $parameters = self::getParameters();
    return $parameters[$domain][$name] ?? null;
}
```

---

## Créer CmsHelper.php

**Rôle** : Helper fournissant deux fonctions essentielles :
1. `getTargets()` : Liste tous les Nodes et Composites pour les dropdowns dans les formulaires admin
2. `groupBlocs()` : Regroupe les blocs consécutifs du même type

**Fichier** : `common/helpers/CmsHelper.php`

### Méthode getTargets()

Génère un array avec tous les Nodes et Composites disponibles, organisés hiérarchiquement pour les dropdowns.

**Fonctionnement** :
- Ajoute une option vide en premier
- Parcourt les Nodes en ajoutant le préfixe en fonction du niveau
- Pour chaque Node, liste ses Composites en sous-groupe
- Résultat utilisable directement dans un dropdown avec groupes

### Méthode groupBlocs()

Regroupe les blocs consécutifs ayant le même blocTypeId.

**Fonctionnement** :
- `$onId` : Array des BlocTypeIds à grouper (ex: Features)
- Si le bloc est dans la liste ET identique au précédent : ajoute au groupe
- Si différent ou pas dans la liste : finalise le groupe
- Un groupe de 1 bloc reste un Bloc, 2+ blocs devient un Array

---

## Créer HrefLangBehavior.php

**Rôle** : Behavior SEO qui génère automatiquement les balises `<link rel="alternate" hreflang="...">` pour le référencement multilingue. Utilise le champ `lang` du bloc Hero.

**Fichier** : `webapp/behaviors/HrefLangBehavior.php`

**Méthode events** :

```php
public function events(): array
{
    return [
        BlackcubeController::EVENT_AFTER_ELEMENT => 'registerHrefLang',
    ];
}
```

Le behavior s'attache à l'événement `EVENT_AFTER_ELEMENT` pour s'exécuter une fois que l'élément est prêt.

**Méthode registerHrefLang** :

```php
public function registerHrefLang($event)
{
    $element = $event->element;
    $view = $this->owner->getView();
    $hrefLangs = [];

    // Ajoute la langue courante
    $hrefLangs[] = [
        'rel' => 'alternate',
        'hreflang' => $element->languageId,
        'href' => Url::toRoute($element->getRoute(), true),
    ];

    // Recherche du contenu lié via le champ lang du Hero
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
    
    // Enregistre seulement si plusieurs langues
    if (count($hrefLangs) > 1) {
        foreach ($hrefLangs as $hrefLang) {
            $view->registerLinkTag($hrefLang);
        }
    }
}
```

**Explications** :
- S'attache à l'événement `EVENT_AFTER_ELEMENT` du BlackcubeController
- Récupère le bloc Hero de l'élément courant
- Si champ `lang` rempli : instancie l'élément lié
- Enregistre les balises uniquement si plusieurs langues disponibles

---

## Créer Header.php

**Rôle** : Widget générant le header avec logo, menu de navigation et language switcher.

**Fichier** : `webapp/widgets/Header.php`

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

**Vue** : `webapp/widgets/views/header.php`

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

## Créer Footer.php

**Rôle** : Widget générant le footer avec copyright et menu de navigation.

**Fichier** : `webapp/widgets/Footer.php`

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

**Vue** : `webapp/widgets/views/footer.php`

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

Avant de continuer :

- Parameters.php créé (helper paramètres)
- CmsHelper.php créé (getTargets + groupBlocs)
- HrefLangBehavior.php créé (balises hreflang)
- Header.php + vue créés
- Footer.php + vue créés