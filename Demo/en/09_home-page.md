# Home Page
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Redirect Pages

---

## Objective

Create the action, widgets, and view to display FR/EN home pages with Hero, Features, and CTA.

---

## actionHome() Action

**Added to**: `webapp/controllers/CmsController.php`

```php
public function actionHome()
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
        return $this->render('home', [
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

## Create home.php View

**File**: `webapp/views/cms/home.php`

```php
<?php

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

<?php echo widgets\BlocHero::widget([
    'type' => 'home',
    'bloc' => $hero
]); ?>

<?php echo widgets\Blocs::widget([
    'blocs' => $blocs
]); ?>


<?php echo widgets\Footer::widget([]); ?>
```

---

## Create Blocs.php Dispatcher

**Role**: Detects each block type and calls appropriate widget. Also handles block groups.

**File**: `webapp/widgets/Blocs.php`

```php
<?php

namespace webapp\widgets;

use app\helpers\Parameters;
use blackcube\core\models\Bloc;
use yii\base\Widget;
use Yii;

class Blocs extends Widget
{
    public $type;
    public array $blocs = [];

    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);
        $html = '';
        foreach($this->blocs as $bloc) {
            if ($bloc instanceof Bloc) {
                $widgetClass = $this->mapWidget($bloc);
                $config = [
                    'bloc' => $bloc
                ];
                if ($widgetClass === BlocCta::class) {
                    $config['type'] = $this->type;
                }
                if($widgetClass !== null) {
                    $html .= $widgetClass::widget($config);
                }
            } elseif (is_array($bloc) && !empty($bloc)) {
                $widgetClass = $this->mapWidget($bloc[0]);
                if($widgetClass !== null) {
                    $html .= $widgetClass::widget([
                        'blocs' => $bloc
                    ]);
                }
            }

        }
        return $html;
    }

    private function mapWidget($bloc) {
        switch($bloc->blocTypeId) {
            case Parameters::get('BLOC', 'HERO'):
                return BlocHero::class;
            case Parameters::get('BLOC', 'TITLE'):
                return BlocTitle::class;
            case Parameters::get('BLOC', 'CONTENT'):
                return BlocContent::class;
            case Parameters::get('BLOC', 'CODE'):
                return BlocCode::class;
            case Parameters::get('BLOC', 'INFO'):
                return BlocInfo::class;
            case Parameters::get('BLOC', 'FEATURE'):
                return SectionFeatures::class;
            case Parameters::get('BLOC', 'CTA'):
                return BlocCta::class;
            case Parameters::get('BLOC', 'CARD'):
                return BlocCard::class;
            default:
                return null;
        }
    }
}
```

**Explanation**:
- Loops through received blocks
- If `Bloc`: individual block, calls mapped widget
- If array: block group (Features), calls `SectionFeatures`
- `mapWidget()` method maps BlocTypeId → Widget
- `$type` property passed to CTA widget to handle variants

---

## Create BlocHero.php

**Role**: Displays Hero block with 3 variants based on page type.

**File**: `webapp/widgets/BlocHero.php`

```php
<?php

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

class BlocHero extends Widget
{
    public $type = 'documentation'; // contact | documentation | home
    public $bloc;

    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);
        $view = 'bloc_hero_'. $this->type;
        return $this->render($view, [
            'bloc' => $this->bloc
        ]);
    }
}
```

### Home View: bloc_hero_home.php

**File**: `webapp/widgets/views/bloc_hero_home.php`

```php
<?php

