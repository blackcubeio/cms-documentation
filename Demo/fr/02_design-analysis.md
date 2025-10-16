# Récupération et Analyse des Créas
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Définition du Projet

---

## Objectif

Analyser les maquettes pour identifier les types de pages, les composants récurrents et leurs variations. Cette analyse détermine la structure des BlocTypes et Types à créer.

---

## Types de Pages Identifiés

### Page d'Accueil

**Caractéristiques** :
- Hero pleine largeur avec image de fond
- Titre H1 principal
- Description courte
- 2 boutons CTA (primaire et secondaire)
- Section features en grille 3 colonnes
- CTA de conversion finale

**Layout** :
```
Header
Hero (pleine largeur, image de fond)
Features (grille 3 colonnes avec icônes)
CTA (section pleine largeur)
Footer
```

### Pages de Documentation

**Caractéristiques** :
- Layout 3 colonnes
- Sidebar navigation gauche (fixe)
- Contenu central (largeur limitée pour lecture)
- TOC droite (fixe)
- Breadcrumb au-dessus du contenu
- Navigation prev/next en bas
- Hero simplifié sans image

**Layout** :
```
Header
┌──────────┬─────────────────────┬──────────┐
│ Sidebar  │ Breadcrumb          │ TOC      │
│ (fixe)   │ Hero (simplifié)    │ (fixe)   │
│          │ Contenu (variable)  │          │
│          │ Navigation          │          │
└──────────┴─────────────────────┴──────────┘
Footer
```

### Page de Contact

**Caractéristiques** :
- Hero simple sans image
- Layout 2 colonnes inégales
- Formulaire gauche (60%)
- Cards informatives droite (40%)
- Hero simplifié

**Layout** :
```
Header
Hero (simplifié)
┌───────────────────────┬──────────────┐
│ Formulaire (60%)      │ Cards (40%)  │
│ - Nom                 │ - Email      │
│ - Email               │ - Support    │
│ - Sujet               │ - Github     │
│ - Message             │              │
└───────────────────────┴──────────────┘
Footer
```

---

## Composants de Layout

### Header

**Récurrent** : Toutes les pages

**Contenu** :
- Logo cliquable
- Menu navigation principale
- Language switcher (FR/EN)
- Menu mobile (hamburger)

### Footer

**Récurrent** : Toutes les pages

**Contenu** :
- Copyright dynamique
- Menu navigation secondaire
- Liens externes (Github)

### Sidebar

**Conditionnel** : Pages de documentation uniquement

**Contenu** :
- Arborescence hiérarchique Nodes/Composites
- Highlight page active
- Génération automatique

### TOC

**Conditionnel** : Pages de documentation uniquement

**Contenu** :
- Table des matières
- Liste des titres H2/H3
- Liens d'ancrage
- Fixe à droite

### Breadcrumb

**Conditionnel** : Pages de documentation uniquement

**Contenu** :
- Fil d'ariane
- Hiérarchie des Nodes parents
- Page courante non cliquable

### Navigation

**Conditionnel** : Pages de documentation uniquement

**Contenu** :
- Lien précédent (si existe)
- Lien suivant (si existe)
- Navigation entre Composites d'un même Node

---

## Composants de Contenu

### Hero

**Usage** : Toutes les pages

**Champs du BlocType** :
- Titre H1
- Description WYSIWYG
- Champ `lang` pour liaison multilingue
- Image de fond (optionnel)
- 2 CTA primaire et secondaire (optionnels)
- Champ `breadcrumbTitle` pour fil d'ariane (optionnel)

**Variantes gérées en PHP** :
- Home : Affiche image + 2 CTA
- Documentation : Affiche breadcrumbTitle, pas d'image ni CTA
- Contact : Pas d'image ni CTA

### Feature

**Usage** : Page d'accueil

**Champs du BlocType** :
- Icône SVG
- Titre H3
- Description courte WYSIWYG

**Affichage** :
- Regroupement en grille 3 colonnes
- Groupement automatique des features consécutives

### Title

**Usage** : Pages de documentation

**Champs du BlocType** :
- Niveau (H2, H3, H4) via dropdown
- Texte du titre

**Comportement** :
- Génère une ancre automatique
- Alimente la TOC

### Content

**Usage** : Pages de documentation

**Champs du BlocType** :
- Contenu riche WYSIWYG
- Toolbar limitée (bold, italic, list, link)

### Code

**Usage** : Pages de documentation

