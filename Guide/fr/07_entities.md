# Les entités

Structure du contenu via 4 types d'entités

Blackcube structure le contenu via 4 types d'entités. Une fois maîtrisées, elles permettent d'organiser n'importe quel site. Ces entités sont complémentaires et répondent à des besoins différents : hiérarchie, liberté, étiquetage et regroupement.

Point crucial : toutes les entités peuvent contenir des blocs et générer une page complète. Une rubrique peut afficher des blocs et lister des articles. Un tag peut afficher des blocs et lister le contenu taggué. Une catégorie peut afficher des blocs et lister ses tags ou tout le contenu taggué. Chaque entité est une page à part entière, avec son propre contenu et ses propres listes.

## Les 4 entités de contenu

> **Info**
>
> Toutes les entités peuvent contenir des blocs pour créer une page complète. Le choix dépend de l'organisation souhaitée : Node pour la hiérarchie, Composite pour les contenus libres, Tag pour l'étiquetage transversal, Category pour regrouper les tags.

### Node : L'arborescence

Les Nodes sont des contenus organisés en arborescence hiérarchique.

Cas d'usage :

- Rubriques de site (Organisation du contenu)
- Navigation structurée
- Pages de liste (actualités, blog, portfolio)

Capacités :

- **Contient des blocs** : Peut afficher une page complète avec titre, contenu, images, etc.
- **Liste des Composites ordonnée** : Affiche les articles liés avec ordre manuel géré dans le backoffice
- **Peut être taggué** : Permet de retrouver la rubrique via un tag
- **Hiérarchie parent/enfant** : Organisation en arborescence
- **Combine contenu et liste** : Une rubrique peut afficher ses propres blocs ET la liste des articles
- **Ultra-rapide grâce à Hazel Tree**

> **Info**
>
> Les Nodes sont la seule entité permettant un ordre manuel des articles. L'ordre est géré via la table de liaison dans le backoffice.

Exemple concret :

```
Node "Actualités" (/fr/actualites)
├─ Blocs : [hero, intro, filtres]
├─ Liste : 15 articles (Composites liés)
└─ Rendu : Page avec contenu + liste d'articles

Node "Services" (/fr/services)
├─ Blocs : [hero, text, features, cta]
└─ Pas de liste : Page de contenu pur
```

Exemple d'arborescence :

```
Root (redirection automatique) (Node)
├── Root/fr (Node)
│   ├── /fr (Accueil FR)
│   ├── /fr/a-propos  (Composite)
│   ├── /fr/services (Node)
│   └── /fr/actualites (Node)
└── Root/en
    ├── /en (Accueil EN)
    ├── /en/about (Composite)
    ├── /en/services (Node)
    └── /en/news (Node)
```

### Composite : Les contenus libres

Les Composites sont des contenus sans hiérarchie.

Cas d'usage :

- Articles de blog
- Actualités
- Études de cas
- Témoignages
- Pages autonomes (À propos, Contact)

Capacités :

- **Contient des blocs** : Affiche une page complète avec tous les blocs nécessaires
- **Pas de hiérarchie** : Contenu indépendant, non lié à une structure d'arborescence
- **Peut être lié à un Node** : Pour apparaître dans une liste (ex: article listé dans la rubrique Actualités)
- **Peut être taggué** : Permet de retrouver le contenu via un tag

Exemple concret :

```
Composite "Nouvelle gamme 2025" (/fr/actualites/nouvelle-gamme-2025)
├─ Type : actualite
├─ Blocs : [hero, text, image-gallery, cta]
├─ Lié à : Node "Actualités" (pour apparaître dans la liste)
├─ Tags : "Produits", "Innovation"
└─ Rendu : Page d'article complète

Composite "À propos" (/fr/a-propos)
├─ Type : page-standard
├─ Blocs : [hero, text, team, timeline]
├─ Non lié à un Node : Page autonome
└─ Rendu : Page de contenu
```

### Tag : L'étiquetage

Les Tags sont des étiquettes pour catégoriser les contenus de manière transversale.

Cas d'usage :

- Taguer par type de média (Podcast, Vidéo, Article)
- Taguer par thématique (Tech, Marketing, RH)
- Taguer par format (Tutoriel, Interview, Analyse)
- Pages de tag avec contenu éditorial

Capacités :

- **Contient des blocs** : Affiche une page complète dédiée au tag avec intro, description, filtres
- **Liste des Composites taggués** : Affiche tous les articles/contenus ayant ce tag (ordre automatique)
- **Liste des Nodes taggués** : Affiche toutes les rubriques ayant ce tag (ordre automatique)
- **Combine contenu et listes** : Page de tag avec contenu éditorial + liste de contenu taggué

> **Attention**
>
> Les listes générées par les Tags sont automatiques et ne peuvent pas être ordonnées manuellement. L'ordre dépend de critères comme la date de création, date de mise à jour, etc.

