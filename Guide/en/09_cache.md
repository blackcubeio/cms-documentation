# Granular Cache

Automatic invalidation based on dependencies

Blackcube integrates an intelligent cache system based on database dependencies. Unlike traditional CMS where cache invalidation must be manually managed, Blackcube automatically detects modifications and invalidates only the concerned cache.

Cache should be an optimization, not a band-aid. Blackcube uses Yii2's DbQueryDependency with a granular dependency system: each query defines which tables to monitor, and cache automatically invalidates as soon as a modification occurs.

## The Problem with Classic Cache

### Traditional Approach

In most applications, cache works like this:

```php
// Caching
$data = $cache->get('my-data');
if ($data === false) {
    $data = expensiveQuery();
    $cache->set('my-data', $data, 3600); // 1 hour
}
```

**Mandatory Manual Invalidation**

```php
// On each modification, manually invalidate
$model->save();
$cache->delete('my-data'); // ← Easy to forget!
```

**Too Wide or Too Narrow Cache**

1. Too wide: Invalidate entire cache when only one piece of data changed
2. Too narrow: Multiply cache keys and invalidations

**Stale Data**

1. If we forget to invalidate, users see outdated data
2. Or we drastically reduce cache duration (performance loss)

### The Blackcube Solution

Blackcube uses Yii2's DbQueryDependency with a granular dependency system.

**Principle:**

1. Each query defines its dependencies (which tables to monitor)
2. Blackcube automatically monitors MAX(dateUpdate) of these tables
3. As soon as a table changes, **only cache depending on that table is invalidated**
4. **No manual invalidation necessary**

**Result:** Performant cache + always up-to-date data + zero manual management.

## Blackcube Cache Architecture

### QueryCache Helper

Blackcube provides the QueryCache helper which centralizes all dependencies.

**Available dependencies:**

- getCmsDependencies(): All CMS tables
- getNodeDependencies(): Nodes and their relationships
- getCompositeDependencies(): Composites and their relationships
- getCategoryDependencies(): Categories
- getTagDependencies(): Tags
- getMenuDependencies(): Menus
- getSlugDependencies(): URLs and redirects
- getLanguageDependencies(): Languages
- getTypeDependencies(): Content types

### Dependency Granularity

**Example: Node dependencies**

Monitors modifications on:

1. Table `nodes`: Node modification
2. Table `nodes_blocs`: Adding/removing blocs in a node
3. Table `nodes_composites`: Node ↔ composite linking
4. Table `blocs`: Bloc content modification
5. Table `blocTypes`: BlocType modification

**Result:** As soon as one of these tables changes, **all cached queries using this dependency are invalidated**.

> **Info**
>
> Composite, tag, menu, etc. cache remains valid because they have their own dependencies.

## Usage in Code

### In Blackcube Queries (automatic)

Blackcube models already use cache automatically in some critical queries.

**Example: Slug search by URL**

```php
// Blackcube internal code
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

**What happens:**

1. First execution: SQL query executed, result cached
2. Following executions: Result returned from cache (no SQL)
3. Slug modification: Cache automatically invalidated
4. Next execution: SQL query re-executed, new cache

**You have nothing to do. It's automatic.**

### In Your Own Queries (optional)

You can also use cache in your custom controllers/models.

**Example: Retrieve Last 5 Articles**

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

**Result:**

1. First visit: SQL query executed
2. Following visits: Result served from cache
3. Composite modification: Cache automatically invalidated
4. Next visit: Query re-executed

## Cache Configuration

### Cache Duration

Default duration is defined in module configuration:

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'cacheDuration' => 3600, // 1 hour (in seconds)
    ],
],
```

**Common values:**

- `3600`: 1 hour (recommended for production)
- `300`: 5 minutes (dev/staging)
- `86400`: 24 hours (rarely modified sites)
- `0`: No cache (debug only)

> **Info**
>
> Even with a long duration (24h), cache is automatically invalidated as soon as a modification occurs.

### Custom Cache for Blackcube

You can use a different cache for Blackcube:

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'cache' => 'blackcubeCache', // Custom component
        'cacheDuration' => 3600,
    ],
],

'components' => [
    'cache' => [
        'class' => 'yii\caching\FileCache', // Default cache
    ],
    'blackcubeCache' => [
        'class' => 'yii\redis\Cache', // Blackcube cache
        'redis' => [
            'hostname' => 'localhost',
            'port' => 6379,
            'database' => 1, // Different Redis database
        ],
    ],
],
```

> **Info**
>
> Separating Blackcube cache from application cache allows isolating performance: CMS benefits from fast Redis cache for content, while your application can use simpler file cache. This also prevents application cache from impacting CMS cache and vice-versa.

## Best Practices

### Choose Most Specific Dependency

**Use dependency adapted to your need:**

```php
// Only for composites
QueryCache::getCompositeDependencies()

// For nodes
QueryCache::getNodeDependencies()

// For entire CMS (wider, less performant)
QueryCache::getCmsDependencies()
```

**Principle:** The more specific the dependency, the less cache is unnecessarily invalidated.

## Key Points to Remember

1. **Automatic**: No manual invalidation to manage
2. **Granular**: Cache by content type (nodes, composites, menus, etc.)
3. **Intelligent**: Based on MAX(dateUpdate) of tables
4. **Performant**: Significant reduction in generation time
5. **Transparent**: Works without additional code

> **Info**
>
> Blackcube imposes nothing: these are usage examples. Developer always remains master of cache implementation.