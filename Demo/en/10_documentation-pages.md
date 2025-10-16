# Documentation Page
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Home Page

---

## Objective

Create the action, widgets, and view to display documentation pages with 3-column layout (Sidebar + Content + TOC), breadcrumb, and prev/next navigation.

---

## actionDocumentation() Action

**Added to**: `webapp/controllers/CmsController.php`

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

**Operation**:
- Sets application language based on element
- Retrieves Hero block separately
- Retrieves all other blocks
- Groups consecutive Features with `CmsHelper::groupBlocs()`
- Passes everything to view

---

## Create Block Widgets

### Widget 1: BlocTitle

**Role**: Displays intermediate H2, H3, or H4 headings with dynamic sizing based on level.

**File**: `webapp/widgets/BlocTitle.php`

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

**View**: `webapp/widgets/views/bloc_title.php`

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
<!-- bloc_title -->
<?php echo Html::tag($bloc->hn, Html::encode($bloc->title), [
        'class' => $size.' font-bold text-accent mb-4 mt-12',
        'id' => 'title-'.$bloc->id
    ]); ?>
```

**Explanation**:
- Uses `Html::tag()` with dynamic level `$bloc->hn`
- Size adapted by level: H2 (3xl), H3 (2xl), H4 (xl)
- Generates unique ID `title-{id}` for TOC anchors
- Vertical spacing: `mt-12` and `mb-4`

---

### Widget 2: BlocContent

**Role**: Displays rich WYSIWYG content with HTML cleanup via `Quill::cleanHtml()`.

**File**: `webapp/widgets/BlocContent.php`

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

**View**: `webapp/widgets/views/bloc_content.php`

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

**Explanation**:
- Uses `Quill::cleanHtml()` to clean HTML from Quill editor
- `.content` class to style inner elements (lists, links, etc.)
- Vertical spacing `mb-8` between blocks

---

### Widget 3: BlocCode

**Role**: Displays code block with syntax highlighting and copy button.

**File**: `webapp/widgets/BlocCode.php`

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

**View**: `webapp/widgets/views/bloc_code.php`

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

**Explanation**:
- Escapes `<` and `>` characters to avoid HTML interpretation
- `code-highlight` attribute with language for JS syntax highlighting
- Copy button in absolute position (top-right)
- Dark background `bg-gray-900` with light text
- Horizontal scroll if code too wide

---

### Widget 4: BlocInfo

**Role**: Displays colored alert based on type (info, warning, error).

**File**: `webapp/widgets/BlocInfo.php`

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

**View**: `webapp/widgets/views/bloc_info.php`

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

**Explanation**:
- 3 color variants based on `$bloc->type`: info (blue), warning (yellow), error (red)
- Thick left border with `border-l-4`
- Different SVG icon based on alert type
- Light colored background with dark text for readability

---

## Create Layout Widgets

### Widget 5: Sidebar

**Role**: Generates hierarchical navigation in left column with list of Nodes and their Composites.

**File**: `webapp/widgets/Sidebar.php`

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

**View**: `webapp/widgets/views/sidebar.php`

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

**Explanation**:
- Handles Composite AND Node with `instanceof`
- For Composite: retrieves Node via `getNodes()->active()->one()`
- Retrieves level 2 parent Node with filter `['level' => 2]`
- Lists all child Nodes of `$topNode`
- For each Node: lists its Composites
- Uses Hero `title` field if available, otherwise `name`
- Active element with blue background and left border
- Sticky with `top-16` to follow scroll

---

### Widget 6: Toc (Table of Contents)

**Role**: Generates table of contents in right column by listing all H2, H3 headings.

**File**: `webapp/widgets/Toc.php`

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
        // 6a. Additional emoticons ( 1F600 - 1F636 )
        $clean = preg_replace('/[\x{1F600}-\x{1F636}]/u', '', $clean);
        // 6b. Additional transport and map symbols ( 1F681 - 1F6C5 )
        $clean = preg_replace('/[\x{1F681}-\x{1F6C5}]/u', '', $clean);
        // 6c. Other additional symbols ( 1F30D - 1F567 )
        $clean = preg_replace('/[\x{1F30D}-\x{1F567}]/u', '', $clean);
        return trim($clean);
    }
}
```

**View**: `webapp/widgets/views/toc.php`

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

**Explanation**:
- Retrieves all TITLE type blocks via `each()` (iterator)
- Extracts level (H2/H3/H4) with `$level = null` by default
- Checks `if($level !== null)` before adding to `$links`
- Calls `cleanTitle()` to clean title (emoji, parentheses, numbers)
- View: displays only if `!empty($links)`
- Shows H2 and H3 only (`level <= 2` and `level === 3`)
- H3 with indentation `pl-3`
- Title translation with `Yii::t('app', 'On this page')`

---

### Widget 7: Breadcrumb

**Role**: Generates breadcrumb trail by climbing Node parent hierarchy.

**File**: `webapp/widgets/Breadcrumb.php`

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

**View**: `webapp/widgets/views/breadcrumb.php`

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

**Explanation**:
- Retrieves element's Node via `getNodes()->one()`
- Retrieves parents with filter `['>', 'level', 1]` (ignore root)
- Adds `$baseNode` at end with `$nodes[] = $baseNode`
- Title priority: `breadcrumbTitle` > `title` > `name`
- View: "/" separator with spacing `mx-2`
- Checks `if (isset($item['route']))` to know if clickable

---

### Widget 8: Navigation (Prev/Next)

**Role**: Generates Previous/Next links with article titles between Composites of same Node.

**File**: `webapp/widgets/Navigation.php`

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

**View**: `webapp/widgets/views/navigation.php`

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

**Explanation**:
- Verifies element is a Composite
- Retrieves Node via `getNodes()->active()->one()`
- Uses algorithm with `$findNext` flag instead of index
- Loops with `each()` (iterator) to save memory
- When finds current Composite: `$findNext = true` and `continue`
- Before current: store in `$previous`
- After current: store in `$next` and `break`
- Retrieves TITLES via each Composite's Hero
- Passes 4 variables to view: `previousRoute`, `previousTitle`, `nextRoute`, `nextTitle`
- View: displays titles with arrows `<<` and `>>`

---

## Create documentation.php View

**File**: `webapp/views/cms/documentation.php`

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

**Explanation**:
- **Flexbox 3-column layout**: Sidebar + Main + TOC
- Main limited to `max-w-5xl` for comfortable reading
- Widget order: Breadcrumb, Hero, Blocs, Navigation
- `prose prose-lg` classes: Tailwind Typography to automatically style content
- Passes `type='documentation'` to BlocHero AND Blocs (for variants)
- Sidebar and TOC in sticky position to follow scroll

---

## Validation

Before continuing:

- actionDocumentation() added to CmsController
- documentation.php view created with 3-column layout
- BlocTitle.php + view created (H2/H3/H4 headings)
- BlocContent.php + view created (WYSIWYG content)
- BlocCode.php + view created (code blocks)
- BlocInfo.php + view created (colored alerts)
- Sidebar.php + view created (hierarchical navigation)
- Toc.php + view created (table of contents with cleanTitle)
- Breadcrumb.php + view created (breadcrumb trail)
- Navigation.php + view created (prev/next with titles)