# Réflexion sur les BlocTypes
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Réflexion sur les Types

---

## Objectif

Définir la structure de chaque BlocType avec ses champs, identifier les variantes et planifier les templates d'administration personnalisés.

> **Info**
> Cette étape est une réflexion. La création effective des BlocTypes dans le back-office se fera après le développement du code PHP.

---

## Définir les BlocTypes

> **Info**
> Les préfixes numériques (01., 02., etc.) dans les noms des BlocTypes servent uniquement à les ordonner dans les listes du back-office. Ils ne sont pas affichés au contributeur lors de la création de contenu.

### BlocType 1 : Hero (H1)

**Usage** : Toutes les pages

**Nom** : `01. Hero (H1)`
**Vue** : `bloc_hero`

**Champs** :
- `lang` : Contenu associé pour l'autre langue (string)
- `title` : Titre H1 (string, requis)
- `breadcrumbTitle` : Titre pour le fil d'ariane (string, optionnel)
- `description` : Description WYSIWYG (string, requis)
- `image` : Image de fond (file PNG/JPG, optionnel)
- `ctaTitle` : Titre du CTA primaire (string, optionnel)
- `ctaTarget` : Cible du CTA primaire (string, optionnel)
- `ctaSecondaryTitle` : Titre du CTA secondaire (string, optionnel)
- `ctaSecondaryTarget` : Cible du CTA secondaire (string, optionnel)

**Champs obligatoires** : title, description

---

### BlocType 2 : Titre (H2 - H4)

**Usage** : Pages de documentation

**Nom** : `03. Titre (H2 - H4)`
**Vue** : `bloc_title`

**Champs** :
- `hn` : Niveau du titre (dropdown H2/H3/H4, requis)
- `title` : Texte du titre (string, requis)

**Champs obligatoires** : hn, title

---

### BlocType 3 : Content

**Usage** : Pages de documentation

**Nom** : `04. Content`
**Vue** : `bloc_content`

**Champs** :
- `content` : Contenu riche (WYSIWYG, requis)

**Options WYSIWYG** :
- Toolbar : bold, italic, underline, list, link
- Formats autorisés : bold, italic, link, underline, list

**Champs obligatoires** : content

---

### BlocType 4 : Code

**Usage** : Pages de documentation

**Nom** : `06. Code`
**Vue** : `bloc_code`

**Champs** :
- `language` : Langage utilisé (dropdown, requis)
- `code` : Bloc de code (textarea, requis)

**Langages disponibles** : PHP, SQL, JSON, Typescript, HTML, SCSS, Javascript, CSS, Bash, Texte, GraphQL

**Champs obligatoires** : language, code

---

### BlocType 5 : Info

**Usage** : Pages de documentation

**Nom** : `05. Info`
**Vue** : `bloc_info`

**Champs** :
- `type` : Type d'alerte (dropdown info/warning/error, requis)
- `title` : Titre de l'infobox (string, requis)
- `content` : Contenu de l'infobox (WYSIWYG, requis)

**Options WYSIWYG** :
- Toolbar : bold, italic, underline, list
- Formats autorisés : bold, italic, underline, list

**Champs obligatoires** : type, title, content

---

### BlocType 6 : Feature (H3)

**Usage** : Page d'accueil

**Nom** : `02. Feature (H3)`
**Vue** : `bloc_feature`

**Champs** :
- `icon` : Icône SVG (file SVG, requis)
- `title` : Titre H3 (string, requis)
- `description` : Description courte (WYSIWYG, requis)

**Options WYSIWYG** :
- Toolbar : bold, italic, underline, list
- Formats autorisés : bold, italic, underline, list

**Champs obligatoires** : icon, title, description

---

### BlocType 7 : Cta

**Usage** : Page d'accueil et documentation

**Nom** : `04. Cta`
**Vue** : `bloc_cta`

**Champs** :
- `title` : Titre H2 (string, requis)
- `description` : Description (WYSIWYG, requis)
- `ctaTitle` : Titre du bouton (string, requis)
- `ctaTarget` : Cible du lien (string, requis)

**Options WYSIWYG** :
- Toolbar : bold, italic, underline, list
- Formats autorisés : bold, italic, underline, list

