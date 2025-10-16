# Page Contact
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Page Documentation

---

## Objectif

Créer l'action, le modèle, les widgets et les vues pour afficher la page de contact avec formulaire d'envoi d'email et cards informatives en sidebar.

---

## Créer le Modèle MailContact

**Rôle** : Modèle de validation du formulaire de contact avec 4 champs (email, name, subject, message).

**Fichier** : `app/models/MailContact.php`

```php
<?php


namespace app\models;

use yii\base\Model;

class MailContact extends Model
{
    public $email;
    public $name;
    public $subject;
    public $message;

    public function rules()
    {
        return [
            [['email', 'name', 'subject', 'message'], 'required'],
            [['email'], 'email'],
        ];
    }

    public function attributeLabels()
    {
        return [
            'email' => \Yii::t('app', 'Email'),
            'name' => \Yii::t('app','Name'),
            'subject' => \Yii::t('app','Subject'),
            'message' => \Yii::t('app','Message'),
        ];
    }

    public function attributeHints()
    {
        return [
            'email' => \Yii::t('app', 'john.doe@example.com'),
            'name' => \Yii::t('app','John Doe'),
            'subject' => \Yii::t('app','Question about ...'),
            'message' => \Yii::t('app','Describe your request...'),
        ];
    }
}
```

---

## Action actionContact()

**Ajout dans** : `webapp/controllers/CmsController.php`

```php
public function actionContact()
{
    try {
        Yii::debug('Trace :'.__METHOD__, __METHOD__);
        Yii::$app->language = $this->element->languageId;
        $heroBlocTypeId = Parameters::get('BLOC', 'HERO');
        $contactBlocTypeId = Parameters::get('BLOC', 'CONTACT');
        $featureBlocTypeId = Parameters::get('BLOC', 'FEATURE');
        $hero = $this->element->getBlocs()
            ->andWhere(['blocTypeId' => $heroBlocTypeId])
            ->one();
        $contact = $this->element->getBlocs()
            ->andWhere(['blocTypeId' => $contactBlocTypeId])
            ->one();
        $blocs = $this->element->getBlocs()
            ->andWhere(['not in', 'blocTypeId', [$heroBlocTypeId, $contactBlocTypeId]])
            ->all();

        $mailContact = Yii::createObject(MailContact::class);
        if (Yii::$app->request->isPost) {
            $mailContact->load(Yii::$app->request->bodyParams);
            if ($mailContact->validate()) {

                $message = "Nouveau message depuis le formulaire de contact :\n";
                $message .= "Nom : ".$mailContact->name."\n";
                $message .= "Email : ".$mailContact->email."\n";
                $message .= "Contenu: \n".$mailContact->message;
                $from = getstrenv('MAILJET_FROM');
                $to = getstrenv('MAILJET_TO');

                $data = Yii::$app->mailer
                    ->compose()
                    ->setFrom($from)
                    ->setTo($to)
                    ->setSubject('Blackcube doc: '.$mailContact->subject)
                    ->setTextBody($message)
                    ->send();
                return $this->render('contact-success',
                    [
                        'element' => $this->element,
                        'hero' => $hero,
                        'contact' => $contact,
                        'blocs' => CmsHelper::groupBlocs($blocs, [$featureBlocTypeId]),
                    ]);
            }
        }

        return $this->render('contact', [
            'element' => $this->element,
            'hero' => $hero,
            'contact' => $contact,
            'blocs' => CmsHelper::groupBlocs($blocs, [$featureBlocTypeId]),
            'mailContact' => $mailContact,
        ]);
    } catch (Exception $e) {
        Yii::error($e->getMessage(), __METHOD__);
        throw $e;
    }
}
```

**Fonctionnement** :

- Récupère les IDs des BlocTypes via `Parameters::get()` pour éviter le hardcoding
- Sépare Hero (BlocType HERO) et bloc Contact (BlocType CONTACT)
- Récupère tous les autres blocs (Cards de la sidebar)
- Groupe les Features avec `CmsHelper::groupBlocs()` pour les afficher ensemble
- Utilise `getstrenv()` pour récupérer les emails from/to depuis l'environnement
- Redirige vers `contact-success.php` après envoi réussi

> **Info**
> `getstrenv()` est une fonction helper qui récupère les variables d'environnement avec un fallback par défaut (cherche dans `$_ENV` puis `$_SERVER`).

---

## Créer le Widget BlocCard

**Rôle** : Affiche une card informative avec icône SVG, titre, description et lien optionnel.

**Fichier** : `webapp/widgets/BlocCard.php`

