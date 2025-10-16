# Pages Documentation (Remplissage Contenu)
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Pages de Contact (Remplissage Contenu)

---

## Objectif

Créer et remplir des articles de documentation exemples dans les Nodes Guide et À propos, avec différents types de blocs (Hero, Titre, Content, Code, Info, Cta). Configurer les URLs, le sitemap et le SEO.

---

## Vue d'ensemble

Les pages Documentation utilisent le Type **Documentation** qui autorise 8 BlocTypes :
- **Hero (H1)** : En-tête de page
- **Titre (H2-H4)** : Structuration du contenu
- **Content** : Paragraphes riches
- **Code** : Exemples de code
- **Info** : Alertes colorées
- **Feature (H3)** : Mises en avant optionnelles
- **Cta** : Appels à l'action
- **Card** : Encarts optionnels

**Structure recommandée** :
```
/fr/guide/pourquoi-choisir-blackcube (Composite FR)
└── Blocs :
    ├── Hero (H1)
    ├── Titre (H2)
    ├── Content
    ├── Titre (H3)
    ├── Content
    ├── Info
    └── Cta

/fr/a-propos/pourquoi-choisir-blackcube (Composite FR)
└── Même structure
```

---

## Créer l'Article "Pourquoi choisir Blackcube" (FR)

### Étape 1 : Création du Composite

**Accès** : Menu latéral → **Gestion → Articles** → Bouton **Ajouter un article**

**Onglet Article** :
1. **Actif** : Coché
2. **Type** : Sélectionner `Documentation`
3. **Langue** : Sélectionner `Français`
4. **Nom** : `Pourquoi choisir Blackcube`
5. **Rubrique** : Sélectionner **À propos** (FR) dans le dropdown
6. Enregistrer

Le Composite est créé avec un ID unique.

---

### Étape 2 : Remplir le Hero

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **01. Hero (H1)**

**Champs à remplir** :

**title** (obligatoire) :
```
Pourquoi choisir Blackcube ?
```

**description** (obligatoire) :
```
Découvrez les avantages de Blackcube CMS pour vos projets Yii2.
```

**breadcrumbTitle** (optionnel) :
```
Pourquoi Blackcube
```

**lang** (liaison multilingue) :
- Sélectionner dans le dropdown → **Articles** → **Why choose Blackcube** (EN)
- À remplir après création de l'article EN

**Champs non utilisés** :
- image : Laisser vide
- ctaTitle : Laisser vide
- ctaTarget : Laisser vide
- ctaSecondaryTitle : Laisser vide
- ctaSecondaryTarget : Laisser vide

Enregistrer le bloc.

---

### Étape 3 : Structurer le Contenu

#### Titre H2 : Flexibilité

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **03. Titre (H2 - H4)**

**Champs à remplir** :

**hn** (obligatoire) :
- Sélectionner `h2` dans le dropdown

**title** (obligatoire) :
```
Flexibilité totale
```

Enregistrer le bloc.

---

#### Content : Description Flexibilité

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Content**

**Champs à remplir** :

**content** (obligatoire) :
```
<p>Blackcube ne vous impose aucune structure. Vous gardez le contrôle total sur votre architecture, vos widgets et votre code. Adaptez le CMS à vos besoins, pas l'inverse.</p>
```

Enregistrer le bloc.

---

#### Titre H2 : Performance

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **03. Titre (H2 - H4)**

**Champs à remplir** :

**hn** (obligatoire) :
- Sélectionner `h2` dans le dropdown

**title** (obligatoire) :
```
Performance optimisée
```

Enregistrer le bloc.

---

#### Content : Description Performance

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Content**

**Champs à remplir** :

**content** (obligatoire) :
```
<p>L'architecture Hazel Tree permet de récupérer toute l'arborescence en une seule requête SQL. ElasticModel évite les migrations de base de données à chaque modification de structure.</p>
```

Enregistrer le bloc.

---

#### Info Box

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **05. Info**

**Champs à remplir** :

**type** (obligatoire) :
- Sélectionner `info` dans le dropdown

**title** (obligatoire) :
```
Yii2 requis
```

**content** (obligatoire) :
```
<p>Blackcube est un module pour Yii2. Vous devez avoir une application Yii2 existante ou en créer une nouvelle.</p>
```

Enregistrer le bloc.

---

#### CTA Final

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Cta**

**Champs à remplir** :

