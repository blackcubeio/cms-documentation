# Helpers

Simplification de l'affichage et manipulation du contenu

Blackcube fournit des helpers pour simplifier l'affichage et la manipulation du contenu dans vos vues. Ces helpers étendent les fonctionnalités de Yii2 avec des features spécifiques au CMS.

Concrètement, trois helpers sont disponibles : Element pour récupérer et filtrer les blocs d'un contenu, Html pour gérer les images et fichiers uploadés, et Quill pour nettoyer le HTML WYSIWYG. Ces helpers automatisent les tâches répétitives et garantissent la cohérence du rendu.

## Element Helper

Le helper **Element** permet de récupérer et filtrer les blocs d'un élément avec cache automatique.

### Namespace

```php
use blackcube\core\web\helpers\Element;
```

### Element::getDateCreate()

Récupère la date de création réelle d'un élément en tenant compte des blocs.

```php
Element::getDateCreate(ElementInterface $element): DateTime
```

**Comportement :**

Retourne le `MIN(dateCreate)` entre l'élément et ses blocs. Si un bloc a été créé avant l'élément, c'est la date du bloc qui est retournée.

**Exemple :**

```php
$realDateCreate = Element::getDateCreate($composite);
echo $realDateCreate->format('d/m/Y H:i');
```

### Element::getDateUpdate()

Récupère la date de modification réelle d'un élément en tenant compte des blocs.

```php
Element::getDateUpdate(ElementInterface $element): DateTime
```

**Comportement :**

Retourne le `MAX(dateUpdate)` entre l'élément et ses blocs. Si un bloc a été modifié après l'élément, c'est la date du bloc qui est retournée.

**Exemple :**

```php
$realDateUpdate = Element::getDateUpdate($composite);
echo $realDateUpdate->format('d/m/Y H:i');
```

### Element::getWithTypes()

Récupère les blocs d'un élément pour des types spécifiques.

```php
Element::getWithTypes(ElementInterface $element, array $selectedBlocTypeIds): Bloc[]
```

**Exemple :**

```php
use app\helpers\Parameters;

// Récupérer uniquement les blocs de contenu
$contentBlocs = Element::getWithTypes($composite, [
    Parameters::get('BLOC', 'CONTENT'),
    Parameters::get('BLOC', 'CODE'),
    Parameters::get('BLOC', 'INFO')
]);

foreach ($contentBlocs as $bloc) {
    echo Quill::cleanHtml($bloc->content);
}
```

### Element::getExceptTypes()

Récupère les blocs d'un élément sauf certains types.

```php
Element::getExceptTypes(ElementInterface $element, array $exceptBlocTypeIds): Bloc[]
```

**Exemple :**

```php
// Récupérer tous les blocs sauf le hero
$otherBlocs = Element::getExceptTypes($composite, [
    Parameters::get('BLOC', 'HERO')
]);
```

### Element::getFirstWithType()

Récupère le premier bloc d'un type spécifique.

```php
Element::getFirstWithType(ElementInterface $element, int $blocTypeId): ?Bloc
```

**Exemple :**

```php
// Récupérer le hero
$heroBloc = Element::getFirstWithType(
    $composite, 
    Parameters::get('BLOC', 'HERO')
);

if ($heroBloc !== null) {
    echo $heroBloc->title;
}
```

> **Info**
>
> Toutes les méthodes du helper Element utilisent un cache en mémoire pour éviter les requêtes SQL répétées durant la même requête HTTP.

## Html Helper

Le helper **Html** étend **yii\helpers\Html** et ajoute des fonctionnalités pour gérer les images et fichiers uploadés dans Blackcube.

### Namespace

```php
use blackcube\core\helpers\Html;
```

### Html::cacheImage()

Cache et redimensionne automatiquement une image.

```php
Html::cacheImage(string $imageLink, ?int $width = null, ?int $height = null): string
```

> **Attention**
> Les images uploadées dans Blackcube sont stockées avec un préfixe Flysystem (`@blackcubefs/...`) qui ne peut pas être utilisé directement dans une balise `<img>`. Le helper :
> 1. Résout le chemin Flysystem (local/S3/etc.)
> 2. Redimensionne si nécessaire
> 3. Met en cache dans `/cache/`
> 4. Retourne l'URL publique

**Exemple :**

```php
<?php if (!empty($bloc->image)): ?>
    <?php echo Html::img(Html::cacheImage($bloc->image, 600), [
        'alt' => $bloc->imageAlt ?? ''
    ]); ?>
<?php endif; ?>
```

**Comportement selon les dimensions :**

