# Configuration

Configuration post-installation de Blackcube CMS

Après l'installation de Blackcube, quelques configurations sont nécessaires avant de créer du contenu. Ces configurations se font principalement via le backoffice et concernent les langues, les utilisateurs et les options avancées des modules.

Ce guide présente les étapes de configuration post-installation et les options avancées disponibles.

## Activer les langues

Blackcube est multilingue par défaut. Toutes les langues sont disponibles, mais vous devez activer celles dont vous avez besoin.

Dans le backoffice :

1. Allez dans Paramètres → Langues
2. Activez les langues nécessaires pour votre projet
3. Définissez la langue par défaut

L'interface de Blackcube est localisée en anglais et français uniquement. Cela n'affecte pas les langues disponibles pour votre contenu.

Vous pouvez activer autant de langues que nécessaire. Chaque contenu (Node, Composite, Tag, Category) est lié à une langue. Les slugs sont gérés par langue.

Exemple pour un site français/anglais :

```text
Langues actives :
- Français (fr)
- English (en)
```

Activation rapide via SQL :

```sql
-- Activer uniquement en et fr
update languages set active = 0 where id not in ('fr', 'en');
```

## Système RBAC

Blackcube intègre un système de gestion des rôles et permissions pour le backoffice.

Le rôle Admin donne un accès total. Les autres rôles disponibles :

- Site : Accès backoffice, dashboard, preview, search
- User, Language, Parameter, Type, BlocType, Menu : Gestion des paramètres
- Node, Composite, Category, Tag : Gestion du contenu avec permissions Create, Update, Delete, View, Export, Import
- Slug : Gestion des redirections

Dans le backoffice, allez dans Paramètres → Utilisateurs pour créer ou éditer un utilisateur et assigner des rôles et permissions.

> **Info**
> Le système RBAC contrôle uniquement l'accès au backoffice, pas le front-end du site.

## Options avancées

### Sensibilité aux accents des URLs

Par défaut, les URLs ne sont pas sensibles aux accents : /général = /general.

Pour activer la sensibilité :

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'slugSensitive' => true, // URLs sensibles aux accents
    ],
]
```

### Cache spécifique Blackcube

Si vous souhaitez utiliser un cache spécifique pour Blackcube :

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'cache' => 'cache', // Composant de cache personnalisé
    ],
]
```

### Templates d'administration personnalisés

Pour personnaliser l'interface d'administration :

```php
'modules' => [
    'bo' => [
        'class' => blackcube\admin\Module::class,
        'adminTemplatesAlias' => '@app/admin', // Templates custom
        'additionalAssets' => [
            'webapp\assets\AdminCustomAsset', // CSS/JS custom
        ],
    ],
]
```

> **Attention**
> La gestion des contenus peut être personnalisée selon vos besoins. Cette fonctionnalité prendra toute son importance pour adapter l'interface aux workflows spécifiques de votre projet.

## Vérification de la configuration

Avant de créer du contenu, vérifiez que :

1. Au moins une langue est activée
2. Un utilisateur administrateur existe
3. Les permissions utilisateurs sont définies si nécessaire

## Dépannage

### Erreur 403 dans le backoffice

Un utilisateur ne peut pas accéder à certaines sections.

- Vérifiez les permissions RBAC de l'utilisateur
- Assignez les rôles appropriés dans Paramètres → Utilisateurs

### Les langues n'apparaissent pas

Les langues sont disponibles mais ne s'affichent pas dans le backoffice.

- Vérifiez qu'au moins une langue est activée
- Videz le cache