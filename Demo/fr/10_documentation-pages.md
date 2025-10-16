# Page Documentation
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Page d'Accueil

---

## Objectif

Créer l'action, les widgets et la vue pour afficher les pages de documentation avec layout 3 colonnes (Sidebar + Contenu + TOC), breadcrumb et navigation prev/next.

---

## Action actionDocumentation()

**Ajout dans** : `webapp/controllers/CmsController.php`

```php
public function actionDocumentation()
{
    try {
        Yii::debug('Trace :'.__METHOD__, __METHOD__);
        Yii::$app->language = $this->element->languageId;
        $heroBlocTypeId = Parameters::get('BLOC', 'HERO');
        $hero = $this->element->getBlocs()
            ->andWhere(['blocTypeId' => $heroBlocTypeId])
            ->one();
        $blocs = $this->element->getBlocs()
            ->andWhere(['!=', 'blocTypeId', $heroBlocTypeId])
            ->all();

        $featureBlocTypeId = Parameters::get('BLOC', 'FEATURE');
        return $this->render('documentation', [
            'element' => $this->element,
            'hero' => $hero,
            'blocs' => CmsHelper::groupBlocs($blocs, [$featureBlocTypeId]),
        ]);
    } catch (Exception $e) {
        Yii::error($e->getMessage(), __METHOD__);
        throw $e;
    }
}
```

**Fonctionnement** :
- Définit la langue de l'application selon l'élément
- Récupère le bloc Hero séparément
- Récupère tous les autres blocs
- Groupe les Features consécutives avec `CmsHelper::groupBlocs()`
- Passe tout à la vue

---

## Créer les Widgets de Blocs

### Widget 1 : BlocTitle

**Rôle** : Affiche les titres intermédiaires H2, H3 ou H4 avec gestion dynamique de la taille selon le niveau.

**Fichier** : `webapp/widgets/BlocTitle.php`

```php
<?php
/**
 * File BlocTitle.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

/**
 * Class BlocTitle
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class BlocTitle extends Widget
{

    public $bloc;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        return $this->render('bloc_title', [
            'bloc' => $this->bloc
        ]);
    }
}
```

**Vue** : `webapp/widgets/views/bloc_title.php`

```php
<?php
/**
 * bloc_title.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $bloc \blackcube\core\models\Bloc
 */

use yii\helpers\Html;
use yii\helpers\Url;

$size = 'text-3xl';
switch ($bloc->hn) {
    case 'h2':
        $size = 'text-3xl';
        break;
    case 'h3':
        $size = 'text-2xl';
        break;
    case 'h4':
        $size = 'text-xl';
        break;
}
?>
<!-- bloc_titre -->
<?php echo Html::tag($bloc->hn, Html::encode($bloc->title), [
        'class' => $size.' font-bold text-accent mb-4 mt-12',
        'id' => 'title-'.$bloc->id
    ]); ?>
```

**Explications** :
- Utilise `Html::tag()` avec le niveau dynamique `$bloc->hn`
- Taille adaptée selon le niveau : H2 (3xl), H3 (2xl), H4 (xl)
- Génère un ID unique `title-{id}` pour les ancres TOC
- Espacement vertical : `mt-12` et `mb-4`

---

### Widget 2 : BlocContent

**Rôle** : Affiche le contenu riche WYSIWYG avec nettoyage HTML via `Quill::cleanHtml()`.

**Fichier** : `webapp/widgets/BlocContent.php`

```php
<?php
/**
 * File BlocContent.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

/**
 * Class BlocContent
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class BlocContent extends Widget
{

    public $bloc;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        return $this->render('bloc_content', [
            'bloc' => $this->bloc
        ]);
    }
}
```

**Vue** : `webapp/widgets/views/bloc_content.php`

```php
<?php
/**
 * bloc_content.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $bloc \blackcube\core\models\Bloc
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- bloc_content -->
<div class="text-gray-700 leading-relaxed mb-8 content">
    <?php echo \blackcube\core\web\helpers\Quill::cleanHtml($bloc->content); ?>
</div>
```