**Champs du BlocType** :
- Langage via dropdown (PHP, SQL, JSON, TypeScript, HTML, SCSS, Javascript, CSS, Bash, Texte, GraphQL)
- Code brut (textarea)

**Affichage** :
- Coloration syntaxique
- Bouton copier

### Info

**Usage** : Pages de documentation

**Champs du BlocType** :
- Type via dropdown (info, warning, error)
- Titre
- Contenu WYSIWYG

**Affichage** :
- Couleur selon type (bleu, jaune, rouge)
- Bordure gauche épaisse

### CTA

**Usage** : Page d'accueil, pages de documentation

**Champs du BlocType** :
- Titre H2
- Description WYSIWYG
- Texte bouton
- Target via dropdown (sélection Node/Composite)

### Card

**Usage** : Page de contact

**Champs du BlocType** :
- Icône SVG
- Titre
- Description WYSIWYG
- Lien optionnel (texte + target)

### Contact

**Usage** : Page de contact uniquement

**Champs du BlocType** :
- Titre formulaire
- Titre succès
- Message succès WYSIWYG

**Comportement** :
- Configure le formulaire
- Définit les messages de retour

---

## Récapitulatif des Variations

### Composants avec Variantes

**Hero** : Variantes gérées au niveau du widget PHP selon le type de page
- Tous les champs dans le BlocType
- Champs optionnels utilisés selon besoin

**Features** : Groupement dynamique
- Individuelles : Affichage card simple
- Multiples consécutives : Grille 3 colonnes

### Composants Uniques

Tous les autres composants (Title, Content, Code, Info, CTA, Card, Contact) ont une seule variante.

---

## Implications pour les BlocTypes

### BlocTypes à Créer

D'après l'analyse des créas :

**Hero** : 1 BlocType avec tous les champs
- Champs : title, description, lang, image, ctaTitle, ctaTarget, ctaSecondaryTitle, ctaSecondaryTarget, breadcrumbTitle
- Variantes gérées dans le widget PHP

**Feature** : 1 BlocType
- Champs : icon, title, description

**Title** : 1 BlocType
- Champs : hn, title

**Content** : 1 BlocType
- Champs : content

**Code** : 1 BlocType
- Champs : language, code

**Info** : 1 BlocType
- Champs : type, title, content

**CTA** : 1 BlocType
- Champs : title, description, ctaTitle, ctaTarget

**Card** : 1 BlocType
- Champs : icon, title, description, link, linkRoute, linkUrl

**Contact** : 1 BlocType
- Champs : title, successTitle, success

**Total** : 9 BlocTypes

---

## Implications pour les Types

### Types à Créer

**Home** :
- Route : cms/home
- BlocTypes autorisés : Hero, Feature, CTA
- Usage : Pages d'accueil

**Documentation** :
- Route : cms/documentation
- BlocTypes autorisés : Hero, Title, Content, Code, Info, Feature, CTA, Card
- Usage : Articles de documentation

**Contact** :
- Route : cms/contact
- BlocTypes autorisés : Hero, Card, Info, Contact
- Usage : Pages de contact

**Redirect Language** :
- Route : cms/redirect-language
- BlocTypes autorisés : Aucun
- Usage : Node racine détection langue

**Redirect First Composite** :
- Route : cms/redirect-first-composite
- BlocTypes autorisés : Aucun
- Usage : Nodes containers

**Total** : 5 Types

---

## Templates d'Admin à Prévoir

Certains BlocTypes nécessitent des interfaces personnalisées pour faciliter la saisie.

### Hero

**Besoin** : Dropdown pour sélectionner le contenu lié (champ `lang`)

**Raison** : Permet de lier FR/EN pour le language switcher et les balises hreflang

### CTA

**Besoin** : Dropdown pour sélectionner la cible (champ `ctaTarget`)

**Raison** : Évite la saisie manuelle de routes, propose tous les Nodes/Composites

### Card

**Besoin** : Dropdown pour sélectionner la cible (champ `linkRoute`)

**Raison** : Évite la saisie manuelle de routes, propose tous les Nodes/Composites

**Total** : 3 templates d'admin

---

## Validation

Avant de continuer :
- 3 types de pages identifiés
- Composants de layout listés (6 widgets)
- Composants de contenu listés (9 widgets)
- Variations des composants identifiées
- 9 BlocTypes définis
- 5 Types définis
- 3 templates d'admin planifiés