use blackcube\core\models\Bloc;
use blackcube\core\web\helpers\Quill;
use blackcube\core\web\helpers\Html;
use yii\helpers\Url;
?>
<!-- Hero Section -->
<section class="relative bg-accent text-white overflow-hidden">
    <?php if (!empty($bloc->image)): ?>
    <!-- Background Image with Overlay -->
    <div class="absolute inset-0 z-0">
        <?php echo Html::img($bloc->image, [
            'class' => 'w-full h-full object-cover opacity-40',
            'alt' => Html::encode($bloc->title),
            'loading' => 'lazy',
            'width' => 1200,
        ]); ?>

        <div class="absolute inset-0 bg-gradient-to-br from-accent via-accent-light to-accent opacity-80"></div>
    </div>
    <?php endif; ?>

    <!-- Content -->
    <div class="relative z-10 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-24 sm:py-32">
        <div class="max-w-3xl">
            <h1 class="text-5xl sm:text-6xl font-bold mb-6 leading-tight">
                <?php echo Html::encode($bloc->title); ?>
            </h1>
            <div class="text-xl sm:text-2xl text-gray-300 mb-10 leading-relaxed">
                <?php echo Quill::cleanHtml($bloc->description); ?>
            </div>
            <div class="flex flex-col sm:flex-row gap-4">
                <?php if (!empty($bloc->ctaTitle) && !empty($bloc->ctaTarget)): ?>
                <?php echo Html::a($bloc->ctaTitle, Url::toRoute($bloc->ctaTarget), [
                    'class' => 'inline-flex items-center justify-center px-8 py-4 bg-primary hover:bg-primary-600 text-white font-semibold rounded-lg shadow-lg transition transform hover:scale-105',
                    ]); ?>
                <?php endif; ?>
                <?php if (!empty($bloc->ctaSecondaryTitle) && !empty($bloc->ctaSecondaryTarget)): ?>
                    <?php echo Html::a($bloc->ctaSecondaryTitle, Url::toRoute($bloc->ctaSecondaryTarget), [
                            'class' => 'inline-flex items-center justify-center px-8 py-4 border-2 border-white/30 hover:border-white text-white font-semibold rounded-lg transition',
                    ]); ?>
                <?php endif; ?>
            </div>
        </div>
    </div>
</section>
```

### Documentation View: bloc_hero_documentation.php

**File**: `webapp/widgets/views/bloc_hero_documentation.php`

```php
<?php

use blackcube\core\models\Bloc;
use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Hero Section -->
<div class="relative bg-gradient-to-br from-accent to-accent-light text-white rounded-xl p-12 mb-12 overflow-hidden">
    <?php if (!empty($bloc->image)): ?>
    <div class="absolute inset-0 opacity-10">
        <?php echo Html::img($bloc->image, [
                'class' => 'w-full h-full object-cover',
                'alt' => Html::encode($bloc->title),
                'loading' => 'lazy',
                'width' => 1200,
        ]); ?>
    </div>
    <?php endif; ?>
    <div class="relative z-10">
        <h1 class="text-4xl sm:text-5xl font-bold mb-4">
            <?php echo Html::encode($bloc->title); ?>
        </h1>
        <div class="text-xl text-gray-300 max-w-2xl">
            <?php echo Quill::cleanHtml($bloc->description); ?>
        </div>
    </div>
</div>
```

### Contact View: bloc_hero_contact.php

**File**: `webapp/widgets/views/bloc_hero_contact.php`

```php
<?php

use blackcube\core\models\Bloc;
use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Hero Section -->
<section class="relative bg-gradient-to-br from-accent to-accent-light text-white overflow-hidden">
    <?php if (!empty($bloc->image)): ?>
    <div class="absolute inset-0 opacity-10">
        <?php echo Html::img($bloc->image, [
                'class' => 'w-full h-full object-cover',
                'alt' => Html::encode($bloc->title),
                'loading' => 'lazy',
                'width' => 1200,
        ]); ?>
    </div>
    <?php endif; ?>
    <div class="relative z-10 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-20">
        <h1 class="text-4xl sm:text-5xl font-bold mb-4">
            <?php echo Html::encode($bloc->title); ?>
        </h1>
        <div class="text-xl text-gray-300 max-w-2xl">
            <?php echo Quill::cleanHtml($bloc->description); ?>
        </div>
    </div>
</section>
```

---

## Create BlocFeature.php

**Role**: Displays individual feature with SVG processing.

**File**: `webapp/widgets/BlocFeature.php`

```php
<?php

namespace webapp\widgets;

use blackcube\core\components\Flysystem;
use blackcube\core\web\helpers\Html;
use yii\base\Widget;
use Yii;

class BlocFeature extends Widget
{
    public $bloc;

    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);
        $icon = false;
        $cacheKey = null;
        if (Yii::$app->cache !== null) {
            $cacheKey = sha1('BlocFeature:'.$this->bloc->icon);
            $icon = Yii::$app->cache->get($cacheKey);
        }
        if ($icon === false) {
            $cacheFile = Yii::getAlias('@webroot'.Html::cacheFile($this->bloc->icon));
            $icon = file_get_contents($cacheFile);
            $icon = preg_replace('/<svg /', '<svg class="w-6 h-6 text-primary" ', $icon);
            if (Yii::$app->cache !== null) {
                $cacheDuration = 86400;
                Yii::$app->cache->set($cacheKey, $icon, $cacheDuration);
            }
        }

        return $this->render('bloc_feature', [
            'bloc' => $this->bloc,
            'icon' => $icon,
        ]);
    }
}
```

**View**: `webapp/widgets/views/bloc_feature.php`

```php
<?php

