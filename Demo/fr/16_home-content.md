# Pages d'Accueil (Remplissage Contenu)
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Pages de Documentation (Remplissage Contenu)

---

## Objectif

Remplir les blocs Hero, Features et CTA des pages d'accueil françaises et anglaises dans le back-office. Les Nodes Home FR/EN ont déjà été créés dans l'étape 13, il suffit maintenant de compléter leurs blocs de contenu.

---

## Vue d'ensemble

Les pages Home utilisent le Type **Home** qui autorise 3 BlocTypes :
- **Hero (H1)** : En-tête principal avec CTA
- **Feature (H3)** : Mises en avant (groupées par 3)
- **Cta** : Appel à l'action final

**Structure finale** :
```
/fr (Node FR - Type: Home)
└── Blocs :
    ├── Hero (H1)
    ├── Feature × 3 (Contrôle Total, Flexible, Performant)
    └── Cta

/en (Node EN - Type: Home)
└── Blocs :
    ├── Hero (H1)
    ├── Feature × 3 (Total Control, Flexible, Performant)
    └── Cta
```

**Total** : 5 blocs par page

> **Info**
> Les Nodes Home FR/EN ont déjà été créés à l'étape 13 avec leur configuration URL, Sitemap et SEO. Cette étape concerne uniquement le remplissage des blocs de contenu.

---

## Remplir le Node Home FR

### Accès au Node

**Menu latéral** → **Gestion → Rubriques** → Cliquer sur le crayon pour éditer le Node **FR**

---

### Étape 1 : Compléter le Hero

Le Hero a déjà été créé à l'étape 13. Il faut maintenant le compléter avec tous les champs.

**Section Contenus** → Cliquer sur le Hero existant pour l'éditer

**Champs à remplir** :

**title** (obligatoire) :
```
Documentation Blackcube CMS
```

**description** (obligatoire) :
```
<p>Le CMS professionnel qui laisse le contrôle total aux développeurs. Code ton rendu et gère ton contenu avec flexibilité.</p>
```

**image** (optionnel) :
- Uploader une image de fond PNG/JPG si disponible
- Laisser vide si pas d'image

**ctaTitle** (obligatoire) :
```
Démarrer >
```

**ctaTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Articles** → **Pourquoi choisir Blackcube** (FR)

**ctaSecondaryTitle** (obligatoire) :
```
Documentation
```

**ctaSecondaryTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Rubriques** → **Guide** (FR)

**breadcrumbTitle** (optionnel) :
- Laisser vide

**lang** (liaison multilingue) :
- Laisser vide pour le moment
- À remplir après création du Hero EN

Enregistrer le bloc.

---

### Étape 2 : Ajouter les 3 Features

#### Feature 1 : Contrôle Total

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **02. Feature (H3)**

**Champs à remplir** :

**title** (obligatoire) :
```
Contrôle Total
```

**description** (obligatoire) :
```
<p>Le développeur code 100% du rendu. Aucune contrainte de template, liberté totale sur le front-end.</p>
```

**icon** (obligatoire) :
- Uploader une icône SVG (code source)
- SVG recommandé pour "Contrôle Total" :

```svg
<svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4"></path>
</svg>
```

**Champs non utilisés** :
- link : Laisser vide
- linkRoute : Laisser vide
- linkUrl : Laisser vide

Enregistrer le bloc.

---

#### Feature 2 : Flexible

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **02. Feature (H3)**

**Champs à remplir** :

**title** (obligatoire) :
```
Flexible
```

**description** (obligatoire) :
```
<p>JSON Schema pour définir vos contenus. Pas de migration SQL, évolution instantanée de votre structure.</p>
```

**icon** (obligatoire) :
- Uploader une icône SVG (layout/grille)
- SVG recommandé pour "Flexible" :

```svg
<svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 13a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H5a1 1 0 01-1-1v-6zM16 13a1 1 0 011-1h2a1 1 0 011 1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-6z"></path>
</svg>
```

**Champs non utilisés** :
- link : Laisser vide
- linkRoute : Laisser vide
- linkUrl : Laisser vide

Enregistrer le bloc.

---

#### Feature 3 : Performant

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **02. Feature (H3)**

**Champs à remplir** :

**title** (obligatoire) :
```
Performant
```

**description** (obligatoire) :
```
<p>Hazel Tree : une seule requête pour charger toute l'arborescence.</p>
```

**icon** (obligatoire) :
- Uploader une icône SVG (éclair/performance)
- SVG recommandé pour "Performant" :

```svg
<svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path>
</svg>
```

**Champs non utilisés** :
- link : Laisser vide
- linkRoute : Laisser vide
- linkUrl : Laisser vide

Enregistrer le bloc.

---

### Étape 3 : Ajouter le CTA final

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **08. Cta**

**Champs à remplir** :

**title** (obligatoire) :
```
Prêt à commencer ?
```

**description** (obligatoire) :
```
<p>Découvrez comment Blackcube peut transformer votre workflow de développement.</p>
```

**ctaTitle** (obligatoire) :
```
Commencer maintenant >
```

**ctaTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Articles** → **Pourquoi choisir Blackcube** (FR)

Enregistrer le bloc.

---

## Remplir le Node Home EN

### Accès au Node

**Menu latéral** → **Gestion → Rubriques** → Cliquer sur le crayon pour éditer le Node **EN**

---

### Étape 1 : Compléter le Hero