```php
<?php
/**
 * File BlocCard.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */

namespace webapp\widgets;

use blackcube\core\web\helpers\Html;
use yii\base\Widget;
use Yii;

/**
 * Class BlocCard
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 */
class BlocCard extends Widget
{

    public $bloc;
    /**
     * {@inheritDoc}
     */
    public function run()
    {
        Yii::debug('Trace: '.__METHOD__, __METHOD__);
        $icon = false;
        $cacheKey = null;
        if (Yii::$app->cache !== null) {
            $cacheKey = sha1('BlocCard:'.$this->bloc->icon);
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
        return $this->render('bloc_card', [
            'bloc' => $this->bloc,
            'icon' => $icon,
        ]);
    }
}
```

**Explications** :

- Utilise `Html::cacheFile()` pour obtenir le chemin du fichier SVG géré par Blackcube
- Système de cache avec clé unique basée sur le nom du fichier icon
- Injecte dynamiquement les classes Tailwind dans le SVG via `preg_replace()`
- Durée de cache : 24h

> **Info**
> `Html::cacheFile()` est un helper Blackcube qui met en cache le fichier et retourne le chemin utilisable que le fichier ait été uploadé en local ou en S3.

**Vue** : `webapp/widgets/views/bloc_card.php`

```php
<?php
/**
 * bloc_card.php
 *
 * PHP version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $this yii\web\View
 * @var $bloc Bloc;
 * @var $icon string
 */

use blackcube\core\models\Bloc;
use blackcube\core\web\helpers\Quill;
use yii\helpers\Html;
use yii\helpers\Url;
?>
<!-- Card -->
<div class="bg-white rounded-lg shadow-md p-6">
    <div class="flex items-start">
        <div class="flex-shrink-0">
            <div class="w-12 h-12 bg-primary/10 rounded-lg flex items-center justify-center">
                <?php echo $icon; ?>
            </div>
        </div>
        <div class="ml-4">
            <h3 class="text-lg font-semibold text-accent mb-2">
                <?php echo Html::encode($bloc->title); ?>
            </h3>
            <div class="text-gray-600 text-sm mb-3">
                <?php echo Quill::cleanHtml($bloc->description); ?>
            </div>
            <?php echo Html::a($bloc->link, (!empty($bloc->linkUrl) ? $bloc->linkUrl : [$bloc->linkRoute]), [
                'class' => 'text-primary hover:text-primary-600 font-medium text-sm transition inline-flex items-center',
                'target' => !empty($bloc->linkUrl) ? '_blank' : null,
            ]); ?>
        </div>
    </div>
</div>
```

**Explications** :

- Utilise `Quill::cleanHtml()` pour nettoyer et afficher le contenu riche de la description
- Lien intelligent : priorise `linkUrl` (URL externe), sinon utilise `linkRoute` (route interne)

> **Info**
> `Quill::cleanHtml()` est un helper Blackcube qui nettoie le HTML généré par l'éditeur Quill utilisé dans le back-office.

---

## Créer la Vue contact.php

**Fichier** : `webapp/views/cms/contact.php`