**Champs obligatoires** : title, description, ctaTitle, ctaTarget

---

### BlocType 8 : Card

**Usage** : Page de contact

**Nom** : `04. Card`
**Vue** : `bloc_card`

**Champs** :
- `icon` : Icône SVG (file SVG, requis)
- `title` : Titre (string, requis)
- `description` : Description (WYSIWYG, requis)
- `link` : Titre du lien (string, optionnel)
- `linkRoute` : Cible du lien (string, optionnel)
- `linkUrl` : URL brute (string, optionnel)

**Options WYSIWYG** :
- Toolbar : bold, italic, underline
- Formats autorisés : bold, italic, underline

**Champs obligatoires** : icon, title, description

---

### BlocType 9 : Contact

**Usage** : Page de contact uniquement

**Nom** : `07. Contact`
**Vue** : `bloc_contact`

**Champs** :
- `title` : Titre du formulaire H2 (string, requis)
- `successTitle` : Titre en cas de succès H2 (string, requis)
- `success` : Message de succès (WYSIWYG, requis)

**Options WYSIWYG** :
- Toolbar : bold, italic, underline
- Formats autorisés : bold, italic, underline

**Champs obligatoires** : title, successTitle, success

---

## Identifier les Variantes

### Hero : Variantes selon le Type de Page

Le BlocType Hero contient tous les champs possibles. Les variantes sont gérées au niveau du widget PHP :

**Variante Home** :
- Affiche : title, description, image, ctaTitle, ctaTarget, ctaSecondaryTitle, ctaSecondaryTarget
- Masque : breadcrumbTitle
- Layout : Pleine largeur avec image de fond

**Variante Documentation** :
- Affiche : title, description, breadcrumbTitle
- Masque : image, CTA
- Layout : Simplifié sans image

**Variante Contact** :
- Affiche : title, description
- Masque : image, CTA, breadcrumbTitle
- Layout : Simplifié sans image

**Champ lang** : Présent dans toutes les variantes pour la liaison multilingue

---

## Templates d'Administration

Certains BlocTypes nécessitent des interfaces personnalisées dans le back-office pour améliorer l'expérience de saisie.

### Template 1 : bloc_hero.php

**BlocType** : Hero (H1)

**Besoin** : Dropdowns pour sélectionner les cibles

**Champs concernés** :
- `lang` : Dropdown listant tous les Nodes/Composites pour liaison FR/EN
- `ctaTarget` : Dropdown listant tous les Nodes/Composites
- `ctaSecondaryTarget` : Dropdown listant tous les Nodes/Composites

**Raison** : Évite la saisie manuelle de routes, affiche une liste organisée par type (Rubriques/Articles) avec indication de langue

---

### Template 2 : bloc_cta.php

**BlocType** : Cta

**Besoin** : Dropdown pour sélectionner la cible

**Champs concernés** :
- `ctaTarget` : Dropdown listant tous les Nodes/Composites

**Raison** : Évite la saisie manuelle de routes, propose tous les contenus disponibles

---

### Template 3 : bloc_card.php

**BlocType** : Card

**Besoin** : Dropdown pour sélectionner la cible

**Champs concernés** :
- `linkRoute` : Dropdown listant tous les Nodes/Composites

**Raison** : Évite la saisie manuelle de routes, permet de choisir facilement le contenu cible

---

## Récapitulatif

### BlocTypes Définis

**Total** : 9 BlocTypes

**Avec variantes** : 1 (Hero)
**Avec templates admin** : 3 (Hero, Cta, Card)

### Liste Complète

- Hero (H1) - bloc_hero - Template admin
- Titre (H2-H4) - bloc_title
- Content - bloc_content
- Code - bloc_code
- Info - bloc_info
- Feature (H3) - bloc_feature
- Cta - bloc_cta - Template admin
- Card - bloc_card - Template admin
- Contact - bloc_contact

---

## Validation

Avant de continuer, vérifier :

- 9 BlocTypes définis avec leurs champs
- Champs obligatoires identifiés pour chaque BlocType
- Variantes du Hero planifiées
- 3 templates d'admin à développer identifiés