1. Largeur ET hauteur : Redimensionne en crop
2. Largeur seule : Redimensionne proportionnellement
3. Hauteur seule : Redimensionne proportionnellement
4. Aucune dimension : Copie sans redimensionnement

### Html::cacheFile()

Cache un fichier (PDF, documents) pour l'affichage.

```php
Html::cacheFile(string $fileLink): string
```

> **Attention**
> Les fichiers uploadés dans Blackcube sont stockés avec un préfixe Flysystem (`@blackcubefs/...`) qui ne peut pas être utilisé directement dans un lien. Le helper :
> 1. Résout le chemin Flysystem (local/S3/etc.)
> 2. Met en cache dans `/cache/`
> 3. Retourne l'URL publique

**Exemple :**

```php
<?php if (!empty($bloc->document)): ?>
    <?php echo Html::a('Télécharger le PDF', Html::cacheFile($bloc->document), [
        'target' => '_blank'
    ]); ?>
<?php endif; ?>
```

### Html::img()

Surcharge de **yii\helpers\Html::img()** avec support automatique de **cacheImage()**.

```php
Html::img(string|array $src, array $options = []): string
```

Si **$options['width']** et/ou **$options['height']** sont définis, appelle automatiquement **cacheImage()** avec ces dimensions.

## Quill Helper

Le helper **Quill** permet de nettoyer et manipuler le HTML généré par l'éditeur Quill.js (format WYSIWYG par défaut dans Blackcube).

### Namespace

```php
use blackcube\core\helpers\Quill;
```

### Quill::cleanHtml()

Nettoie le HTML généré par Quill en supprimant les éléments indésirables.

```php
Quill::cleanHtml(
    ?string $htmlContent, 
    bool $removeStyles = true, 
    bool $removeEmptyTags = true, 
    bool $removeSpan = true, 
    bool $addNoFollow = true
): ?string
```

> **Info**
> Ce helper a été développé pour garantir la conformité RGAA du contenu généré par l'éditeur WYSIWYG.

**Ce qui est nettoyé par défaut :**

1. Attributs `style`
2. Balises vides (`<p></p>`, `<p><br></p>`)
3. Balises `<span>` (contenu conservé)
4. Ajout de `rel="nofollow"` aux liens externes

**Exemple :**

```php
<?php if (!empty($bloc->content)): ?>
    <div class="content">
        <?php echo Quill::cleanHtml($bloc->content); ?>
    </div>
<?php endif; ?>
```

### Quill::toRaw()

Convertit le HTML en texte brut en gardant uniquement certaines balises.

```php
Quill::toRaw(?string $htmlContent, array $keepTags = ['p']): ?string
```

**Comportement :**

1. Nettoie d'abord le HTML avec **cleanHtml()**
2. Supprime toutes les balises sauf celles dans **$keepTags**

**Exemples :**

```php
$html = '<p><strong>Texte</strong> en <em>gras</em></p><p>Autre paragraphe</p>';

// Garder les <p>
echo Quill::toRaw($html);
// → <p>Texte en gras</p><p>Autre paragraphe</p>

// Tout supprimer
echo Quill::toRaw($html, []);
// → Texte en grasAutre paragraphe

// Garder <p> et <strong>
echo Quill::toRaw($html, ['p', 'strong']);
// → <p><strong>Texte</strong> en gras</p><p>Autre paragraphe</p>
```

**Cas d'usage :**

```php
// Meta description (texte pur)
$description = Quill::toRaw($bloc->content, []);
$description = mb_substr($description, 0, 160);

// Extrait accessible (garde les paragraphes)
$excerpt = Quill::toRaw($bloc->content);

// Flux RSS (texte avec formatage minimal)
$rssContent = Quill::toRaw($bloc->content, ['p', 'br', 'strong', 'em']);
```

## Bonnes pratiques

1. **Toujours utiliser Html::cacheImage()** pour afficher les images uploadées
2. **Toujours utiliser Html::cacheFile()** pour afficher les fichiers uploadés
3. **Toujours utiliser Quill::cleanHtml()** pour le contenu WYSIWYG

## Points clés à retenir

1. **Element Helper** : Récupération et filtrage des blocs avec cache automatique
2. **Html Helper** : Gestion des fichiers Flysystem, cache et redimensionnement automatiques
3. **Quill Helper** : Nettoyage RGAA du HTML, conversion en texte brut

> **Info**
>
> Les helpers Blackcube automatisent les tâches répétitives et garantissent la cohérence du rendu. Ils gèrent le cache, la sécurité et l'accessibilité automatiquement.
