# Réflexion sur les Types
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Découpage Logique

---

## Objectif

Définir les Types de contenu du projet et identifier quels BlocTypes chaque Type peut utiliser. Cette réflexion structure l'organisation du contenu dans le back-office.

---

## Qu'est-ce qu'un Type ?

Un Type est un modèle de page qui associe :
- **Des BlocTypes autorisés** : Quels blocs peuvent être ajoutés
- **Une route** : Quel controller/action affichera le contenu

## Différence Type vs BlocType

**BlocType** : Structure de données (champs de saisie)
**Type** : Modèle de page (combinaison de BlocTypes + controller)

---

## Types à Créer

### Type 1 : Home

**Usage** : Pages d'accueil FR/EN

**Route** : `cms/home`

**BlocTypes autorisés** :
- Hero (H1)
- Feature (H3)
- Cta

**Justification** :
- Hero pour l'en-tête principal avec image et 2 CTA
- Features pour présenter les fonctionnalités en grille 3 colonnes
- Cta pour la conversion finale

**Article autorisé** : Coché

---

### Type 2 : Documentation

**Usage** : Articles de documentation technique

**Route** : `cms/documentation`

**BlocTypes autorisés** :
- Hero (H1)
- Titre (H2 - H4)
- Content
- Code
- Info
- Feature (H3)
- Cta
- Card

**Justification** :
- Hero pour l'en-tête de page
- Titre pour structurer le contenu (H2, H3, H4)
- Content pour les paragraphes
- Code pour les exemples
- Info pour les alertes
- Feature optionnel pour des mises en avant
- Cta pour la navigation
- Card optionnel pour des encarts

**Article autorisé** : Coché

---

### Type 3 : Contact

**Usage** : Pages de contact FR/EN

**Route** : `cms/contact`

**BlocTypes autorisés** :
- Hero (H1)
- Card
- Info
- Contact

**Justification** :
- Hero pour l'en-tête
- Card pour les informations de contact en sidebar
- Info pour des alertes éventuelles
- Contact pour configurer le formulaire

**Article autorisé** : Coché

---

### Type 4 : Redirect Language

**Usage** : Node racine pour détection de langue

**Route** : `cms/redirect-language`

**BlocTypes autorisés** : Aucun

**Justification** :
- Ce Type ne contient pas de contenu
- Il redirige automatiquement vers /fr ou /en selon la langue du navigateur

**Article autorisé** : Non coché

---

### Type 5 : Redirect First Composite

**Usage** : Nodes containers (Guide, À propos, About)

**Route** : `cms/redirect-first-composite`

**BlocTypes autorisés** : Aucun

**Justification** :
- Ce Type ne contient pas de contenu
- Il redirige automatiquement vers le premier Composite actif du Node

**Article autorisé** : Non coché

---

## BlocTypes Autorisés par Type

### Type Home
- Hero
- Feature
- Cta

### Type Documentation
- Hero
- Feature
- Titre
- Content
- Code
- Info
- Cta
- Card

### Type Contact
- Hero
- Card
- Info
- Contact

### Type Redirect Language
Aucun BlocType

### Type Redirect First Composite
Aucun BlocType

---

## Logique de Restriction

### Types avec Contenu Limité

**Home** : Seulement Hero + Features + Cta
- Limitation volontaire pour garder un format homogène
- Pas de contenu riche pour rester sur une page de présentation

**Contact** : Seulement Hero + Cards + Info + Contact
- Structure fixe : formulaire + sidebar informative
- Pas de contenu riche car page utilitaire

### Types avec Contenu Riche

**Documentation** : Presque tous les BlocTypes
- Flexibilité maximale pour créer du contenu technique varié
- Support code, alertes, mise en forme avancée

### Types sans Contenu

**Redirect Language** et **Redirect First Composite** : Aucun BlocType
- Purement techniques, pas de contenu éditorial
- Uniquement des redirections automatiques

---

## Validation

Avant de continuer, vérifier :

**Types définis** :
- Home (3 BlocTypes)
- Documentation (8 BlocTypes)
- Contact (4 BlocTypes)
- Redirect Language (0 BlocType)
- Redirect First Composite (0 BlocType)

**Total** : 5 Types

**Matrice complète** : Chaque Type a ses BlocTypes autorisés clairement identifiés