```php
<?php
/**
 * contact.php
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
 * @var $contact \blackcube\core\models\Bloc
 * @var $mailContact \app\models\MailContact
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

<?php echo widgets\BlocHero::widget([
        'type' => 'contact',
        'bloc' => $hero
]); ?>

<!-- Contact Form Section -->
<section class="py-16 bg-gray-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="grid lg:grid-cols-5 gap-12">

            <!-- Form (60%) -->
            <div class="lg:col-span-3">
                <div class="bg-white rounded-lg shadow-md p-8">
                    <h2 class="text-2xl font-bold text-accent mb-6">
                        <?php echo Html::encode($contact->title); ?>
                    </h2>

                    <?php echo Html::beginForm('', 'post', [
                            'novalidate' => 'novalidate',
                            'class' => 'space-y-6'
                    ]); ?>
                        <!-- Name -->
                        <div>

                            <?php echo Html::activeLabel($mailContact, 'name', [
                                    'class' => ($mailContact->hasErrors('name')?'text-red-700':'text-gray-700').' block text-sm font-medium mb-2'
                            ]); ?>
                            <?php echo Html::activeTextInput($mailContact, 'name', [
                                    'class' => ($mailContact->hasErrors('name')?'border-red-300':'border-gray-300').' w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-primary focus:border-transparent transition',
                                    'placeholder' => $mailContact->getAttributeHint('name'),
                                    'required' => true,
                            ]); ?>
                        </div>

                        <!-- Email -->
                        <div>
                            <?php echo Html::activeLabel($mailContact, 'email', [
                                    'class' => ($mailContact->hasErrors('email')?'text-red-700':'text-gray-700').' block text-sm font-medium mb-2'
                            ]); ?>
                            <?php echo Html::activeTextInput($mailContact, 'email', [
                                    'class' => ($mailContact->hasErrors('email')?'border-red-300':'border-gray-300').' w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-primary focus:border-transparent transition',
                                    'placeholder' => $mailContact->getAttributeHint('email'),
                                    'required' => true,
                            ]); ?>
                        </div>

                        <!-- Subject -->
                        <div>
                            <?php echo Html::activeLabel($mailContact, 'subject', [
                                    'class' => ($mailContact->hasErrors('subject')?'text-red-700':'text-gray-700').' block text-sm font-medium mb-2'
                            ]); ?>
                            <?php echo Html::activeTextInput($mailContact, 'subject', [
                                    'class' => ($mailContact->hasErrors('subject')?'border-red-300':'border-gray-300').' w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-primary focus:border-transparent transition',
                                    'placeholder' => $mailContact->getAttributeHint('subject'),
                                    'required' => true,
                            ]); ?>
                        </div>

                        <!-- Message -->
                        <div>
                            <?php echo Html::activeLabel($mailContact, 'message', [
                                    'class' => ($mailContact->hasErrors('message')?'text-red-700':'text-gray-700').' block text-sm font-medium mb-2'
                            ]); ?>
                            <?php echo Html::activeTextarea($mailContact, 'message', [
                                    'class' => ($mailContact->hasErrors('message')?'border-red-300':'border-gray-300').' w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-primary focus:border-transparent transition resize-none',
                                    'placeholder' => $mailContact->getAttributeHint('message'),
                                'required' => true,
                                'rows' => 6,
                            ]); ?>
                        </div>

                        <!-- Submit Button -->
                        <div>
                            <button
                                    type="submit"
                                    class="w-full inline-flex items-center justify-center px-8 py-4 bg-primary hover:bg-primary-600 text-white font-semibold rounded-lg shadow-lg transition transform hover:scale-105 cursor-pointer">
                                <?php echo Yii::t('app', 'Send Message'); ?>
                                <svg class="w-5 h-5 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3"/>
                                </svg>
                            </button>
                        </div>
                    <?php echo Html::endForm(); ?>
                </div>
            </div>

            <!-- Info Sidebar (40%) -->
            <div class="lg:col-span-2 space-y-6">
                    <?php echo widgets\Blocs::widget([
                            'blocs' => $blocs
                    ]); ?>
            </div>

        </div>
    </div>
</section>

<?php echo widgets\Footer::widget([]); ?>
```

**Explications** :

- Grille 5 colonnes avec répartition 3/2 (60% formulaire, 40% sidebar)
- Sidebar utilise le dispatcher `Blocs` qui affichera automatiquement les widgets `BlocCard` pour chaque bloc de type Card

---

## Créer la Vue contact-success.php

**Fichier** : `webapp/views/cms/contact-success.php`

```php
<?php
/**
 * contact-success.php
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
 * @var $contact \blackcube\core\models\Bloc
 * @var $blocs array
 */

use blackcube\core\web\helpers\Quill;
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
        'type' => 'contact',
        'bloc' => $hero
]); ?>

<!-- Contact Form Section -->
<section class="py-16 bg-gray-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="grid lg:grid-cols-5 gap-12">

            <!-- Form (60%) -->
            <div class="lg:col-span-3">
                <div class="bg-white rounded-lg shadow-md p-8">
                    <h2 class="text-2xl font-bold text-accent mb-6">
                        <?php echo Html::encode($contact->successTitle); ?>
                    </h2>

                    <?php echo Quill::cleanHtml($contact->success); ?>
                </div>
            </div>

            <!-- Info Sidebar (40%) -->
            <div class="lg:col-span-2 space-y-6">
                    <?php echo widgets\Blocs::widget([
                            'blocs' => $blocs
                    ]); ?>
            </div>

        </div>
    </div>
</section>

<?php echo widgets\Footer::widget([]); ?>
```

**Explications** :

- Utilise les champs `successTitle` et `success` du bloc Contact
- `Quill::cleanHtml()` nettoie le message de confirmation riche

---

## Validation

Avant de continuer :

- MailContact.php créé (modèle de validation)
- actionContact() ajoutée au CmsController (gestion POST et email)
- BlocCard.php + vue créés (cards sidebar avec cache SVG)
- Vue contact.php créée (formulaire complet)
- Vue contact-success.php créée (page de confirmation)