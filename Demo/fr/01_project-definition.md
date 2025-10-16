# Définition du Projet
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : PHP, Framework MVC Yii2 est un plus

---

## Objectif

Créer un **site de documentation professionnel** pour Blackcube CMS qui démontre les capacités du CMS en situation réelle.

Ce projet sert deux objectifs :

1. **Vitrine technique** : Démonstration concrète des capacités Blackcube
2. **Cas d'usage réel** : Exemple d'implémentation complète et fonctionnelle

> **Info**
> Ce guide présente des exemples d'architecture et d'utilisation. Blackcube n'impose rien : adapte les structures, widgets et approches selon les besoins de ton projet. Le développeur reste maître de son code et de ses choix.

---

## Public Cible

Développeurs cherchant à :
- Évaluer Blackcube CMS pour leurs projets
- Comprendre comment structurer un projet Blackcube
- Voir un exemple concret d'implémentation

---

## Stack Technique

**Backend** :
- Yii2 (PHP 8.3+)
- Blackcube (core + admin)
- MySQL 8.0 / MariaDB 10.11

**Frontend** :
- Tailwind CSS v4
- Vanilla JS
- Heroicons (SVG)

---

## Fonctionnalités Démontrées

### Structure de Contenu

**BlocTypes et Types** :
- 9 BlocTypes avec JSON Schema
- 5 Types de pages
- Templates d'admin personnalisés

**Arborescence** :
- Nodes (Rubriques)
- Composites (Articles)
- Relations parent/enfant

### Multilingue

- Structure FR/EN parallèle
- Language switcher
- Balises hreflang automatiques

### SEO

- Configuration complète par élément
- Sitemap XML
- URLs propres

### Navigation

- Sidebar hiérarchique
- Breadcrumb automatique
- TOC extraite des titres
- Navigation prev/next

### Administration

- Paramètres centralisés
- Menus configurables
- Interface de saisie personnalisée

---

## Livrables

### Configuration Back-Office

- 9 BlocTypes configurés
- 5 Types de contenu
- Paramètres centralisés
- Menus (header/footer FR/EN)
- Arborescence complète

### Code PHP

- Helpers (Parameters, CmsHelper)
- Controllers (Cms, Redirect)
- Behavior (HrefLang)
- Modèle (MailContact)
- Widgets de blocs et de layout
- Vues
- Templates d'admin

### Contenu

- Pages d'accueil (FR/EN)
- Pages contact (FR/EN)
- Articles de documentation

---

## Structure du Site

### Arborescence

```
/ (Redirect Language)
├── /fr (Home FR)
│   ├── /fr/guide (Documentation FR)
│   │   └── /fr/guide/* (Articles)
│   ├── /fr/a-propos (À propos FR)
│   │   └── /fr/a-propos/* (Articles)
│   └── /fr/contact (Contact FR)
│
└── /en (Home EN)
    ├── /en/guide (Documentation EN)
    │   └── /en/guide/* (Articles)
    ├── /en/about (About EN)
    │   └── /en/about/* (Articles)
    └── /en/contact (Contact EN)
```

### Types de Pages

**Page d'accueil** :
- Type : Home
- Layout : Header + Hero + Features + CTA + Footer

**Pages de documentation** :
- Type : Documentation
- Layout : Header + Sidebar + Contenu + TOC + Footer

**Page de contact** :
- Type : Contact
- Layout : Header + Hero + Formulaire + Sidebar + Footer

---

## Flexibilité Architecturale

L'approche widgets/dispatcher présentée est UN exemple. Chaque projet peut avoir sa propre architecture :
- Widgets réutilisables
- Code direct dans les vues
- Approche mixte

> **Info**
> Blackcube ne force aucune structure particulière.

---

## Validation

Avant de continuer :
- Objectif du projet compris
- Stack technique validée
- Fonctionnalités à démontrer identifiées