Le Hero a déjà été créé à l'étape 13. Il faut maintenant le compléter.

**Section Contenus** → Cliquer sur le Hero existant pour l'éditer

**Champs à remplir** :

**title** (obligatoire) :
```
Blackcube CMS Documentation
```

**description** (obligatoire) :
```
<p>The professional CMS that gives total control to developers. Code your rendering and manage your content with flexibility.</p>
```

**image** (optionnel) :
- Uploader la même image que pour FR si disponible
- Laisser vide si pas d'image

**ctaTitle** (obligatoire) :
```
Get Started >
```

**ctaTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Articles** → **Why choose Blackcube** (EN)

**ctaSecondaryTitle** (obligatoire) :
```
Documentation
```

**ctaSecondaryTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Rubriques** → **Guide** (EN)

**breadcrumbTitle** (optionnel) :
- Laisser vide

**lang** (liaison multilingue) :
- Laisser vide pour le moment
- À remplir à l'étape suivante

Enregistrer le bloc.

---

### Étape 2 : Ajouter les 3 Features

#### Feature 1 : Total Control

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **02. Feature (H3)**

**Champs à remplir** :

**title** (obligatoire) :
```
Total Control
```

**description** (obligatoire) :
```
<p>Developers code 100% of the rendering. No template constraints, total freedom on the front-end.</p>
```

**icon** (obligatoire) :
- Uploader la même icône SVG que Feature 1 FR (code source)

**Champs non utilisés** :
- link : Laisser vide
- linkRoute : Laisser vide
- linkUrl : Laisser vide

Enregistrer le bloc.

---

#### Feature 2 : Flexible

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **02. Feature (H3)**

**Champs à remplir** :

**title** (obligatoire) :
```
Flexible
```

**description** (obligatoire) :
```
<p>JSON Schema to define your content. No SQL migration, instant evolution of your structure.</p>
```

**icon** (obligatoire) :
- Uploader la même icône SVG que Feature 2 FR (layout/grille)

**Champs non utilisés** :
- link : Laisser vide
- linkRoute : Laisser vide
- linkUrl : Laisser vide

Enregistrer le bloc.

---

#### Feature 3 : Performant

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **02. Feature (H3)**

**Champs à remplir** :

**title** (obligatoire) :
```
Performant
```

**description** (obligatoire) :
```
<p>Hazel Tree: a single query to load the entire tree structure.</p>
```

**icon** (obligatoire) :
- Uploader la même icône SVG que Feature 3 FR (éclair/performance)

**Champs non utilisés** :
- link : Laisser vide
- linkRoute : Laisser vide
- linkUrl : Laisser vide

Enregistrer le bloc.

---

### Étape 3 : Ajouter le CTA final

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **08. Cta**

**Champs à remplir** :

**title** (obligatoire) :
```
Ready to get started?
```

**description** (obligatoire) :
```
<p>Discover how Blackcube can transform your development workflow.</p>
```

**ctaTitle** (obligatoire) :
```
Start now >
```

**ctaTarget** (obligatoire) :
- Sélectionner dans le dropdown → **Articles** → **Why choose Blackcube** (EN)

Enregistrer le bloc.

---

## Liaison Multilingue FR ↔ EN

### Mettre à jour le champ lang du Hero FR

1. Éditer le Node **FR** → Cliquer sur le bloc **Hero**
2. Champ **lang** : Sélectionner dans le dropdown → **Rubriques** → **EN**
3. Enregistrer

### Mettre à jour le champ lang du Hero EN

1. Éditer le Node **EN** → Cliquer sur le bloc **Hero**
2. Champ **lang** : Sélectionner dans le dropdown → **Rubriques** → **FR**
3. Enregistrer

> **Info**
> Cette liaison permet au language switcher de basculer entre `/fr` et `/en`. Le behavior HrefLang génère automatiquement les balises `<link rel="alternate" hreflang="..." />` pour le SEO.

---

## Validation

Vérifier dans **Gestion → Rubriques** :

**Nodes modifiés** :
- FR (Type: Home) - Langue: Français
- EN (Type: Home) - Langue: English

**Blocs par Node** :
- Hero (H1) : Titre + description + 2 CTA
- Feature (H3) × 3 : Icône + titre + description
- Cta : Titre + description + CTA
- **Total** : 5 blocs par page

**Configuration** :
- Liaison FR ↔ EN fonctionnelle via champ `lang` du Hero
- Tous les champs obligatoires remplis
- Tous les blocs actifs (validation JSON Schema passée)

> **Info**
> Les URLs, Sitemap et SEO ont déjà été configurés à l'étape 13. Cette étape concernait uniquement le remplissage des blocs de contenu.

---

## Recommandations

### Contenu des Features

Les 3 Features présentent les forces de Blackcube :
- **Contrôle Total** : Liberté totale du développeur sur le rendu
- **Flexible** : JSON Schema, pas de migration SQL
- **Performant** : Hazel Tree, architecture optimisée

Ces messages ciblent les développeurs et mettent en avant les avantages techniques du CMS.

### CTA et Navigation

Les CTA orientent vers :
- **CTA primaire Hero** : Article "Pourquoi choisir Blackcube" (découverte)
- **CTA secondaire Hero** : Guide (documentation technique)
- **CTA final** : Article "Pourquoi choisir Blackcube" (conversion)

Cette navigation guide progressivement le développeur de la découverte vers l'action.

---

**Fin de l'étape 16 - Guide de développement complet**