**Explications** :
- Utilise `Quill::cleanHtml()` pour nettoyer le HTML de l'éditeur Quill
- Classe `.content` pour styliser les éléments internes (listes, liens, etc.)
- Espacement vertical `mb-8` entre les blocs

---

### Widget 3 : BlocCode

**Rôle** : Affiche un bloc de code avec coloration syntaxique et bouton copier.

**Fichier** : `webapp/widgets/BlocCode.php`

```php
<?php
/**
 * File BlocCode.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

/**
 * Class BlocCode
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class BlocCode extends Widget
{

    public $bloc;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        return $this->render('bloc_code', [
            'bloc' => $this->bloc
        ]);
    }
}
```

**Vue** : `webapp/widgets/views/bloc_code.php`

```php
<?php
/**
 * bloc_code.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $bloc \blackcube\core\models\Bloc
 */

use yii\helpers\Html;
use yii\helpers\Url;


$code = str_replace(['<', '>'], ['&lt;', '&gt;'], $bloc?->code??'');
?>
<!-- bloc_code -->
<?php echo Html::beginTag('div', [
        'class' => 'relative bg-gray-900 rounded-lg p-6 mb-8 overflow-x-auto',
        'code-highlight' => $bloc?->language
]); ?>
    <button class="absolute top-4 right-4 text-sm text-gray-400 hover:text-white transition cursor-pointer">
        <svg class="w-5 h-5" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 12h3.75M9 15h3.75M9 18h3.75m3 .75H18a2.25 2.25 0 0 0 2.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 0 0-1.123-.08m-5.801 0c-.065.21-.1.433-.1.664 0 .414.336.75.75.75h4.5a.75.75 0 0 0 .75-.75 2.25 2.25 0 0 0-.1-.664m-5.8 0A2.251 2.251 0 0 1 13.5 2.25H15c1.012 0 1.867.668 2.15 1.586m-5.8 0c-.376.023-.75.05-1.124.08C9.095 4.01 8.25 4.973 8.25 6.108V8.25m0 0H4.875c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V9.375c0-.621-.504-1.125-1.125-1.125H8.25ZM6.75 12h.008v.008H6.75V12Zm0 3h.008v.008H6.75V15Zm0 3h.008v.008H6.75V18Z" />
        </svg>
    </button>
    <?php echo Html::beginTag('pre', [
            'class' => 'font-mono text-sm text-gray-100 mt-5 mb-0 mx-0',
    ]); ?>

    <?php echo Html::tag('code', trim($code)); ?>

    <?php echo Html::endTag('pre'); ?>

<?php echo Html::endTag('div'); ?>
```

**Explications** :
- Échappe les caractères `<` et `>` pour éviter l'interprétation HTML
- Attribut `code-highlight` avec le langage pour coloration syntaxique JS
- Bouton copier en position absolue (top-right)
- Fond sombre `bg-gray-900` avec texte clair
- Scroll horizontal si code trop large

---

### Widget 4 : BlocInfo

**Rôle** : Affiche une alerte colorée selon le type (info, warning, error).

**Fichier** : `webapp/widgets/BlocInfo.php`

```php
<?php
/**
 * File BlocInfo.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

/**
 * Class BlocInfo
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class BlocInfo extends Widget
{
    public $bloc;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        return $this->render('bloc_info', [
            'bloc' => $this->bloc,
        ]);
    }
}
```

**Vue** : `webapp/widgets/views/bloc_info.php`