**title** (obligatoire) :
```
Prêt à commencer ?
```

**description** (obligatoire) :
```
<p>Consultez notre guide d'installation pour démarrer avec Blackcube.</p>
```

**ctaTitle** (obligatoire) :
```
Voir l'installation
```

**ctaTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Articles** → **Installation** (FR)
- Si l'article n'existe pas encore, laisser vide et revenir plus tard

Enregistrer le bloc.

---

### Étape 4 : Configuration URL

> **Info**
> Dans Blackcube, le chemin d'URL est toujours **complet et absolu** pour tous les éléments (Nodes, Composites, Categories, Tags). On n'hérite jamais automatiquement du chemin parent.

**Onglet URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `fr/a-propos/pourquoi-choisir-blackcube`
4. Enregistrer

URL publique finale : `/fr/a-propos/pourquoi-choisir-blackcube`

---

### Étape 5 : Configuration Sitemap

**Onglet Sitemap** :

1. **Actif** : Coché
2. **Fréquence** : `monthly`
3. **Priorité** : `0.7`
4. Enregistrer

---

### Étape 6 : Configuration SEO

**Onglet SEO** :

1. **Actif** : Coché
2. **Titre** : `Pourquoi choisir Blackcube CMS ?`
3. **Description** : `Découvrez les avantages de Blackcube : flexibilité totale, performance optimisée et architecture modulaire pour vos projets Yii2.`
4. Enregistrer

---

## Créer l'Article "Why choose Blackcube" (EN)

### Étape 1 : Création du Composite

**Accès** : Menu latéral → **Gestion → Articles** → Bouton **Ajouter un article**

**Onglet Article** :
1. **Actif** : Coché
2. **Type** : Sélectionner `Documentation`
3. **Langue** : Sélectionner `English`
4. **Nom** : `Why choose Blackcube`
5. **Rubrique** : Sélectionner **About** (EN) dans le dropdown
6. Enregistrer

---

### Étape 2 : Remplir le Hero

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **01. Hero (H1)**

**Champs à remplir** :

**title** (obligatoire) :
```
Why choose Blackcube?
```

**description** (obligatoire) :
```
Discover the advantages of Blackcube CMS for your Yii2 projects.
```

**breadcrumbTitle** (optionnel) :
```
Why Blackcube
```

**lang** (liaison multilingue) :
- Sélectionner dans le dropdown → **Articles** → **Pourquoi choisir Blackcube** (FR)

Enregistrer le bloc.

---

### Étape 3 : Structurer le Contenu

#### Titre H2 : Flexibility

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **03. Titre (H2 - H4)**

**hn** : `h2`  
**title** :
```
Total flexibility
```

Enregistrer.

---

#### Content : Flexibility Description

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Content**

**content** :
```
<p>Blackcube doesn't impose any structure. You keep total control over your architecture, widgets and code. Adapt the CMS to your needs, not the other way around.</p>
```

Enregistrer.

---

#### Titre H2 : Performance

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **03. Titre (H2 - H4)**

**hn** : `h2`  
**title** :
```
Optimized performance
```

Enregistrer.

---

#### Content : Performance Description

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Content**

**content** :
```
<p>The Hazel Tree architecture retrieves the entire tree structure in a single SQL query. ElasticModel avoids database migrations with every structure change.</p>
```

Enregistrer.

---

#### Info Box

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **05. Info**

**type** : `info`  
**title** :
```
Yii2 required
```

**content** :
```
<p>Blackcube is a module for Yii2. You must have an existing Yii2 application or create a new one.</p>
```

Enregistrer.

---

#### CTA Final

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Cta**

**title** :
```
Ready to start?
```

**description** :
```
<p>Check out our installation guide to get started with Blackcube.</p>
```

**ctaTitle** :
```
View installation
```

**ctaTarget** :
- Sélectionner dans le dropdown → **Articles** → **Installation** (EN)

Enregistrer.

---

### Étape 4 : Configuration URL

**Onglet URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `en/about/why-choose-blackcube`
4. Enregistrer

URL publique finale : `/en/about/why-choose-blackcube`

---

### Étape 5 : Configuration Sitemap

**Onglet Sitemap** :

1. **Actif** : Coché
2. **Fréquence** : `monthly`
3. **Priorité** : `0.7`
4. Enregistrer

---

### Étape 6 : Configuration SEO

**Onglet SEO** :

