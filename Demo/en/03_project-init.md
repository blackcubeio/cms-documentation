# Project Initialization
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Design Analysis

---

## Objective

Install Blackcube CMS in a Yii2 project and verify the installation works correctly. This step creates the base database structure and configures back-office access.

---

## Package Installation

Blackcube consists of two Composer packages:

**blackcube/core**: The CMS core (required)
**blackcube/admin**: The admin interface (recommended)

In your Yii2 project, install the packages:

```bash
composer require blackcube/core
composer require blackcube/admin
```

---

## Module Configuration

### Core Module

Add the Blackcube module in your webapp configuration:

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'slugSensitive' => false,
    ],
]
```

**slugSensitive**: Defines if URLs are accent-sensitive
- `false`: /général and /general are identical (recommended)
- `true`: /général and /general are different

### Admin Module

Add the admin module in your webapp configuration:

```php
'modules' => [
    'bo' => [
        'class' => blackcube\admin\Module::class,
        'adminTemplatesAlias' => '@app/admin',
        'additionalAssets' => [],
        'modules' => [],
    ],
]
```

**adminTemplatesAlias**: Path to custom admin templates
**additionalAssets**: Additional CSS/JS assets for backoffice
**modules**: Admin sub-modules (for plugins)

---

## Structure Creation

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
> These commands create all necessary tables (nodes, composites, blocs, slugs, etc.) and initialize Blackcube. Only needed at startup.

---

## Web Server Configuration

### Apache

.htaccess file in public folder:

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

---

## Installation Verification

### Backoffice Access

Access the admin interface:

```text
http://your-site.local/bo
```

Log in with credentials created during `bc:admin/create`.

### Creation Test

In the backoffice, verify you can access:
- Management → Sections
- Management → Articles
- Settings → Block Types
- Settings → Types

If all these sections are accessible, the installation is correct.

---

## Initial Configuration

### Enable Languages

In the backoffice:

1. Go to Settings → Languages
2. Enable French (fr)
3. Enable English (en)
4. Set French as default language

Quick activation via SQL:

```sql
UPDATE languages SET active = 1 WHERE id IN ('fr', 'en');
UPDATE languages SET active = 0 WHERE id NOT IN ('fr', 'en');
```

### Create Admin Folder

Create the folder for custom admin templates:

```bash
mkdir -p common/admin
```

This folder will contain admin templates for BlocTypes (Hero, CTA, Card).

---

## Project Structure

After installation, your project should have this minimal structure:

```
my-project/
├── common/
│   ├── config/
│   └── admin/              # Admin templates (to create)
├── webapp/
│   ├── config/
│   ├── controllers/
│   ├── models/
│   ├── views/
│   └── web/
│       └── index.php
└── composer.json
```

---

## Troubleshooting

### Migration Error

**Problem**: The migrate command fails

**Solutions**:
- Check database configuration
- Ensure database exists and user has necessary rights
- Verify PHP has MySQL/PDO extension enabled

### 404 Error on /bo

**Problem**: The /bo page returns a 404 error

**Solutions**:
- Verify the bo module is properly configured
- Check web server configuration
- Clear cache: `php yii.php cache/flush-all`

### bc:init Command Not Found

**Problem**: The bc:init command returns "Unknown command"

**Solutions**:
- Verify blackcube/core is properly installed via Composer
- Verify the blackcube module is configured
- Run `composer dump-autoload`

---

## Validation

Before continuing:
- Blackcube installed via Composer
- Modules configured in webapp config
- Migrations executed
- Back-office access working
- FR/EN languages enabled
- common/admin folder created