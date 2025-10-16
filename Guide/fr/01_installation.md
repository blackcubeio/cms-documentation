# Installation

Installation de Blackcube CMS dans un projet Yii2

L'intégration de Blackcube dans un projet Yii2 existant ou nouveau se fait entièrement via Composer. Le processus d'installation comprend l'ajout de deux packages (core et admin), la configuration des modules dans votre application, puis l'exécution des migrations pour créer toute la structure de base de données nécessaire. Les commandes Blackcube permettent ensuite d'initialiser le système RBAC et de créer votre premier compte administrateur pour accéder au backoffice. L'ensemble du processus prend quelques minutes et ne modifie pas la structure de votre projet Yii2.

Ce guide couvre l'installation complète de Blackcube, de l'ajout des packages Composer jusqu'à la vérification du fonctionnement et la configuration initiale.

## Prérequis

- **PHP 8.2 ou supérieur**
- **MySQL 5.7+** ou **MariaDB 10.2+** (PostgreSQL également supporté)
- **Composer** installé globalement
- **Un projet Yii2** existant (basic ou advanced) ou nouveau
- **Apache** ou **Nginx** configuré

## Installation via Composer

Blackcube se compose de deux packages :

- **blackcube/core** : Le cœur du CMS (obligatoire)
- **blackcube/admin** : L'interface d'administration (optionnel mais recommandé)

Dans votre projet Yii2, installez les packages :

```bash
composer require blackcube/core
composer require blackcube/admin
```

## Configuration des modules

Ajoutez les modules Blackcube dans votre configuration webapp :

```php
'modules' => [
    // Module core (obligatoire)
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'slugSensitive' => false, // false = /général et /general sont identiques
    ],
    
    // Module admin (optionnel mais recommandé)
    'bo' => [
        'class' => blackcube\admin\Module::class,
        'adminTemplatesAlias' => '@app/admin',
        'additionalAssets' => [],
        'modules' => [],
    ],
]
```

### Options du module core

```php
'blackcube' => [
    'class' => blackcube\core\Module::class,
    
    // Sensibilité aux accents des URLs
    'slugSensitive' => false, // false = /général et /general sont identiques
    
    // Composant de cache personnalisé (optionnel)
    // 'cache' => 'cache',
]
```

### Options du module admin

```php
'bo' => [
    'class' => blackcube\admin\Module::class,
    
    // Alias vers vos templates d'administration personnalisés
    'adminTemplatesAlias' => '@app/admin',
    
    // Assets CSS/JS additionnels pour le backoffice
    'additionalAssets' => [],
    
    // Sous-modules admin (pour plugins)
    'modules' => [],
    
    // Composant de cache personnalisé (optionnel)
    // 'cache' => 'cache',
]
```

## Création de la structure de base

Exécutez les migrations pour créer les tables Blackcube :

```bash
# Migration de la DB
php yii.php migrate
# Initialisation des droits RBAC
php yii.php bc:init
# Création de l'administrateur initial
php yii.php bc:admin/create
```

> **Info**
> Ces commandes créent toutes les tables nécessaires (nodes, composites, blocs, slugs, etc.) et initialisent Blackcube. Nécessaire uniquement au démarrage.

## Configuration du serveur web

### Apache

Créez ou modifiez votre fichier .htaccess dans le dossier public :

```text
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php
```

### Nginx

Configuration minimale :

```nginx
location / {
    try_files $uri $uri/ /index.php?$args;
}
```

## Vérification de l'installation

### Accès au backoffice

Accédez à l'interface d'administration :

```text
http://votre-site.local/bo
```

Connectez-vous avec les identifiants créés.

### Vérification du frontend

Créez un controller de test :

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

> **Attention**
> Blackcube s'intègre dans n'importe quelle structure Yii2 : basic, advanced ou personnalisée. La structure de votre projet reste inchangée.

## Points clés à retenir

1. **Installation rapide** : Quelques commandes Composer et migrations suffisent pour installer Blackcube dans n'importe quel projet Yii2
2. **Deux packages** : Le core est obligatoire, l'admin est recommandé pour avoir accès au backoffice
3. **Structure préservée** : Blackcube n'impose aucune modification de la structure de votre projet Yii2
4. **Configuration minimale** : Seuls les deux modules (core et admin) doivent être ajoutés à votre configuration
5. **RBAC intégré** : Le système de permissions est initialisé automatiquement avec bc:init

## Dépannage

### Erreur de migration

**Problème** : La commande migrate échoue

**Solutions** :

- Vérifiez votre configuration de base de données
- Assurez-vous que la base de données existe et que l'utilisateur a les droits nécessaires
- Vérifiez que PHP a l'extension MySQL/PDO activée

### Erreur 404 sur /bo

**Problème** : La page /bo retourne une erreur 404

**Solutions** :

- Vérifiez que le module bo est bien configuré
- Vérifiez la configuration du serveur web
- Videz le cache : **php yii.php cache/flush-all**

### Erreur "Table not found"

**Problème** : Erreur SQL indiquant qu'une table n'existe pas

**Solutions** :

- Assurez-vous d'avoir exécuté les migrations
- Vérifiez que toutes les migrations Blackcube ont été appliquées
- En cas de doute, consultez les logs

### bc:init ne trouve pas la commande

**Problème** : La commande bc:init retourne "Unknown command"

**Solutions** :

- Vérifiez que blackcube/core est bien installé via Composer
- Vérifiez que le module blackcube est configuré
- Exécutez **composer dump-autoload**

## Support

Si vous rencontrez des problèmes non résolus :

- Consultez la documentation complète
- Ouvrez une issue sur GitHub
- Consultez les issues existantes pour voir si le problème est connu