```php
<?php
/**
 * bloc_info.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $bloc \blackcube\core\models\Bloc
 */

use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;

$borderColor = 'border-primary';
$titleColor = 'text-primary-800';
$contentColor = 'text-primary-700';
$iconColor = 'text-primary';
$bgColor = 'bg-primary-50';
switch($bloc->type) {
    case 'warning':
        $borderColor = 'border-yellow-500';
        $titleColor = 'text-yellow-800';
        $contentColor = 'text-yellow-700';
        $iconColor = 'text-yellow-600';
        $bgColor = 'bg-yellow-50';
        break;
    case 'error':
        $borderColor = 'border-red-500';
        $titleColor = 'text-red-800';
        $contentColor = 'text-red-700';
        $iconColor = 'text-red-600';
        $bgColor = 'bg-red-50';
        break;
}
?>
<!-- bloc_info (warning) -->
<?php echo Html::beginTag('div', ['class' => 'text-base border-l-4 '.$borderColor.' '.$bgColor.' p-4 rounded-r-lg mb-8']); ?>
    <div class="flex items-start">
        <?php echo Html::beginTag('svg', ['class' => 'w-5 h-5 '.$iconColor.' mr-3 flex-shrink-0 mt-0.5', 'fill' => 'none', 'stroke' => 'currentColor', 'viewBox' => '0 0 24 24']); ?>
        <?php if($bloc->type == 'warning'): ?>
           <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v3.75m9-.75a9 9 0 1 1-18 0 9 9 0 0 1 18 0Zm-9 3.75h.008v.008H12v-.008Z" />
        <?php elseif($bloc->type == 'error'): ?>
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v3.75m-9.303 3.376c-.866 1.5.217 3.374 1.948 3.374h14.71c1.73 0 2.813-1.874 1.948-3.374L13.949 3.378c-.866-1.5-3.032-1.5-3.898 0L2.697 16.126ZM12 15.75h.007v.008H12v-.008Z" />
        <?php else: ?>
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
        <?php endif; ?>
        <?php echo Html::endTag('svg'); ?>

        <div>
            <?php echo Html::tag('p', Html::encode($bloc->title), ['class' => 'font-semibold '.$titleColor.' mb-2 mt-0']); ?>
            <?php echo Html::tag('div', Quill::cleanHtml($bloc->content), ['class' => $contentColor.' content']); ?>
        </div>
    </div>
<?php echo Html::endTag('div'); ?>
```

**Explications** :
- 3 variantes de couleurs selon `$bloc->type` : info (bleu), warning (jaune), error (rouge)
- Bordure gauche épaisse avec `border-l-4`
- Icône SVG différente selon le type d'alerte
- Fond coloré léger avec texte foncé pour lisibilité

---

## Créer les Widgets de Layout

### Widget 5 : Sidebar

**Rôle** : Génère la navigation hiérarchique dans la colonne de gauche avec la liste des Nodes et leurs Composites.

**Fichier** : `webapp/widgets/Sidebar.php`

```php
<?php
/**
 * File Sidebar.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use app\helpers\Parameters;
use blackcube\core\models\Composite;
use blackcube\core\models\Node;
use yii\base\Widget;
use Yii;

/**
 * Class Sidebar
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class Sidebar extends Widget
{

    /**
     * @var Composite|Node
     */
    public $element;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);
        if ($this->element instanceof Composite) {
            $node = $this->element->getNodes()
                ->active()
                ->one();
            $composite = $this->element;

        } elseif ($this->element instanceof Node) {
            $node = $this->element;
            $composite = null;
        }
        $topNode = $node?->getParents()
            ->active()
            ->andWhere(['level' => 2])
            ->one();
        $nodeList = $topNode?->getChildren()
            ->active()
            ->all();
        if ($nodeList === null) {
            return '';
        }
        $menuList = [];
        $heroBlocTypeId = Parameters::get('BLOC', 'HERO');

        foreach($nodeList as $childNode) {
            $menu = [
                'title' => $childNode->getBlocs()
                        ->andWhere(['blocTypeId' => $heroBlocTypeId])
                        ->one()?->title ?? $childNode->name,
                'route' => $childNode->getRoute(),
                'active' => $childNode->id === $node->id,
                'children' => []
            ];
            foreach ($childNode->getComposites()->active()->all() as $childComposite) {
                $menu['children'][] = [
                    'title' => $childComposite->getBlocs()
                            ->andWhere(['blocTypeId' => $heroBlocTypeId])
                            ->one()?->title ?? $childComposite->name,
                    'route' => $childComposite->getRoute(),
                    'active' => $composite?->id === $childComposite->id
                ];
            }
            $menuList[] = $menu;
        }
        return $this->render('sidebar', [
            'menuList' => $menuList,
        ]);
    }
}
```

