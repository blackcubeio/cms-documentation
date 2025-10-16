# Cache granulaire

Invalidation automatique basée sur les dépendances

Blackcube intègre un système de cache intelligent basé sur les dépendances de base de données. Contrairement aux CMS traditionnels où il faut gérer manuellement l'invalidation du cache, Blackcube détecte automatiquement les modifications et invalide uniquement le cache concerné.

Le cache doit être une optimisation, pas un pansement. Blackcube utilise DbQueryDependency de Yii2 avec un système de dépendances granulaires : chaque requête définit quelles tables surveiller, et le cache s'invalide automatiquement dès qu'une modification survient.

## Le problème du cache classique

### Approche traditionnelle

Dans la plupart des applications, le cache fonctionne ainsi :

```php
// Mise en cache
$data = $cache->get('my-data');
if ($data === false) {
    $data = expensiveQuery();
    $cache->set('my-data', $data, 3600); // 1 heure
}
```

**Invalidation manuelle obligatoire**

```php
// À chaque modification, invalider manuellement
$model->save();
$cache->delete('my-data'); // ← Facile à oublier !
```

**Cache trop large ou trop étroit**

1. Trop large : Invalider tout le cache alors qu'une seule donnée a changé
2. Trop étroit : Multiplier les clés de cache et les invalidations

**Données obsolètes**

1. Si on oublie d'invalider, les utilisateurs voient des données périmées
2. Ou on réduit drastiquement la durée du cache (perte de performance)

### La solution Blackcube

Blackcube utilise DbQueryDependency de Yii2 avec un système de dépendances granulaires.

**Principe :**

1. Chaque requête définit ses dépendances (quelles tables surveiller)
2. Blackcube surveille automatiquement le MAX(dateUpdate) de ces tables
3. Dès qu'une table change, **seul le cache dépendant de cette table est invalidé**
4. **Aucune invalidation manuelle nécessaire**

**Résultat :** Cache performant + données toujours à jour + zéro gestion manuelle.

## Architecture du cache Blackcube

### QueryCache Helper

Blackcube fournit le helper QueryCache qui centralise toutes les dépendances.

**Dépendances disponibles :**

- getCmsDependencies() : Toutes les tables du CMS
- getNodeDependencies() : Nodes et leurs relations
- getCompositeDependencies() : Composites et leurs relations
- getCategoryDependencies() : Catégories
- getTagDependencies() : Tags
- getMenuDependencies() : Menus
- getSlugDependencies() : URLs et redirections
- getLanguageDependencies() : Langues
- getTypeDependencies() : Types de contenu

### Granularité des dépendances

**Exemple : Node dependencies**

Surveille les modifications sur :

1. Table `nodes` : Modification d'un node
2. Table `nodes_blocs` : Ajout/suppression de blocs dans un node
3. Table `nodes_composites` : Liaison node ↔ composite
4. Table `blocs` : Modification du contenu d'un bloc
5. Table `blocTypes` : Modification d'un BlocType

**Résultat :** Dès qu'une de ces tables change, **toutes les requêtes en cache utilisant cette dépendance sont invalidées**.

> **Info**
>
> Le cache des composites, tags, menus, etc. reste valide car ils ont leurs propres dépendances.

## Utilisation dans le code

### Dans les requêtes Blackcube (automatique)

Les modèles Blackcube utilisent déjà le cache automatiquement dans certaines requêtes critiques.

**Exemple : Recherche de slug par URL**

```php
// Code interne de Blackcube
public static function findByPathinfoAndHostname($pathInfo, $hostname = null)
{
    if(Module::getInstance()->slugSensitive === true) {
        $expression = new Expression('BINARY LOWER([[path]]) LIKE LOWER(:path)', [
            ':path' => $pathInfo
        ]);
    } else {
        $expression = [
            'path' => $pathInfo,
        ];
    }
    $slugQuery = static::find()->where($expression)
        ->cache(Module::getInstance()->cacheDuration, QueryCache::getSlugDependencies())
        ->andWhere(['OR',
            ['host' => $hostname],
            ['IS', 'host', null]
        ])
        ->orderBy(['host' => SORT_DESC])
        ->limit(1);
    $slugQuery->multiple = false;
    return $slugQuery;
}
```