use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Feature 1 -->
<div class="bg-white rounded-lg p-8 shadow-md hover:shadow-lg transition">
    <div class="w-12 h-12 bg-primary/10 rounded-lg flex items-center justify-center mb-6">
        <?php echo $icon; ?>
    </div>
    <h3 class="text-xl font-semibold mb-3 text-accent">
        <?php echo Html::encode($bloc->title); ?>
    </h3>
    <div class="text-gray-600 leading-relaxed">
        <?php echo Quill::cleanHtml($bloc->description); ?>
    </div>
</div>
```

---

## Create SectionFeatures.php

**Role**: Displays feature group in 3-column grid.

**File**: `webapp/widgets/SectionFeatures.php`

```php
<?php

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

class SectionFeatures extends Widget
{
    public $blocs;

    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        return $this->render('section_features', [
            'blocs' => $this->blocs,
        ]);
    }
}
```

**View**: `webapp/widgets/views/section_features.php`

```php
<?php

use webapp\widgets;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Features Section -->
<section class="py-20 bg-gray-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="grid md:grid-cols-3 gap-8">
            <?php foreach ($blocs as $bloc): ?>
            <?php echo widgets\BlocFeature::widget([
                'bloc' => $bloc
                ]); ?>
            <?php endforeach; ?>

        </div>
    </div>
</section>
```

---

## Create BlocCta.php

**Role**: Displays call-to-action with 2 variants (default and document).

**File**: `webapp/widgets/BlocCta.php`

```php
<?php

namespace webapp\widgets;

use yii\base\Widget;
use Yii;

class BlocCta extends Widget
{
    public $type = '';
    public $bloc;

    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);

        $view = 'bloc_cta';
        if ($this->type !== null && trim($this->type) !== '') {
            $view .= '_'.$this->type;
        }
        return $this->render($view, [
            'bloc' => $this->bloc,
        ]);
    }
}
```

### Default View: bloc_cta.php

**File**: `webapp/widgets/views/bloc_cta.php`

```php
<?php

use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- CTA Section -->
<section class="py-20 bg-accent text-white">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
        <h2 class="text-3xl sm:text-4xl font-bold mb-6">
            <?php echo Html::encode($bloc->title); ?>
        </h2>
        <div class="text-xl text-gray-300 mb-10">
            <?php echo Quill::cleanHtml($bloc->description); ?>
        </div>
        <?php if (!empty($bloc->ctaTitle) && !empty($bloc->ctaTarget)): ?>
            <?php echo Html::a($bloc->ctaTitle, Url::toRoute($bloc->ctaTarget), [
                    'class' => 'inline-flex items-center justify-center px-8 py-4 bg-primary hover:bg-primary-600 text-white font-semibold rounded-lg shadow-lg transition transform hover:scale-105',
            ]); ?>
        <?php endif; ?>
    </div>
</section>
```

### Document View: bloc_cta_document.php

**File**: `webapp/widgets/views/bloc_cta_document.php`

```php
<?php

use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- CTA Section -->
<section class="py-5 bg-accent text-white rounded rounded-2xl">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
        <p class="text-xl font-bold mb-2">
            <?php echo Html::encode($bloc->title); ?>
        </p>
        <div class="text-gray-300 mb-2">
            <?php echo Quill::cleanHtml($bloc->description); ?>
        </div>
        <?php if (!empty($bloc->ctaTitle) && !empty($bloc->ctaTarget)): ?>
            <?php echo Html::a($bloc->ctaTitle, Url::toRoute($bloc->ctaTarget), [
                    'class' => 'inline-flex items-center justify-center px-8 py-2 bg-primary hover:bg-primary-600 text-white font-semibold rounded-lg shadow-lg transition transform hover:scale-105',
            ]); ?>
        <?php endif; ?>
    </div>
</section>
```

---

## Validation

Before continuing:

- actionHome() added to CmsController
- home.php view created
- Blocs.php dispatcher created with widget mapping
- BlocHero.php + 3 views (home, documentation, contact) created
- BlocFeature.php + view created
- SectionFeatures.php + view created
- BlocCta.php + 2 views (default, document) created