1. **Actif** : Coché
2. **Titre** : `Why choose Blackcube CMS?`
3. **Description** : `Discover the advantages of Blackcube: total flexibility, optimized performance and modular architecture for your Yii2 projects.`
4. Enregistrer

---

## Créer des Articles Techniques

Pour démontrer l'utilisation du bloc **Code**, créons un article technique.

### Article "Installation" (FR)

#### Création

**Accès** : Menu latéral → **Gestion → Articles** → Bouton **Ajouter un article**

1. **Actif** : Coché
2. **Type** : `Documentation`
3. **Langue** : `Français`
4. **Nom** : `Installation`
5. **Rubrique** : **Guide** (FR)
6. Enregistrer

---

#### Hero

**title** :
```
Installation
```

**description** :
```
Guide d'installation de Blackcube CMS dans votre projet Yii2.
```

**breadcrumbTitle** :
```
Installation
```

**lang** : Lier avec **Installation** (EN) après création

Enregistrer.

---

#### Titre H2 : Prérequis

**hn** : `h2`  
**title** :
```
Prérequis
```

Enregistrer.

---

#### Content : Liste des prérequis

**content** :
```
<p>Avant d'installer Blackcube, assurez-vous d'avoir :</p>
<ul>
<li>PHP 8.3 ou supérieur</li>
<li>Une application Yii2 fonctionnelle</li>
<li>Composer installé</li>
<li>MySQL 8.0 ou MariaDB 10.11</li>
</ul>
```

Enregistrer.

---

#### Titre H2 : Installation via Composer

**hn** : `h2`  
**title** :
```
Installation via Composer
```

Enregistrer.

---

#### Content : Instructions

**content** :
```
<p>Installez Blackcube avec Composer :</p>
```

Enregistrer.

---

#### Code : Commande Composer

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **06. Code**

**Champs à remplir** :

**language** (obligatoire) :
- Sélectionner `bash` dans le dropdown

**code** (obligatoire) :
```
composer require blackcube/core
composer require blackcube/admin
```

Enregistrer le bloc.

---

#### Info Box : Note importante

**type** : `warning`  
**title** :
```
Configuration requise
```

**content** :
```
<p>Après l'installation, vous devez configurer les modules dans votre fichier de configuration.</p>
```

Enregistrer.

---

#### Titre H2 : Configuration

**hn** : `h2`  
**title** :
```
Configuration
```

Enregistrer.

---

#### Content : Introduction configuration

**content** :
```
<p>Ajoutez les modules Blackcube dans votre configuration :</p>
```

Enregistrer.

---

#### Code : Configuration PHP

**language** : `php`  
**code** :
```
'modules' => [
    'admin' => [
        'class' => 'blackcube\admin\Module',
    ],
    'core' => [
        'class' => 'blackcube\core\Module',
    ],
],
```

Enregistrer.

---

#### CTA : Suite

**title** :
```
Étape suivante
```

**description** :
```
<p>Maintenant que Blackcube est installé, configurez votre base de données.</p>
```

**ctaTitle** :
```
Configuration de la base de données
```

**ctaTarget** :
- Sélectionner un article suivant dans le dropdown

Enregistrer.

---

#### Configuration URL/Sitemap/SEO

**URL** : `fr/guide/installation`  
**Sitemap** : Actif, weekly, 0.8  
**SEO** : `Installation Blackcube CMS | Guide complet`

---

## Lier les Contenus FR/EN

Une fois tous les articles créés, revenir sur chaque Hero FR pour lier avec l'article EN correspondant via le champ `lang`.

---

## Articles Recommandés

Pour un site de documentation complet, créer au minimum :

**Section Guide (FR/EN)** :
- Installation
- Configuration
- Premiers pas
- Créer des BlocTypes
- Créer des Types

**Section À propos (FR/EN)** :
- Pourquoi choisir Blackcube
- Licence BSD 3 clauses
- Contribuer

---

## Validation

Vérifier dans **Gestion → Articles** :

**Articles créés** :
- Au moins 2 articles par langue
- Type: Documentation
- URLs configurées avec chemins complets
- Sitemap activé
- SEO configuré
- Liaisons FR ↔ EN fonctionnelles

**Structure des blocs** :
- Hero présent sur tous les articles
- Alternance Titre/Content pour structure claire
- Blocs Code pour exemples techniques
- Info Boxes pour notes importantes
- CTA pour navigation