**Vue** : `webapp/widgets/views/sidebar.php`

```php
<?php
/**
 * sidebar.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $menuList array
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Sidebar -->
<aside class="hidden lg:block w-72 border-r border-gray-200 bg-gray-50 min-h-screen sticky top-16 overflow-y-auto">
    <nav class="p-6 space-y-6">
        <?php foreach ($menuList as $menuItem): ?>
            <div>
                <p class="block text-xs font-semibold text-gray-500 uppercase tracking-wider mb-3">
                    <?php echo Html::encode($menuItem['title']); ?>
                </p>
                <?php if (isset($menuItem['children']) && !empty($menuItem['children'])): ?>
                    <ul class="space-y-1">
                    <?php foreach ($menuItem['children'] as $child): ?>
                        <li>
                            <?php echo Html::a(Html::encode($child['title']), Url::to([$child['route']]), [
                                'class' => 'flex items-center px-3 py-2 text-sm '.($child['active'] ? 'font-medium text-white bg-primary rounded-md border-l-4 border-primary-600' : 'text-gray-700 hover:bg-gray-200 rounded-md transition')
                            ]); ?>
                        </li>
                    <?php endforeach; ?>
                    </ul>
                <?php endif; ?>
            </div>
        <?php endforeach; ?>
    </nav>
</aside>
```

**Explications** :
- Gère Composite ET Node avec `instanceof`
- Pour Composite : récupère le Node via `getNodes()->active()->one()`
- Récupère le Node parent de niveau 2 avec filtre `['level' => 2]`
- Liste tous les Nodes enfants du `$topNode`
- Pour chaque Node : liste ses Composites
- Utilise le champ `title` du Hero si disponible, sinon `name`
- Élément actif avec fond bleu et bordure gauche
- Sticky avec `top-16` pour suivre le scroll

---

### Widget 6 : Toc (Table of Contents)

**Rôle** : Génère la table des matières dans la colonne de droite en listant tous les titres H2, H3.

**Fichier** : `webapp/widgets/Toc.php`

```php
<?php
/**
 * File Toc.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use app\helpers\Parameters;
use yii\base\Widget;
use Yii;

/**
 * Class Toc
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class Toc extends Widget
{

    public $element;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        $titleBlocTypeId = Parameters::get('BLOC', 'TITLE');
        $titleQuery = $this->element->getBlocs()->andWhere(['blocTypeId' => $titleBlocTypeId]);
        $links = [];

        foreach($titleQuery->each() as $titleBloc) {
            $level = null;
            switch($titleBloc->hn) {
                case 'h2':
                    $level = 2;
                    break;
                case 'h3':
                    $level = 3;
                    break;
                case 'h4':
                    $level = 4;
                    break;
            }
            if($level !== null) {
                $links[] = [
                    'title' => $this->cleanTitle($titleBloc->title),
                    'id' => 'title-' . $titleBloc->id,
                    'level' => $level
                ];
            }
        }
        return $this->render('toc', [
            'links' => $links
        ]);
    }

    private function cleanTitle($string)
    {

        // remove (.*)
        $clean = preg_replace('/\s*\(.*?\)\s*/', '', $string);
        // remove 1., 01.
        $clean = preg_replace('/\d+\s*\./', '', $clean);
        // remove Emoticons ( 1F601 - 1F64F )
        $clean = preg_replace('/[\x{1F600}-\x{1FFFF}]/u', '', $clean);
        // Dingbats ( 2702 - 27B0 )
        $clean = preg_replace('/[\x{2700}-\x{27BF}]/u', '', $clean);
        // Transport and map symbols ( 1F680 - 1F6C0 )
        $clean = preg_replace('/[\x{1F680}-\x{1F6C0}]/u', '', $clean);
        // Enclosed characters ( 24C2 - 1F251 )
        $clean = preg_replace('/[\x{24C2}-\x{1F251}]/u', '', $clean);
        // Uncategorized U+00A9, U+00AE, U+203C, U+3299, U+0023 U+20E3, U+0030 U+20E3 - U+0039 U+20E3
        // 	U+2122, U+2139, U+2194 - U+2199, U+21A9 - U+21AA, U+231A - U+231B, U+2328, U+23CF, U+23E9 - U+23F3
        // 	U+23F8 - U+23FA, U+24C2
        // $clean = preg_replace('/[\x{00A9}\x{00AE}\x{203C}\x{3299}\x{0023}\x{20E3}\x{0030}-\x{0039}\x{2122}\x{2139}\x{2194}-\x{2199}\x{21A9}-\x{21AA}\x{231A}-\x{231B}\x{2328}\x{23CF}\x{23E9}-\x{23F3}\x{23F8}-\x{23FA}\x{24C2}]/u', '', $clean);
        // 6a. Additional emoticons ( 1F600 - 1F636 )
        $clean = preg_replace('/[\x{1F600}-\x{1F636}]/u', '', $clean);
        // 6b. Additional transport and map symbols ( 1F681 - 1F6C5 )
        $clean = preg_replace('/[\x{1F681}-\x{1F6C5}]/u', '', $clean);
        // 6c. Other additional symbols ( 1F30D - 1F567 )
        $clean = preg_replace('/[\x{1F30D}-\x{1F567}]/u', '', $clean);
        // 🧩 Puzzle Piece ( 1F9E9 )
        return trim($clean);
    }
}
```

