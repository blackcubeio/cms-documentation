# Initialisation du Projet
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Récupération et Analyse des Créas

---

## Objectif

Installer Blackcube CMS dans un projet Yii2 et vérifier que l'installation fonctionne correctement. Cette étape crée la structure de base de données et configure l'accès au back-office.

---

## Installation des Packages

Blackcube se compose de deux packages Composer :

**blackcube/core** : Le cœur du CMS (obligatoire)
**blackcube/admin** : L'interface d'administration (recommandé)

Dans votre projet Yii2, installez les packages :

```bash
composer require blackcube/core
composer require blackcube/admin
```

---

## Configuration des Modules

### Module Core

Ajoutez le module Blackcube dans votre configuration webapp :

```php
'modules' => [
    'blackcube' => [
        'class' => blackcube\core\Module::class,
        'slugSensitive' => false,
    ],
]
```

**slugSensitive** : Définit si les URLs sont sensibles aux accents
- `false` : /général et /general sont identiques (recommandé)
- `true` : /général et /general sont différents

### Module Admin

Ajoutez le module admin dans votre configuration webapp :

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

**adminTemplatesAlias** : Chemin vers les templates d'admin personnalisés
**additionalAssets** : Assets CSS/JS additionnels pour le backoffice
**modules** : Sous-modules admin (pour plugins)

---

## Création de la Structure

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

---

## Configuration du Serveur Web

### Apache

Fichier .htaccess dans le dossier public :

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

---

## Vérification de l'Installation

### Accès au Backoffice

Accédez à l'interface d'administration :

```text
http://votre-site.local/bo
```

Connectez-vous avec les identifiants créés lors de `bc:admin/create`.

### Test de Création

Dans le backoffice, vérifiez que vous pouvez accéder à :
- Gestion → Rubriques
- Gestion → Articles
- Paramètres → Types de bloc
- Paramètres → Types

Si toutes ces sections sont accessibles, l'installation est correcte.

---

## Configuration Initiale

### Activer les Langues

Dans le backoffice :

1. Allez dans Paramètres → Langues
2. Activez Français (fr)
3. Activez English (en)
4. Définissez le français comme langue par défaut

Activation rapide via SQL :

```sql
UPDATE languages SET active = 1 WHERE id IN ('fr', 'en');
UPDATE languages SET active = 0 WHERE id NOT IN ('fr', 'en');
```

### Créer le Dossier Admin

Créez le dossier pour les templates d'administration personnalisés :

```bash
mkdir -p common/admin
```

Ce dossier contiendra les templates d'admin pour les BlocTypes (Hero, CTA, Card).

---

## Structure du Projet

Après installation, votre projet devrait avoir cette structure minimale :

```
mon-projet/
├── common/
│   ├── config/
│   └── admin/              # Templates d'admin (à créer)
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

## Dépannage

### Erreur de Migration

**Problème** : La commande migrate échoue

**Solutions** :
- Vérifiez la configuration de base de données
- Assurez-vous que la base existe et que l'utilisateur a les droits nécessaires
- Vérifiez que PHP a l'extension MySQL/PDO activée

### Erreur 404 sur /bo

**Problème** : La page /bo retourne une erreur 404

**Solutions** :
- Vérifiez que le module bo est bien configuré
- Vérifiez la configuration du serveur web
- Videz le cache : `php yii.php cache/flush-all`

### bc:init ne Trouve pas la Commande

**Problème** : La commande bc:init retourne "Unknown command"

**Solutions** :
- Vérifiez que blackcube/core est bien installé via Composer
- Vérifiez que le module blackcube est configuré
- Exécutez `composer dump-autoload`

---

## Validation

Avant de continuer :
- Blackcube installé via Composer
- Modules configurés dans la config webapp
- Migrations exécutées
- Accès au back-office fonctionnel
- Langues FR/EN activées
- Dossier common/admin créé