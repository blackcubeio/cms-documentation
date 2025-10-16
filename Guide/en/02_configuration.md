# Configuration

Post-installation configuration of Blackcube CMS

After installing Blackcube, some configurations are necessary before creating content. These configurations are mainly done via the backoffice and concern languages, users and advanced module options.

This guide presents post-installation configuration steps and available advanced options.

## Activate Languages

Blackcube is multilingual by default. All languages are available, but you must activate the ones you need.

In the backoffice:

1. Go to Settings → Languages
2. Activate the necessary languages for your project
3. Define the default language

The Blackcube interface is localized in English and French only. This does not affect the languages available for your content.

You can activate as many languages as necessary. Each content (Node, Composite, Tag, Category) is linked to a language. Slugs are managed per language.

Example for a French/English site:

```text
Active languages:
- Français (fr)
- English (en)
```

Quick activation via SQL:

```sql
-- Activate only en and fr
update languages set active = 0 where id not in ('fr', 'en');
```

## RBAC System

Blackcube integrates a role and permission management system for the backoffice.

The Admin role gives full access. Other available roles:

- Site: Backoffice access, dashboard, preview, search
- User, Language, Parameter, Type, BlocType, Menu: Settings management
- Node, Composite, Category, Tag: Content management with Create, Update, Delete, View, Export, Import permissions
- Slug: Redirect management

In the backoffice, go to Settings → Users to create or edit a user and assign roles and permissions.

> **Info**
> The RBAC system only controls backoffice access, not the front-end of the site.

## Advanced Options

### URL Accent Sensitivity

By default, URLs are not accent-sensitive: /général = /general.

To enable sensitivity:

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'slugSensitive' => true, // Accent-sensitive URLs
    ],
]
```

### Blackcube-Specific Cache

If you want to use a specific cache for Blackcube:

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'cache' => 'cache', // Custom cache component
    ],
]
```

### Custom Administration Templates

To customize the administration interface:

```php
'modules' => [
    'bo' => [
        'class' => blackcube\admin\Module::class,
        'adminTemplatesAlias' => '@app/admin', // Custom templates
        'additionalAssets' => [
            'webapp\assets\AdminCustomAsset', // Custom CSS/JS
        ],
    ],
]
```

> **Warning**
> Content management can be customized according to your needs. This feature will be very important to adapt the interface to your project's specific workflows.

## Configuration Verification

Before creating content, verify that:

1. At least one language is activated
2. An administrator user exists
3. User permissions are defined if necessary

## Troubleshooting

### 403 Error in the Backoffice

A user cannot access certain sections.

- Check the user's RBAC permissions
- Assign appropriate roles in Settings → Users

### Languages Don't Appear

Languages are available but don't display in the backoffice.

- Check that at least one language is activated
- Clear the cache