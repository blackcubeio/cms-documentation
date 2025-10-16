# Yii2/Blackcube

Configuration de votre application Blackcube CMS

Blackcube CMS s'intègre dans n'importe quelle application Yii2 (PHP 8.3+), qu'elle soit basic, advanced ou personnalisée. Cette documentation présente la configuration des modules Blackcube et des services associés.

La structure présentée ici est un exemple d'organisation, pas une obligation. Blackcube n'impose aucune architecture particulière.

## Dépendances

### Versions requises

- **PHP** : 8.3 minimum
- **Yii2** : ~2.0
- **Blackcube Core** : ^3.6
- **Blackcube Admin** : ^3.6

### Extensions PHP requises

```
ext-intl         # Internationalisation
ext-json         # Manipulation JSON
ext-mbstring     # Chaînes multi-octets
ext-gd           # Manipulation d'images
```

### Dépendances principales

```json
"blackcube/core": "^3.6",
"blackcube/admin": "^3.6",
"vlucas/phpdotenv": "~5.0",
"yiisoft/yii2": "~2.0",
"yiisoft/yii2-redis": "~2.0",
"league/flysystem-aws-s3-v3": "^2.5",
"league/flysystem-async-aws-s3": "^2.5"
```

## Modules Blackcube

### Module Core (blackcube)

```php
'blackcube' => [
    'class' => blackcube\core\Module::class,
]
```

Le module core gère :
- Les contenus (Node, Composite, Tag, Category)
- Le routing automatique prioritaire sur Yii2
- Le système de cache granulaire

### Module Admin (bo)

```php
'bo' => [
    'class' => blackcube\admin\Module::class,
    'adminTemplatesAlias' => '@app/admin',
]
```

Le module admin fournit :
- L'interface backoffice à `/bo`
- La gestion complète des contenus via GUI
- Les templates d'admin personnalisables via `adminTemplatesAlias`

## Configuration du container

### Base de données

```php
'container' => [
    'singletons' => [
        Connection::class => [
            'charset' => 'utf8',
            'dsn' => getstrenv('DB_DRIVER').':host=' . getstrenv('DB_HOST') . ';port=' . getstrenv('DB_PORT') . ';dbname=' . getstrenv('DB_DATABASE'),
            'username' => getstrenv('DB_USER'),
            'password' => getstrenv('DB_PASSWORD'),
            'tablePrefix' => getstrenv('DB_TABLE_PREFIX'),
            'enableSchemaCache' => getboolenv('DB_SCHEMA_CACHE'),
            'schemaCacheDuration' => getintenv('DB_SCHEMA_CACHE_DURATION'),
        ],
    ]
]
```

Variables d'environnement :

```bash
DB_DRIVER=mysql|pgsql
DB_DATABASE=blackcubev2
DB_USER=root
DB_PASSWORD=xxx
DB_HOST=localhost
DB_PORT=3306|5432
DB_TABLE_PREFIX=              # Optionnel, non recommandé
DB_SCHEMA_CACHE=1
DB_SCHEMA_CACHE_DURATION=3600
```

> **Attention**
> Blackcube a été principalement testé sur MySQL. Des erreurs peuvent survenir avec PostgreSQL. N'hésitez pas à ouvrir une issue si nécessaire.

### Système de fichiers (Flysystem)

#### Stockage local (défaut)

```php
'container' => [
    'singletons' => [
        Flysystem::class => [
            'class' => FlysystemLocal::class,
            'path' => getstrenv('FILESYSTEM_LOCAL_PATH'),
        ],
    ]
]
```

Variables :

```bash
FILESYSTEM_TYPE=local
FILESYSTEM_LOCAL_PATH=@data/files
```

#### Stockage AWS S3