**Ce qui se passe :**

1. Première exécution : Requête SQL exécutée, résultat mis en cache
2. Exécutions suivantes : Résultat retourné depuis le cache (pas de SQL)
3. Modification d'un slug : Cache invalidé automatiquement
4. Prochaine exécution : Requête SQL réexécutée, nouveau cache

**Vous n'avez rien à faire. C'est automatique.**

### Dans vos propres requêtes (optionnel)

Vous pouvez aussi utiliser le cache dans vos controllers/models personnalisés.

**Exemple : Récupérer les 5 derniers articles**

```php
use blackcube\core\helpers\QueryCache;
use blackcube\core\models\Composite;
use blackcube\core\Module;

public function actionIndex()
{
    $latestArticles = Composite::find()
        ->active()
        ->orderBy(['dateCreate' => SORT_DESC])
        ->limit(5)
        ->cache(
            $this->cacheDuration,
            QueryCache::getCompositeDependencies()
        )
        ->all();
    
    return $this->render('index', [
        'articles' => $latestArticles
    ]);
}
```

**Résultat :**

1. Première visite : Requête SQL exécutée
2. Visites suivantes : Résultat servi depuis le cache
3. Modification d'un composite : Cache invalidé automatiquement
4. Prochaine visite : Requête réexécutée

## Configuration du cache

### Durée du cache

La durée par défaut est définie dans la configuration du module :

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'cacheDuration' => 3600, // 1 heure (en secondes)
    ],
],
```

**Valeurs courantes :**

- `3600` : 1 heure (recommandé pour production)
- `300` : 5 minutes (dev/staging)
- `86400` : 24 heures (sites peu modifiés)
- `0` : Pas de cache (debug uniquement)

> **Info**
>
> Même avec une durée longue (24h), le cache est invalidé automatiquement dès qu'une modification survient.

### Cache personnalisé pour Blackcube

Vous pouvez utiliser un cache différent pour Blackcube :

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'cache' => 'blackcubeCache', // Composant personnalisé
        'cacheDuration' => 3600,
    ],
],

'components' => [
    'cache' => [
        'class' => 'yii\caching\FileCache', // Cache par défaut
    ],
    'blackcubeCache' => [
        'class' => 'yii\redis\Cache', // Cache Blackcube
        'redis' => [
            'hostname' => 'localhost',
            'port' => 6379,
            'database' => 1, // Base Redis différente
        ],
    ],
],
```

> **Info**
>
> Séparer le cache Blackcube du cache applicatif permet d'isoler les performances : le CMS bénéficie d'un cache Redis rapide pour les contenus, tandis que votre application peut utiliser un cache fichier plus simple. Cela évite aussi que le cache applicatif n'impacte le cache CMS et vice-versa.

## Bonnes pratiques

### Choisir la dépendance la plus spécifique

**Utiliser la dépendance adaptée à votre besoin :**

```php
// Uniquement pour les composites
QueryCache::getCompositeDependencies()

// Pour les nodes
QueryCache::getNodeDependencies()

// Pour tout le CMS (plus large, moins performant)
QueryCache::getCmsDependencies()
```

**Principe :** Plus la dépendance est spécifique, moins le cache est invalidé inutilement.

## Points clés à retenir

1. **Automatique** : Pas d'invalidation manuelle à gérer
2. **Granulaire** : Cache par type de contenu (nodes, composites, menus, etc.)
3. **Intelligent** : Basé sur MAX(dateUpdate) des tables
4. **Performant** : Réduction significative du temps de génération
5. **Transparent** : Fonctionne sans code supplémentaire

> **Info**
>
> Blackcube n'impose rien : ce sont des exemples d'utilisation. Le développeur reste toujours maître de son implémentation du cache.