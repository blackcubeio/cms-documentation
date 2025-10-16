# Yii2/Blackcube

Configuring your Blackcube CMS application

Blackcube CMS integrates into any Yii2 application (PHP 8.3+), whether basic, advanced or custom. This documentation presents the configuration of Blackcube modules and associated services.

The structure presented here is an example of organization, not a requirement. Blackcube does not impose any particular architecture.

## Dependencies

### Required Versions

- **PHP**: 8.3 minimum
- **Yii2**: ~2.0
- **Blackcube Core**: ^3.6
- **Blackcube Admin**: ^3.6

### Required PHP Extensions

```
ext-intl         # Internationalization
ext-json         # JSON manipulation
ext-mbstring     # Multi-byte strings
ext-gd           # Image manipulation
```

### Main Dependencies

```json
"blackcube/core": "^3.6",
"blackcube/admin": "^3.6",
"vlucas/phpdotenv": "~5.0",
"yiisoft/yii2": "~2.0",
"yiisoft/yii2-redis": "~2.0",
"league/flysystem-aws-s3-v3": "^2.5",
"league/flysystem-async-aws-s3": "^2.5"
```

## Blackcube Modules

### Core Module (blackcube)

```php
'blackcube' => [
    'class' => blackcube\core\Module::class,
]
```

The core module handles:
- Content (Node, Composite, Tag, Category)
- Automatic routing with priority over Yii2
- Granular cache system

### Admin Module (bo)

```php
'bo' => [
    'class' => blackcube\admin\Module::class,
    'adminTemplatesAlias' => '@app/admin',
]
```

The admin module provides:
- Backoffice interface at `/bo`
- Complete content management via GUI
- Customizable admin templates via `adminTemplatesAlias`

## Container Configuration

### Database

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

Environment variables:

```bash
DB_DRIVER=mysql|pgsql
DB_DATABASE=blackcubev2
DB_USER=root
DB_PASSWORD=xxx
DB_HOST=localhost
DB_PORT=3306|5432
DB_TABLE_PREFIX=              # Optional, not recommended
DB_SCHEMA_CACHE=1
DB_SCHEMA_CACHE_DURATION=3600
```

> **Warning**
> Blackcube has been primarily tested on MySQL. Errors may occur with PostgreSQL. Feel free to open an issue if needed.

### File System (Flysystem)

#### Local Storage (default)

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

Variables:

```bash
FILESYSTEM_TYPE=local
FILESYSTEM_LOCAL_PATH=@data/files
```

#### AWS S3 Storage

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

Variables:

```bash
FILESYSTEM_TYPE=s3
FILESYSTEM_S3_KEY=xxx
FILESYSTEM_S3_SECRET=xxx
FILESYSTEM_S3_BUCKET=my-bucket
FILESYSTEM_S3_REGION=eu-west-1
FILESYSTEM_S3_ENDPOINT=https://s3.amazonaws.com
FILESYSTEM_S3_PATH_STYLE=false
```

> **Info**
> Blackcube has been tested with "local" and "s3" modes. In theory, any Flysystem is supported.

### Cache System

#### Without Redis (default)

```php
'container' => [
    'singletons' => [
        CacheInterface::class => DummyCache::class,
    ]
]
```

No cache enabled by default.

#### With Redis

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

Variables:

```bash
REDIS_ENABLED=true
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DATABASE=0
REDIS_PASSWORD=              # Optional
```

Configuration:
- Main cache: RedisCache
- Schema cache: Via Redis
- Session: RedisSession (see below)

## Session Management

Blackcube allows three session modes depending on configuration.

### Standard PHP Session (default)

No specific configuration required.

### Database Session

```php
$sessionDbEnabled = getboolenv('YII_SESSION_DB_ENABLED');
if ($sessionDbEnabled) {
    $config['components']['session']['class'] = DbSession::class;
    $config['components']['session']['db'] = 'db';
}
```

Variable:

```bash
YII_SESSION_DB_ENABLED=true
```

### Redis Session

```php
$sessionRedisEnabled = getboolenv('YII_SESSION_REDIS_ENABLED');
$redisEnabled = getboolenv('REDIS_ENABLED');
if ($redisEnabled && $sessionRedisEnabled) {
    $config['components']['session']['class'] = RedisSession::class;
    $config['components']['session']['redis'] = 'redis';
}
```

Variables:

```bash
REDIS_ENABLED=true
YII_SESSION_REDIS_ENABLED=true
```

> **Info**
> Redis session requires Redis to be enabled. If Redis is enabled but not Redis session, the session will use the default mode (PHP or DB).

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

Automatic sitemap configuration. The `additionalSitemapAlias` parameter allows adding custom entries.

## Quick Start

### Installation

```bash
composer install
cp .env.example .env
nano .env
# Configure DB, REDIS, FILESYSTEM, etc.
```

### Initialization

```bash
php yii migrate
```

### Access

- **Frontend**: http://localhost
- **Backoffice**: http://localhost/bo