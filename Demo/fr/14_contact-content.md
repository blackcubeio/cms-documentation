# Pages de Contact (Remplissage Contenu)
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Préparation des Rubriques

---

## Objectif

Créer et remplir les pages de contact françaises et anglaises avec leurs blocs Hero, Cards et Contact dans le back-office. Configurer les URLs, le sitemap et le SEO.

---

## Vue d'ensemble

Les pages Contact utilisent le Type **Contact** qui autorise 4 BlocTypes :
- **Hero (H1)** : En-tête de page
- **Card** : Informations de contact en sidebar
- **Info** : Alertes éventuelles
- **Contact** : Configuration du formulaire

**Structure finale** :
```
/fr/contact (Composite FR)
└── Blocs :
    ├── Hero (H1)
    ├── Card × 3 (Support, GitHub, Documentation)
    ├── Info (Temps de réponse)
    └── Contact

/en/contact (Composite EN)
└── Blocs :
    ├── Hero (H1)
    ├── Card × 3 (Support, GitHub, Documentation)
    ├── Info (Response time)
    └── Contact
```

---

## Créer le Composite Contact FR

### Étape 1 : Création du Composite

**Accès** : Menu latéral → **Gestion → Articles** → Bouton **Ajouter un article**

**Onglet Article** :
1. **Actif** : Coché
2. **Type** : Sélectionner `Contact`
3. **Langue** : Sélectionner `Français`
4. **Nom** : `Contact`
5. **Rubrique** : Sélectionner **FR** dans le dropdown
6. Enregistrer