**Vue** : `webapp/widgets/views/toc.php`

```php
<?php
/**
 * toc.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $links array
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Table of Contents (TOC) -->
<?php if (!empty($links)) :?>
<aside class="hidden xl:block w-64 border-l border-gray-200 bg-gray-50 min-h-screen sticky top-16 overflow-y-auto">
    <nav class="p-6">
        <h3 class="text-xs font-semibold text-gray-500 uppercase tracking-wider mb-4">
            <?php echo Yii::t('app', 'On this page'); ?>
        </h3>
        <ul class="space-y-3 text-sm">
            <?php foreach($links as $link) : ?>
            <?php if ($link['level'] <= 2): ?>
                <li>
                    <?php echo Html::a($link['title'], '#'.$link['id'], [
                        'class' => 'text-gray-700 hover:text-primary transition border-l-2 border-transparent hover:border-primary pl-3 block py-1'
                    ]); ?>
                </li>
            <?php elseif ($link['level'] === 3): ?>
                <li class="pl-3">
                    <?php echo Html::a($link['title'], '#'.$link['id'], [
                            'class' => 'text-xs text-gray-700 hover:text-primary transition border-l-2 border-transparent hover:border-primary pl-3 block py-1'
                    ]); ?>
                </li>
            <?php endif; ?>
            <?php endforeach; ?>
        </ul>
    </nav>
</aside>
<?php endif; ?>
```

**Explications** :
- Récupère tous les blocs de type TITLE via `each()` (iterator)
- Extrait le niveau (H2/H3/H4) avec `$level = null` par défaut
- Check `if($level !== null)` avant d'ajouter à `$links`
- Appelle `cleanTitle()` pour nettoyer le titre (emoji, parenthèses, numéros)
- Vue : affiche seulement si `!empty($links)`
- Affiche H2 et H3 uniquement (`level <= 2` et `level === 3`)
- H3 avec indentation `pl-3`
- Traduction du titre avec `Yii::t('app', 'On this page')`

---

### Widget 7 : Breadcrumb

**Rôle** : Génère le fil d'Ariane en remontant la hiérarchie des Nodes parents.

**Fichier** : `webapp/widgets/Breadcrumb.php`

