# Paramètres

Configuration centralisée dans le CMS

Les paramètres permettent de stocker des valeurs de configuration dans le CMS. L'objectif : éviter de hardcoder les IDs de types de blocs et autres valeurs qui peuvent changer entre environnements.

Concrètement, au lieu d'écrire `['blocTypeId' => 1]` dans le code, on utilise `Parameters::get('BLOC', 'HERO')` qui récupère l'ID depuis la base. Le code devient explicite, documenté, et les migrations entre environnements sont facilitées.

## Pourquoi utiliser les paramètres

**Problème sans paramètres :**

```php
// ID hardcodé = maintenance difficile
$heroBloc = $composite->getBlocs()
    ->andWhere(['blocTypeId' => 1]) // Quel type de bloc ?
    ->one();
```

**Solution avec paramètres :**

```php
$heroBlocTypeId = Parameters::get('BLOC', 'HERO');
$heroBloc = $composite->getBlocs()
    ->andWhere(['blocTypeId' => $heroBlocTypeId])
    ->one();
```

**Avantages :**

- Code explicite et documenté
- Configuration centralisée
- Facilite les migrations entre environnements

## Création dans le back-office

### Accès à l'interface

**Paramètres** → **Paramètres** dans le menu d'administration

### Créer un paramètre

1. Cliquer sur **Créer un paramètre**
2. Renseigner les champs :
   - **Domaine** : Catégorie du paramètre (ex: `BLOC`, `CONFIG`)
   - **Nom** : Identifiant du paramètre (ex: `HERO`, `CONTENT`)
   - **Valeur** : Valeur numérique ou textuelle

> **Attention**
> Créer les paramètres avant de les utiliser dans le code. Si un paramètre n'existe pas, `Parameters::get()` retourne `null`.

## Fonctionnement

Le helper **Parameters** lit les paramètres en base de données et les met en cache en mémoire pour la durée de la requête.

**Comportement :**

1. Premier appel : Charge tous les paramètres depuis la table `parameter`
2. Appels suivants : Retourne les valeurs depuis le cache statique
3. Pas de requête SQL supplémentaire durant la requête HTTP

> **Info**
> Rien de magique : simple lecture en base avec cache statique. Les paramètres sont rechargés à chaque nouvelle requête HTTP.

## Utilisation

### Namespace

```php
use app\helpers\Parameters;
```

### Récupérer une valeur

```php
Parameters::get(string $domain, string $name): mixed
```

**Exemple :**

```php
$heroBlocTypeId = Parameters::get('BLOC', 'HERO');
```

### Dans les requêtes

**Récupérer un bloc spécifique :**

```php
$heroBloc = $composite->getBlocs()
    ->andWhere(['blocTypeId' => Parameters::get('BLOC', 'HERO')])
    ->one();

$pageTitle = $heroBloc->title ?? $composite->name;
```

**Plusieurs types de blocs :**

```php
$contentBlocs = $composite->getBlocs()
    ->andWhere(['blocTypeId' => [
        Parameters::get('BLOC', 'CONTENT'),
        Parameters::get('BLOC', 'CODE'),
        Parameters::get('BLOC', 'INFO')
    ]])
    ->all();
```

**Avec valeur par défaut :**

```php
$perPage = Parameters::get('CONFIG', 'ARTICLES_PER_PAGE') ?? 10;
```

## Exemple pratique

Génération d'un menu avec récupération des titres depuis les blocs Hero.

```php
class Sidebar extends Widget
{
    public $element;

    public function run()
    {
        // Récupération du paramètre
        $heroBlocTypeId = Parameters::get('BLOC', 'HERO');

        foreach($nodeList as $childNode) {
            $menu = [
                // Utilisation du paramètre pour récupérer le titre
                'title' => $childNode->getBlocs()
                        ->andWhere(['blocTypeId' => $heroBlocTypeId])
                        ->one()?->title ?? $childNode->name,
                'route' => $childNode->getRoute(),
                'active' => $childNode->id === $node->id
            ];
            
            $menuList[] = $menu;
        }

        return $this->render('sidebar', ['menuList' => $menuList]);
    }
}
```

## Cas d'usage

**Configuration générale :**

```php
$contactEmail = Parameters::get('CONFIG', 'CONTACT_EMAIL');
$siteName = Parameters::get('CONFIG', 'SITE_NAME');
```

**IDs de types :**

```php
$documentTypeId = Parameters::get('TYPE', 'DOCUMENT');
$newsTypeId = Parameters::get('TYPE', 'NEWS');
```

**Features flags :**

```php
$enableNewsletter = Parameters::get('FEATURE', 'NEWSLETTER_ENABLED');
```

> **Info**
> Les domaines (`BLOC`, `CONFIG`, `TYPE`, etc.) sont libres. Organiser selon les besoins du projet.

## Bonnes pratiques

1. **Nommer clairement** : Utiliser des noms explicites (`HERO`, `CONTACT_EMAIL`)
2. **Grouper par domaine** : Utiliser des domaines cohérents (`BLOC`, `CONFIG`, `TYPE`)
3. **Initialiser au déploiement** : Créer les paramètres avec une migration

## Points clés à retenir

1. **Configuration centralisée** : Les valeurs sont stockées dans le CMS, pas hardcodées
2. **Code explicite** : `Parameters::get('BLOC', 'HERO')` est auto-documenté
3. **Facilite les migrations** : Pas besoin de modifier le code entre environnements
4. **Cache automatique** : Une seule lecture en base par requête HTTP

> **Info**
>
> Les paramètres transforment le code opaque en code documenté. `['blocTypeId' => 1]` ne dit rien, `Parameters::get('BLOC', 'HERO')` est immédiatement compréhensible.