Le Composite est créé avec un ID unique (visible dans l'URL : `/bo/composite/edit?id=XX`).

---

### Étape 2 : Remplir le Hero

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **01. Hero (H1)**

**Champs à remplir** :

**title** (obligatoire) :
```
Contact
```

**description** (obligatoire) :
```
<p>Une question sur Blackcube CMS ? N'hésitez pas à nous contacter, nous sommes là pour vous aider.</p>
```

**breadcrumbTitle** (optionnel) :
```
Contact
```

**lang** (liaison multilingue) :
- Sélectionner dans le dropdown → **Articles** → **Contact** (EN)
- Cette liaison permet au language switcher de fonctionner

**Champs non utilisés** :
- image : Laisser vide
- ctaTitle : Laisser vide
- ctaTarget : Laisser vide
- ctaSecondaryTitle : Laisser vide
- ctaSecondaryTarget : Laisser vide

Enregistrer le bloc.

> **Info**
> Le champ `lang` ne peut être rempli qu'après avoir créé le Composite Contact EN. Vous devrez revenir modifier ce champ après l'étape "Créer le Composite Contact EN".

---

### Étape 3 : Remplir les Cards

#### Card 1 : Support

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Card**

**Champs à remplir** :

**icon** (obligatoire) :
- Upload du fichier SVG `life-ring.svg`

**title** (obligatoire) :
```
Support
```

**description** (obligatoire) :
```
<p>Pour toute question technique, consultez d'abord notre documentation complète.</p>
```

**link** (optionnel) :
```
Voir la documentation >
```

**linkRoute** (obligatoire) :
- Sélectionner dans le dropdown → **Rubriques** → **Guide** (FR)

**linkUrl** (optionnel) :
- Laisser vide (on utilise linkRoute pour un lien interne)

Enregistrer le bloc.

---

#### Card 2 : GitHub

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Card**

**Champs à remplir** :

**icon** (obligatoire) :
- Upload du fichier SVG `github.svg`

**title** (obligatoire) :
```
GitHub
```

**description** (obligatoire) :
```
<p>Retrouvez le code source, signalez un bug ou contribuez au projet sur GitHub.</p>
```

**link** (optionnel) :
```
github.com/blackcubeio
```

**linkUrl** (optionnel) :
```
https://github.com/blackcubeio
```

**linkRoute** (optionnel) :
- Laisser vide (on utilise linkUrl pour un lien externe)

Enregistrer le bloc.

---

#### Card 3 : Documentation

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Card**

**Champs à remplir** :

**icon** (obligatoire) :
- Upload du fichier SVG `book-open.svg`

**title** (obligatoire) :
```
Documentation
```

**description** (obligatoire) :
```
<p>Guides complets, tutoriels et références API pour maîtriser Blackcube CMS.</p>
```

**link** (optionnel) :
```
Explorer la documentation >
```

**linkRoute** (obligatoire) :
- Sélectionner dans le dropdown → **Rubriques** → **Guide** (FR)

**linkUrl** (optionnel) :
- Laisser vide (on utilise linkRoute pour un lien interne)

Enregistrer le bloc.

---

### Étape 4 : Remplir l'Info Box

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **05. Info**

**Champs à remplir** :

**type** (obligatoire) :
- Sélectionner `info` dans le dropdown

**title** (obligatoire) :
```
Temps de réponse
```

**content** (obligatoire) :
```
<p>Nous nous efforçons de répondre dans les 48 heures ouvrées.</p>
```

Enregistrer le bloc.

---

### Étape 5 : Remplir le Bloc Contact

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **07. Contact**

**Champs à remplir** :

**title** (obligatoire) :
```
Envoyez-nous un message
```

**successTitle** (obligatoire) :
```
Message envoyé !
```

**success** (obligatoire) :
```
<p>Merci pour votre message. Nous vous répondrons dans les plus brefs délais.</p>
```

Enregistrer le bloc.

---

### Étape 6 : Configuration URL

> **Info**
> Dans Blackcube, le chemin d'URL est toujours **complet et absolu** pour tous les éléments (Nodes, Composites, Categories, Tags). On n'hérite jamais automatiquement du chemin parent.

**Onglet URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `fr/contact`
4. Enregistrer

URL publique finale : `/fr/contact`

---

### Étape 7 : Configuration Sitemap

**Onglet Sitemap** :

1. **Actif** : Coché
2. **Fréquence** : `monthly`
3. **Priorité** : `0.5`
4. Enregistrer

---

### Étape 8 : Configuration SEO

**Onglet SEO** :

1. **Actif** : Coché
2. **Titre** : `Contact - Blackcube CMS`
3. **Description** : `Contactez l'équipe Blackcube. Questions, suggestions ou support technique, nous sommes là pour vous aider.`
4. Enregistrer

---

## Créer le Composite Contact EN

### Étape 1 : Création du Composite

**Accès** : Menu latéral → **Gestion → Articles** → Bouton **Ajouter un article**

**Onglet Article** :
1. **Actif** : Coché
2. **Type** : Sélectionner `Contact`
3. **Langue** : Sélectionner `English`
4. **Nom** : `Contact`
5. **Rubrique** : Sélectionner **EN** dans le dropdown
6. Enregistrer

---

### Étape 2 : Remplir le Hero

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **01. Hero (H1)**

**Champs à remplir** :

**title** (obligatoire) :
```
Contact Us
```

**description** (obligatoire) :
```
<p>Have a question? A suggestion? We'd love to hear from you about Blackcube CMS.</p>
```

**breadcrumbTitle** (optionnel) :
```
Contact
```

**lang** (liaison multilingue) :
- Sélectionner dans le dropdown → **Articles** → **Contact** (FR)

**Champs non utilisés** :
- image : Laisser vide
- ctaTitle : Laisser vide
- ctaTarget : Laisser vide
- ctaSecondaryTitle : Laisser vide
- ctaSecondaryTarget : Laisser vide

Enregistrer le bloc.

---

### Étape 3 : Remplir les Cards

#### Card 1 : Support

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Card**

**Champs à remplir** :

**icon** (obligatoire) :
- Upload du fichier SVG `life-ring.svg`

**title** (obligatoire) :
```
Support
```

**description** (obligatoire) :
```
<p>For any technical questions, please check our comprehensive documentation first.</p>
```

**link** (optionnel) :
```
View documentation >
```

**linkRoute** (obligatoire) :
- Sélectionner dans le dropdown → **Rubriques** → **Guide** (EN)

Enregistrer le bloc.

---

#### Card 2 : GitHub

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Card**

**Champs à remplir** :

**icon** (obligatoire) :
- Upload du fichier SVG `github.svg`

**title** (obligatoire) :
```
GitHub
```

**description** (obligatoire) :
```
<p>Find the source code, report a bug or contribute to the project on GitHub.</p>
```

**link** (optionnel) :
```
github.com/blackcubeio
```

**linkUrl** (optionnel) :
```
https://github.com/blackcubeio
```

Enregistrer le bloc.

---

#### Card 3 : Documentation

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **04. Card**

**Champs à remplir** :

**icon** (obligatoire) :
- Upload du fichier SVG `book-open.svg`

**title** (obligatoire) :
```
Documentation
```

**description** (obligatoire) :
```
<p>Complete guides, tutorials and API references to master Blackcube CMS.</p>
```

**link** (optionnel) :
```
Explore documentation >
```

**linkRoute** (obligatoire) :
- Sélectionner dans le dropdown → **Rubriques** → **Guide** (EN)

Enregistrer le bloc.

---

### Étape 4 : Remplir l'Info Box

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **05. Info**

**Champs à remplir** :

**type** (obligatoire) :
- Sélectionner `info` dans le dropdown

**title** (obligatoire) :
```
Response time
```

**content** (obligatoire) :
```
<p>We strive to respond within 48 business hours.</p>
```

Enregistrer le bloc.

---

### Étape 5 : Remplir le Bloc Contact

**Section Contenus** → Bouton **Ajouter un bloc** → Sélectionner **07. Contact**

**Champs à remplir** :

**title** (obligatoire) :
```
Send us a message
```

**successTitle** (obligatoire) :
```
Message sent!
```

**success** (obligatoire) :
```
<p>Thank you for your message. We'll get back to you as soon as possible.</p>
```

Enregistrer le bloc.

---

### Étape 6 : Configuration URL

**Onglet URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `en/contact`
4. Enregistrer

URL publique finale : `/en/contact`

---

### Étape 7 : Configuration Sitemap

**Onglet Sitemap** :

1. **Actif** : Coché
2. **Fréquence** : `monthly`
3. **Priorité** : `0.5`
4. Enregistrer

---

### Étape 8 : Configuration SEO

**Onglet SEO** :

1. **Actif** : Coché
2. **Titre** : `Contact - Blackcube CMS`
3. **Description** : `Contact the Blackcube team. Questions, suggestions or technical support, we're here to help.`
4. Enregistrer

---

## Lier les Contenus FR/EN (Suite)

Maintenant que les deux Composites Contact existent, finaliser la liaison.

### Mettre à jour le Hero FR

1. Éditer le Composite **Contact** (FR)
2. Éditer le bloc **Hero (H1)**
3. Champ **lang** : Sélectionner dans le dropdown → **Articles** → **Contact** (EN)
4. Enregistrer

### Mettre à jour le Hero EN

1. Éditer le Composite **Contact** (EN)
2. Éditer le bloc **Hero (H1)**
3. Champ **lang** : Sélectionner dans le dropdown → **Articles** → **Contact** (FR)
4. Enregistrer

---

## Validation

Vérifier dans **Gestion → Articles** :

**Composites créés** :
- Contact (FR) - Type: Contact - Langue: Français
- Contact (EN) - Type: Contact - Langue: English

**Blocs par Composite** :
- 1 Hero (H1)
- 3 Cards
- 1 Info
- 1 Contact
- Total : 6 blocs par page

**Configuration** :
- URLs configurées : `/fr/contact` et `/en/contact`
- Sitemap activé avec fréquence mensuelle
- SEO configuré avec titre et description
- Liaison FR ↔ EN fonctionnelle via champ `lang`