```php
if (getstrenv('FILESYSTEM_TYPE') === 's3') {
    $config['container']['singletons'][Flysystem::class] = [
        'class' => FlysystemAwsS3::class,
        'key' => getstrenv('FILESYSTEM_S3_KEY'),
        'secret' => getstrenv('FILESYSTEM_S3_SECRET'),
        'bucket' => getstrenv('FILESYSTEM_S3_BUCKET'),
        'region' => getstrenv('FILESYSTEM_S3_REGION'),
        'version' => 'latest',
        'endpoint' => getstrenv('FILESYSTEM_S3_ENDPOINT'),
        'pathStyleEndpoint' => getboolenv('FILESYSTEM_S3_PATH_STYLE'),
    ];
    $config['components']['fs'] = Flysystem::class;
}
```

Variables :

```bash
FILESYSTEM_TYPE=s3
FILESYSTEM_S3_KEY=xxx
FILESYSTEM_S3_SECRET=xxx
FILESYSTEM_S3_BUCKET=mon-bucket
FILESYSTEM_S3_REGION=eu-west-1
FILESYSTEM_S3_ENDPOINT=https://s3.amazonaws.com
FILESYSTEM_S3_PATH_STYLE=false
```

> **Info**
> Blackcube a été testé avec les modes "local" et "s3". En théorie, tout Flysystem est supporté.

### Système de cache

#### Sans Redis (défaut)

```php
'container' => [
    'singletons' => [
        CacheInterface::class => DummyCache::class,
    ]
]
```

Pas de cache activé par défaut.

#### Avec Redis

```php
if (getboolenv('REDIS_ENABLED')) {
    $config['container']['singletons'][RedisConnection::class] = [
        'class' => RedisConnection::class,
        'hostname' => getstrenv('REDIS_HOST'),
        'port' => getintenv('REDIS_PORT'),
        'database' => getintenv('REDIS_DATABASE'),
    ];
    $password = getstrenv('REDIS_PASSWORD');
    if ($password !== false && empty($password) === false) {
        $config['container']['singletons'][RedisConnection::class]['password'] = $password;
    }

    $config['container']['singletons'][CacheInterface::class] = [
        'class' => RedisCache::class,
        'redis' => RedisConnection::class
    ];
    $config['components']['redis'] = RedisConnection::class;
}
```

Variables :

```bash
REDIS_ENABLED=true
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DATABASE=0
REDIS_PASSWORD=              # Optionnel
```

Configuration :
- Cache principal : RedisCache
- Schema cache : Via Redis
- Session : RedisSession (voir ci-dessous)

## Gestion des sessions

Blackcube permet trois modes de session selon la configuration.

### Session standard PHP (défaut)

Aucune configuration particulière.

### Session en base de données

```php
$sessionDbEnabled = getboolenv('YII_SESSION_DB_ENABLED');
if ($sessionDbEnabled) {
    $config['components']['session']['class'] = DbSession::class;
    $config['components']['session']['db'] = 'db';
}
```

Variable :

```bash
YII_SESSION_DB_ENABLED=true
```

### Session Redis

```php
$sessionRedisEnabled = getboolenv('YII_SESSION_REDIS_ENABLED');
$redisEnabled = getboolenv('REDIS_ENABLED');
if ($redisEnabled && $sessionRedisEnabled) {
    $config['components']['session']['class'] = RedisSession::class;
    $config['components']['session']['redis'] = 'redis';
}
```

Variables :

```bash
REDIS_ENABLED=true
YII_SESSION_REDIS_ENABLED=true
```

> **Info**
> La session Redis nécessite que Redis soit activé. Si Redis est activé mais pas la session Redis, la session utilisera le mode par défaut (PHP ou DB).

## Sitemap

```php
'container' => [
    'definitions' => [
        'sitemap.xml' => [
            'class' => SitemapAction::class,
            // 'additionalSitemapAlias' => '@app/config/sitemap.xml',
        ],
    ],
]
```

Configuration du sitemap automatique. Le paramètre `additionalSitemapAlias` permet d'ajouter des entrées personnalisées.

## Démarrage rapide

### Installation

```bash
composer install
cp .env.example .env
nano .env
# Configurer DB, REDIS, FILESYSTEM, etc.
```

### Initialisation

```bash
php yii migrate
```

### Accès

- **Frontend** : http://localhost
- **Backoffice** : http://localhost/bo