Exemple concret :

```
Tag "Podcast" (/fr/media/podcasts)
├─ Type : media-tag
├─ Blocs : [hero, description, featured-podcasts]
├─ Liste Composites : 15 épisodes de podcast
├─ Liste Nodes : 2 rubriques (Podcast Tech, Podcast Business)
└─ Rendu : Page avec présentation + liste complète des podcasts

Tag "Innovation" (/fr/themes/innovation)
├─ Type : theme-tag
├─ Blocs : [title, intro]
├─ Liste Composites : 42 articles taggués "Innovation"
└─ Rendu : Page thématique avec liste d'articles
```

### Category : Le regroupement de tags

Les Categories regroupent les Tags par famille et permettent une navigation à deux niveaux.

Cas d'usage :

- Category "Médias" regroupant "Podcast", "Vidéo", "Infographie"
- Category "Secteurs" regroupant "Industrie", "Services", "Public"
- Pages de catégorie avec vue d'ensemble

Capacités :

- **Contient des blocs** : Affiche une page complète avec présentation de la catégorie
- **Liste les Tags** : Affiche tous les tags de cette catégorie (ordre automatique)
- **Liste les Composites taggués** : Affiche directement tous les articles ayant un tag de la catégorie (ordre automatique)
- **Liste les Nodes taggués** : Affiche toutes les rubriques ayant un tag de la catégorie (ordre automatique)
- **Navigation à deux niveaux** : Category → Tags → Contenus
- **Combine tout** : Blocs + liste de tags + liste de contenus simultanément

> **Attention**
>
> Comme pour les Tags, les listes générées par les Categories sont automatiques et ne peuvent pas être ordonnées manuellement. L'ordre dépend de critères automatiques.

Exemple concret :

```
Category "Médias" (/fr/medias)
├─ Type : media-category
├─ Blocs : [hero, intro, featured]
├─ Liste Tags : [Podcast (15), Vidéo (8), Article (42)]
├─ Liste Composites : 65 contenus au total (tous les contenus des 3 tags)
├─ Liste Nodes : 3 rubriques média
└─ Rendu : Page catégorie avec vue d'ensemble + accès aux tags + tous les contenus

Variante 1 : Focus sur les tags
Category "Formats"
├─ Blocs : [title, description]
├─ Liste Tags uniquement : [Tutoriel, Interview, Analyse]
└─ Rendu : Page de navigation vers les tags

Variante 2 : Liste complète
Category "Secteurs"
├─ Blocs : [hero, stats]
├─ Liste Composites directement : Tous les articles de tous les secteurs
└─ Rendu : Page avec tous les contenus agrégés
```

## Principe commun à toutes les entités

**Toutes les entités peuvent générer une page complète** :

1. **Blocs** : Chaque entité peut contenir des blocs pour créer son contenu (hero, texte, images, CTA, etc.)
2. **Listes** : Chaque entité peut afficher des listes selon son type :
   - **Node** : Liste ses Composites liés avec ordre manuel géré dans le backoffice
   - **Composite** : N'affiche pas de liste (c'est le contenu listé)
   - **Tag** : Liste les contenus taggués (Composites et/ou Nodes) avec ordre automatique
   - **Category** : Liste ses Tags et/ou tous les contenus taggués avec ordre automatique
3. **Combinaison** : Blocs + listes peuvent coexister sur la même page

> **Important**
>
> Seuls les Nodes permettent un ordre manuel des contenus listés. Les Tags et Categories génèrent des listes automatiques basées sur des critères (date de création, date de mise à jour, etc.) sans possibilité d'ordre manuel.

**Exemple complet d'une rubrique "Blog"** :

```php
// Node "Blog" avec contenu ET liste
$node->blocs = [
    BlocType "hero" : Titre + description du blog
    BlocType "featured" : Articles mis en avant
    BlocType "text" : Présentation de la ligne éditoriale
];

$node->composites = [
    Article 1, Article 2, Article 3... (liste paginée)
];

// Rendu final :
// - Hero personnalisé
// - Section featured (articles choisis)
// - Texte d'intro
// - Liste complète des articles
```

## Points clés à retenir

1. **4 entités complémentaires** : Node (hiérarchie), Composite (libre), Tag (étiquetage), Category (regroupement)
2. **Toutes génèrent des pages** : Chaque entité peut contenir des blocs et afficher du contenu
3. **Toutes peuvent lister** : Sauf Composite, toutes les entités peuvent afficher des listes de contenu
4. **Flexibilité totale** : Combiner blocs et listes selon les besoins de chaque page
5. **Slugs uniques** : Chaque contenu a une URL unique gérée automatiquement
6. **Liberté d'organisation** : Le choix des entités dépend de la structure souhaitée, pas de contrainte imposée