```php
<?php
/**
 * File Breadcrumb.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use app\helpers\Parameters;
use blackcube\core\models\Composite;
use yii\base\Widget;
use Yii;

/**
 * Class Breadcrumb
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class Breadcrumb extends Widget
{

    public $element;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        $baseNode = $this->element->getNodes()
            ->one();
        $nodes = $baseNode
            ->getParents()
            ->andWhere(['>', 'level', 1])
            ->all();
        $nodes[] = $baseNode;
        $breadcrumb = [];
        $heroBlocTypeId = Parameters::get('BLOC', 'HERO');
        foreach ($nodes as $node) {
            $hero = $node->getBlocs()->andWhere(['blocTypeId' => $heroBlocTypeId])->one();
            $title = $hero?->title ?? $node->name;
            if ($hero!==null && $hero->hasAttribute('breadcrumbTitle') && !empty($hero->breadcrumbTitle)) {
                $title = $hero->breadcrumbTitle;
            }
            $breadcrumb[] = [
                'title' => $title,
                'route' => $node->getRoute(),
            ];
        }

        return $this->render('breadcrumb', [
            'breadcrumb' => $breadcrumb,

        ]);
    }
}
```

**Vue** : `webapp/widgets/views/breadcrumb.php`

```php
<?php
/**
 * breadcrumb.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $breadcrumb array
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Breadcrumb -->
<nav class="text-sm text-gray-500 mb-6">
    <?php foreach($breadcrumb as $i => $item): ?>
        <?php if (isset($item['route'])): ?>
            <?php echo Html::a(Html::encode($item['title']), Url::to([$item['route']]), [
                'class' => 'hover:text-primary transition'
            ]); ?>
        <?php else: ?>
            <span class="text-gray-900">
                <?php echo Html::encode($item['title']); ?>
            </span>
        <?php endif; ?>
        <?php if ($i < count($breadcrumb) - 1): ?>
            <span class="mx-2">/</span>
        <?php endif; ?>
    <?php endforeach; ?>

</nav>
```

**Explications** :
- Récupère le Node de l'élément via `getNodes()->one()`
- Récupère les parents avec filtre `['>', 'level', 1]` (ignore racine)
- Ajoute le `$baseNode` à la fin avec `$nodes[] = $baseNode`
- Priorité des titres : `breadcrumbTitle` > `title` > `name`
- Vue : séparateur "/" avec espacement `mx-2`
- Check `if (isset($item['route']))` pour savoir si cliquable

---

### Widget 8 : Navigation (Prev/Next)

**Rôle** : Génère les liens Précédent/Suivant avec les titres des articles entre les Composites du même Node.

**Fichier** : `webapp/widgets/Navigation.php`

```php
<?php
/**
 * File Navigation.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use app\helpers\Parameters;
use blackcube\core\models\Composite;
use yii\base\Widget;
use Yii;

/**
 * Class Navigation
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class Navigation extends Widget
{

    public $element;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);
        if ($this->element instanceof Composite) {
            $node = $this->element
                ->getNodes()
                ->active()
                ->one();
            $findNext = false;
            $previous = null;
            $next = null;
            foreach($node?->getComposites()->active()->each() as $composite) {
                if ($composite->id === $this->element->id) {
                    $findNext = true;
                    continue;
                }
                if ($findNext === false) {
                    $previous = $composite;
                } elseif ($findNext === true && $next === null) {
                    $next = $composite;
                    break;
                }
            }
            $previousRoute = $previous !== null ? $previous?->getRoute() : null;
            $heroBlocTypeId = Parameters::get('BLOC', 'HERO');
            $previousTitle = $previous?->getBlocs()
                ->andWhere(['blocTypeId' => $heroBlocTypeId])
                ->one()?->title ?? null;
            $nextRoute = $next !== null ? $next?->getRoute() : null;
            $nextTitle = $next?->getBlocs()
                ->andWhere(['blocTypeId' => $heroBlocTypeId])
                ->one()?->title ?? null;
        }
        return $this->render('navigation', [
                'previousRoute' => $previousRoute,
                'previousTitle' => $previousTitle,
                'nextRoute' => $nextRoute,
                'nextTitle' => $nextTitle,
        ]);
    }
}
```

**Vue** : `webapp/widgets/views/navigation.php`

