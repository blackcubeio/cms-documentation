# Découpage Logique
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Initialisation du Projet

---

## Objectif

Identifier et lister tous les composants nécessaires au projet avant de commencer le développement. Cette étape d'analyse permet de planifier l'architecture et d'anticiper les développements.

> **Info**
> Cette étape ne crée aucun code. On identifie et liste uniquement les composants à développer.

---

## Identifier les Types de Pages

D'après l'analyse des créas, le site comprend 3 types de pages différentes.

### Page d'Accueil

**Rôle** : Présenter Blackcube CMS et ses fonctionnalités principales

**Caractéristiques** :
- Hero pleine largeur avec image
- Features en grille 3 colonnes
- CTA de conversion

**Type Blackcube** : Home

### Pages de Documentation

**Rôle** : Contenu technique avec navigation complète

**Caractéristiques** :
- Layout 3 colonnes (Sidebar + Contenu + TOC)
- Breadcrumb et navigation prev/next
- Blocs de contenu variés

**Type Blackcube** : Documentation

### Page de Contact

**Rôle** : Formulaire de contact avec informations

**Caractéristiques** :
- Formulaire de saisie
- Cards informatives en sidebar
- Confirmation après envoi

**Type Blackcube** : Contact

---

## Identifier les Widgets de Layout

Les widgets de layout structurent les pages et gèrent la navigation.

### Widgets Systématiques

**Header** : Présent sur toutes les pages
- Logo
- Menu principal
- Language switcher (FR/EN)
- Menu mobile

**Footer** : Présent sur toutes les pages
- Copyright
- Menu secondaire
- Liens externes

### Widgets Conditionnels

**Sidebar** : Pages de documentation uniquement
- Arborescence Nodes/Composites
- Highlight page active
- Navigation hiérarchique

**TOC** : Pages de documentation uniquement
- Table des matières
- Liste des titres H2/H3
- Liens d'ancrage

**Breadcrumb** : Pages de documentation uniquement
- Fil d'ariane
- Hiérarchie des Nodes parents
- Page courante

**Navigation** : Pages de documentation uniquement
- Lien précédent
- Lien suivant
- Entre Composites d'un même Node

**Récapitulatif** : 6 widgets de layout identifiés

---

## Identifier les Widgets de Contenu

Les widgets de contenu affichent les blocs créés dans le back-office.

### Widgets Principaux

**Hero** : Toutes les pages
- En-tête principal
- Variantes selon type de page
- Champs : title, description, lang, image, CTA, breadcrumbTitle

**Feature** : Page d'accueil
- Mise en avant avec icône
- Affichage grille 3 colonnes
- Groupement automatique

**Title** : Pages de documentation
- Titres H2, H3, H4
- Génération d'ancres
- Alimentation TOC

**Content** : Pages de documentation
- Contenu riche WYSIWYG
- Paragraphes et listes

**Code** : Pages de documentation
- Blocs de code avec coloration
- Support multi-langages
- Bouton copier

**Info** : Pages de documentation
- Alertes colorées (info, warning, error)
- Encadrés informatifs

**CTA** : Page d'accueil et documentation
- Call-to-action
- Liens internes

**Card** : Page de contact
- Cards informatives
- Icônes SVG
- CTA optionnels

**Contact** : Page de contact
- Configuration formulaire
- Messages de retour

**Récapitulatif** : 9 widgets de contenu identifiés

---

## Composants Spéciaux

### Dispatcher de Blocs

**Rôle** : Détecter le type de chaque bloc et appeler le widget approprié

**Fonctionnement** :
- Parcourt l'array de blocs
- Détecte Bloc individuel ou Array de blocs groupés
- Appelle le widget correspondant

**Nom** : Blocs.php

### Groupement de Blocs

**Rôle** : Regrouper les Blocs consécutifs pour un affichage en grille

**Fonctionnement** :
- Détecte les Blocs consécutifs ayant le même blocTypeId
- Regroupe en array

**Exemple** : Widget SectionFeatures.php (en plus de BlocFeature.php)

---

## Architecture Planifiée

### Structure des Fichiers

> **Info**
> Cette structure est un exemple d'organisation. Blackcube n'impose aucune structure particulière : adapte l'arborescence selon les besoins de ton projet.

```
common/
├── admin/
│   ├── bloc_card.php
│   ├── bloc_cta.php
│   └── bloc_hero.php
├── helpers/
│   ├── Parameters.php
│   └── CmsHelper.php
└── models/
    └── MailContact.php

webapp/
├── behaviors/
│   └── HrefLangBehavior.php
├── controllers/
│   ├── CmsController.php
│   └── RedirectController.php
├── views/
│   └── cms/
│       ├── home.php
│       ├── documentation.php
│       ├── contact.php
│       └── contact-success.php
└── widgets/
    ├── Blocs.php                    # Dispatcher
    ├── BlocHero.php                 # + 3 vues
    ├── BlocFeature.php
    ├── SectionFeatures.php
    ├── BlocTitle.php
    ├── BlocContent.php
    ├── BlocCode.php
    ├── BlocInfo.php
    ├── BlocCta.php
    ├── BlocCard.php
    ├── Header.php
    ├── Footer.php
    ├── Sidebar.php
    ├── Toc.php
    ├── Breadcrumb.php
    └── Navigation.php
```

### Dépendances entre Composants

**Helpers** :
- Parameters : Utilisé par tous les widgets et controllers
- CmsHelper : Utilisé par controllers et dispatcher

**Controllers** :
- Utilisent les helpers
- Passent les données aux vues

**Vues** :
- Appellent les widgets
- Pas de logique métier

**Widgets** :
- Utilisent les helpers
- Peuvent appeler d'autres widgets

---

## Validation

Avant de continuer, vérifier que tous les composants sont identifiés :

**Types de pages** :
- Home
- Documentation
- Contact

**Widgets de layout** :
- Header
- Footer
- Sidebar
- TOC
- Breadcrumb
- Navigation

**Widgets de contenu** :
- Hero
- Feature (+ SectionFeatures)
- Title
- Content
- Code
- Info
- CTA
- Card
- Contact
- Blocs (dispatcher)

**Helpers** :
- Parameters
- CmsHelper

**Behaviors** :
- HrefLangBehavior

**Controllers** :
- CmsController
- RedirectController

**Modèles** :
- MailContact

**Total** : 6 widgets layout + 10 widgets contenu + 2 helpers + 1 behavior + 2 controllers + 1 modèle