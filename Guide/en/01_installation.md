# Installation

Installing Blackcube CMS in a Yii2 project

Integrating Blackcube into an existing or new Yii2 project is done entirely via Composer. The installation process includes adding two packages (core and admin), configuring modules in your application, then running migrations to create all the necessary database structure. Blackcube commands then allow you to initialize the RBAC system and create your first administrator account to access the backoffice. The entire process takes a few minutes and does not modify your Yii2 project structure.

This guide covers the complete installation of Blackcube, from adding Composer packages to verifying functionality and initial configuration.

## Prerequisites

- **PHP 8.2 or higher**
- **MySQL 5.7+** or **MariaDB 10.2+** (PostgreSQL also supported)
- **Composer** installed globally
- **A Yii2 project** existing (basic or advanced) or new
- **Apache** or **Nginx** configured

## Installation via Composer

Blackcube consists of two packages:

- **blackcube/core**: The CMS core (required)
- **blackcube/admin**: The administration interface (optional but recommended)

In your Yii2 project, install the packages:

```bash
composer require blackcube/core
composer require blackcube/admin
```

## Module Configuration

Add the Blackcube modules in your webapp configuration:

```php
'modules' => [
    // Core module (required)
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'slugSensitive' => false, // false = /général and /general are identical
    ],
    
    // Admin module (optional but recommended)
    'bo' => [
        'class' => blackcube\admin\Module::class,
        'adminTemplatesAlias' => '@app/admin',
        'additionalAssets' => [],
        'modules' => [],
    ],
]
```

### Core Module Options

```php
'blackcube' => [
    'class' => blackcube\core\Module::class,
    
    // URL accent sensitivity
    'slugSensitive' => false, // false = /général and /general are identical
    
    // Custom cache component (optional)
    // 'cache' => 'cache',
]
```

### Admin Module Options

```php
'bo' => [
    'class' => blackcube\admin\Module::class,
    
    // Alias to your custom administration templates
    'adminTemplatesAlias' => '@app/admin',
    
    // Additional CSS/JS assets for the backoffice
    'additionalAssets' => [],
    
    // Admin sub-modules (for plugins)
    'modules' => [],
    
    // Custom cache component (optional)
    // 'cache' => 'cache',
]
```

## Creating the Base Structure

Run migrations to create Blackcube tables:

```bash
# Database migration
php yii.php migrate
# RBAC rights initialization
php yii.php bc:init
# Initial administrator creation
php yii.php bc:admin/create
```

> **Info**
> These commands create all necessary tables (nodes, composites, blocs, slugs, etc.) and initialize Blackcube. Only necessary at startup.

## Web Server Configuration

### Apache

Create or modify your .htaccess file in the public folder:

```text
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php
```

### Nginx

Minimal configuration:

```nginx
location / {
    try_files $uri $uri/ /index.php?$args;
}
```

## Installation Verification

### Backoffice Access

Access the administration interface:

```text
http://your-site.local/bo
```

Log in with the created credentials.

### Frontend Verification

Create a test controller:

```php
<?php
namespace app\controllers;

use blackcube\core\components\BlackcubeController;

class SiteController extends BlackcubeController
{
    public function actionIndex()
    {
        return $this->render('index');
    }
}
```

> **Warning**
> Blackcube integrates into any Yii2 structure: basic, advanced or custom. Your project structure remains unchanged.

## Key Points to Remember

1. **Quick installation**: A few Composer commands and migrations are enough to install Blackcube in any Yii2 project
2. **Two packages**: Core is required, admin is recommended to access the backoffice
3. **Preserved structure**: Blackcube does not impose any modification to your Yii2 project structure
4. **Minimal configuration**: Only the two modules (core and admin) need to be added to your configuration
5. **Integrated RBAC**: The permissions system is initialized automatically with bc:init

## Troubleshooting

### Migration Error

**Problem**: The migrate command fails

**Solutions**:

- Check your database configuration
- Make sure the database exists and the user has necessary rights
- Check that PHP has the MySQL/PDO extension enabled

### 404 Error on /bo

**Problem**: The /bo page returns a 404 error

**Solutions**:

- Check that the bo module is properly configured
- Check the web server configuration
- Clear the cache: **php yii.php cache/flush-all**

### "Table not found" Error

**Problem**: SQL error indicating a table does not exist

**Solutions**:

- Make sure you ran the migrations
- Check that all Blackcube migrations have been applied
- When in doubt, check the logs

### bc:init command not found

**Problem**: The bc:init command returns "Unknown command"

**Solutions**:

- Check that blackcube/core is properly installed via Composer
- Check that the blackcube module is configured
- Run **composer dump-autoload**

## Support

If you encounter unresolved issues:

- Consult the complete documentation
- Open an issue on GitHub
- Check existing issues to see if the problem is known