```php
<?php
/**
 * navigation.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $nextRoute string
 * @var $nextTitle string
 * @var $previousRoute string
 * @var $previousTitle string
 */

use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Navigation -->
<div class="flex justify-between items-center pt-12 border-t border-gray-200 mt-16">
    <?php if ($previousRoute !== null): ?>
    <?php echo Html::a('<< '.$previousTitle, [$previousRoute], [
            'class' => 'inline-flex items-center text-primary hover:text-primary-600 font-medium transition'
            ]); ?>
    <?php else: ?>
        <div></div>
    <?php endif; ?>
    <?php if ($nextRoute !== null): ?>
        <?php echo Html::a($nextTitle.' >>', [$nextRoute], [
                'class' => 'inline-flex items-center text-right text-primary hover:text-primary-600 font-medium transition'
        ]); ?>
    <?php else: ?>
        <div></div>
    <?php endif; ?>
</div>
```

**Explications** :
- Vérifie que l'élément est un Composite
- Récupère le Node via `getNodes()->active()->one()`
- Utilise un algorithme avec flag `$findNext` au lieu d'index
- Parcourt avec `each()` (iterator) pour économiser la mémoire
- Quand trouve le Composite courant : `$findNext = true` et `continue`
- Avant le courant : stocke dans `$previous`
- Après le courant : stocke dans `$next` et `break`
- Récupère les TITRES via le Hero de chaque Composite
- Passe 4 variables à la vue : `previousRoute`, `previousTitle`, `nextRoute`, `nextTitle`
- Vue : affiche les titres avec flèches `<<` et `>>`

---

## Créer la Vue documentation.php

**Fichier** : `webapp/views/cms/documentation.php`

```php
<?php
/**
 * documentation.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $element \blackcube\core\models\Node|\blackcube\core\models\Composite
 * @var $hero \blackcube\core\models\Bloc
 * @var $blocs array
 */

use webapp\widgets;
use yii\helpers\Html;
use yii\helpers\Url;

if (isset($title) && $title !== null) {
    $this->title = $title;
}
?>

<?php echo widgets\Header::widget([
        'element' => $element
]); ?>

<div class="flex">
    <?php echo widgets\Sidebar::widget([
            'element' => $element
    ]); ?>
    <!-- Main Content -->
    <main class="flex-1 max-w-5xl mx-auto px-4 sm:px-6 lg:px-8 py-8">

        <?php echo widgets\Breadcrumb::widget([
                'element' => $element
        ]); ?>

        <?php echo widgets\BlocHero::widget([
                'type' => 'documentation',
                'bloc' => $hero
        ]); ?>


        <!-- Content Blocks -->
        <article class="prose prose-lg max-w-none">
            <?php echo widgets\Blocs::widget([
                    'type' => 'documentation',
                    'blocs' => $blocs
            ]); ?>
        </article>

        <?php echo widgets\Navigation::widget([
                'element' => $element
        ]); ?>

    </main>

    <?php echo widgets\Toc::widget([
            'element' => $element
    ]); ?>
</div>

<?php echo widgets\Footer::widget([]); ?>
```

**Explications** :
- Layout **Flexbox 3 colonnes** : Sidebar + Main + TOC
- Main limitée à `max-w-5xl` pour une lecture confortable
- Ordre des widgets : Breadcrumb, Hero, Blocs, Navigation
- Classes `prose prose-lg` : Tailwind Typography pour styliser automatiquement le contenu
- Passe `type='documentation'` à BlocHero ET Blocs (pour variantes)
- Sidebar et TOC en position sticky pour suivre le scroll

---

## Validation

Avant de continuer :

- actionDocumentation() ajoutée au CmsController
- Vue documentation.php créée avec layout 3 colonnes
- BlocTitle.php + vue créés (titres H2/H3/H4)
- BlocContent.php + vue créés (contenu WYSIWYG)
- BlocCode.php + vue créés (blocs de code)
- BlocInfo.php + vue créés (alertes colorées)
- Sidebar.php + vue créés (navigation hiérarchique)
- Toc.php + vue créés (table des matières avec cleanTitle)
- Breadcrumb.php + vue créés (fil d'Ariane)
- Navigation.php + vue créés (prev/next avec titres)
