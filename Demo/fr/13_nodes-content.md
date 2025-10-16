# Préparation des Rubriques
## Guide de Développement - Site Documentation Blackcube CMS

**Prérequis** : Configuration Back-Office

---

## Objectif

Créer l'arborescence complète du site dans le back-office : Node racine, Nodes FR/EN avec leurs Heros, Nodes containers (Guide, À propos). Cette étape structure le site et remplit les premiers contenus.

---

## Configurer le Node Racine

**Rôle** : Point d'entrée du site, détecte la langue du navigateur et redirige vers /fr ou /en.

**Accès** : Menu latéral → **Gestion → Rubriques**

> **Info**
> Le Node Racine a été créé automatiquement lors de l'exécution de la commande `php yii bc:init`. Il existe déjà dans l'arborescence, il suffit de le configurer.

### 1. Modifier le Node

1. Dans la liste des rubriques, cliquer sur le crayon pour éditer le Node Racine existant
2. Modifier les champs :
   - **Actif** : Coché
   - **Type** : `Redirect Lang`
   - **Langue** : `Français`
   - **Nom** : `Root` (si pas déjà défini)
3. Enregistrer

### 2. Configuration URL

Après enregistrement, cliquer sur l'onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : *Laisser vide*
4. Enregistrer

URL publique finale : `/`

> **Info**
> Ce Node ne contient aucun bloc car le Type `Redirect Lang` n'autorise aucun BlocType. Son unique rôle est de rediriger. Pas besoin de configurer Sitemap ou SEO car ce Node fait une redirection immédiate.

---

## Créer les Nodes Home FR/EN

### Node Home FR

**Rôle** : Page d'accueil française avec Hero, Features et CTA.

#### 1. Création du Node

1. Bouton **Ajouter une rubrique**
2. Remplir les champs :
   - **Actif** : Coché
   - **Type** : `Home`
   - **Langue** : `Français`
   - **Nom** : `FR`
   - **Chemin** : Hazel Path généré automatiquement (non éditable)
   - **Move** : Cocher
   - **Mode** : `Dans`
   - **Target** : Sélectionner **Root**
3. Enregistrer

#### 2. Configuration URL

> **Info**
> Dans Blackcube, le chemin d'URL est toujours **complet et absolu** pour tous les éléments (Nodes, Composites, Categories, Tags). On n'hérite jamais automatiquement du chemin parent.

Onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `fr`
4. Enregistrer

#### 3. Configuration Sitemap

Onglet **Sitemap** :

1. **Actif** : Coché
2. **Fréquence** : `daily`
3. **Priorité** : `1.0`
4. Enregistrer

#### 4. Configuration SEO

Onglet **SEO** :

1. **Actif** : Coché
2. **Titre** : `Blackcube CMS`
3. **Description** : `CMS pour Yii2. Documentation officielle en français.`
4. Enregistrer

URL publique finale : `/fr`

---

### Node Home EN

**Rôle** : Page d'accueil anglaise avec Hero, Features et CTA.

#### 1. Création du Node

1. Bouton **Ajouter une rubrique**
2. Remplir les champs :
   - **Actif** : Coché
   - **Type** : `Home`
   - **Langue** : `English`
   - **Nom** : `EN`
   - **Chemin** : Hazel Path généré automatiquement (non éditable)
   - **Move** : Cocher
   - **Mode** : `Dans`
   - **Target** : Sélectionner **Root**
3. Enregistrer

#### 2. Configuration URL

Onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `en`
4. Enregistrer

#### 3. Configuration Sitemap

Onglet **Sitemap** :

1. **Actif** : Coché
2. **Fréquence** : `daily`
3. **Priorité** : `1.0`
4. Enregistrer

#### 4. Configuration SEO

Onglet **SEO** :

1. **Actif** : Coché
2. **Titre** : `Blackcube CMS`
3. **Description** : `CMS for Yii2. Official documentation in English.`
4. Enregistrer

URL publique finale : `/en`

---

## Remplir les Heros Home FR/EN

### Ajouter le Hero FR

1. Éditer le Node **FR** (cliquer sur le crayon dans la liste des rubriques)
2. En bas de la page, section **Contenus** : Sélectionner **01. Hero (H1)** dans le dropdown → Cliquer sur le bouton **+**
3. Le bloc Hero s'affiche avec ses champs. Remplir :

**lang** : *Laisser vide* (à remplir après création Hero EN)

**title** :
```
Blackcube CMS
```

**description** :
```
CMS flexible et performant pour Yii2. Créez des sites sur mesure avec une architecture modulaire et un système de blocs puissant.
```

**image** : Uploader une image de fond PNG/JPG (optionnel)

**ctaTitle** :
```
Documentation
```

**ctaTarget** : Sélectionner dans le dropdown → **Rubriques** → **Guide FR**

**ctaSecondaryTitle** :
```
Github
```

**ctaSecondaryTarget** : Saisir la route :
```
redirect/github
```

4. Enregistrer le Node

---

### Ajouter le Hero EN

1. Éditer le Node **EN**
2. En bas de la page, section **Contenus** : Sélectionner **01. Hero (H1)** dans le dropdown → Cliquer sur le bouton **+**
3. Le bloc Hero s'affiche avec ses champs. Remplir :

**lang** : *Laisser vide* (à remplir après)

**title** :
```
Blackcube CMS
```

**description** :
```
Flexible and performant CMS for Yii2. Build custom websites with a modular architecture and powerful block system.
```

**image** : Uploader la même image que FR (ou variante EN)

**ctaTitle** :
```
Documentation
```

**ctaTarget** : Sélectionner dans le dropdown → **Rubriques** → **Guide EN**

**ctaSecondaryTitle** :
```
Github
```

**ctaSecondaryTarget** : Saisir la route :
```
redirect/github
```

4. Enregistrer le Node

---

## Lier les Contenus FR/EN

**Objectif** : Permettre au language switcher de fonctionner en liant les Heros FR et EN via le champ `lang`.

### Lier Hero FR → Hero EN

1. Éditer le Node **FR**
2. Dans le bloc **Hero (H1)** déjà présent, modifier le champ **lang** : Sélectionner dans le dropdown → **Rubriques** → **EN**
3. Enregistrer le Node

### Lier Hero EN → Hero FR

1. Éditer le Node **EN**
2. Dans le bloc **Hero (H1)** déjà présent, modifier le champ **lang** : Sélectionner dans le dropdown → **Rubriques** → **FR**
3. Enregistrer le Node

> **Info**
> Le champ `lang` permet au `HrefLangBehavior` de générer automatiquement les balises SEO `<link rel="alternate" hreflang="...">` et au language switcher de fonctionner.

---

## Créer les Nodes Containers

### Node Guide FR

**Rôle** : Container pour les articles de documentation française. Redirige vers le premier article actif.

#### 1. Création du Node

1. Bouton **Ajouter une rubrique**
2. Remplir les champs :
   - **Actif** : Coché
   - **Type** : `Redirect First Composite`
   - **Langue** : `Français`
   - **Nom** : `Guide`
   - **Chemin** : Hazel Path généré automatiquement (non éditable)
   - **Move** : Cocher
   - **Mode** : `Dans`
   - **Target** : Sélectionner **FR**
3. Enregistrer

#### 2. Configuration URL

Onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `fr/guide`
4. Enregistrer

URL publique finale : `/fr/guide` → redirigera vers `/fr/guide/premier-article`

> **Info**
> Ce Node fait une redirection vers le premier article actif. Pas besoin de configurer Sitemap ou SEO car ce Node n'affiche jamais de contenu.

---

### Node Guide EN

#### 1. Création du Node

1. Bouton **Ajouter une rubrique**
2. Remplir les champs :
   - **Actif** : Coché
   - **Type** : `Redirect First Composite`
   - **Langue** : `English`
   - **Nom** : `Guide`
   - **Chemin** : Hazel Path généré automatiquement (non éditable)
   - **Move** : Cocher
   - **Mode** : `Dans`
   - **Target** : Sélectionner **EN**
3. Enregistrer

#### 2. Configuration URL

Onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `en/guide`
4. Enregistrer

URL publique finale : `/en/guide` → redirigera vers `/en/guide/first-article`

> **Info**
> Ce Node fait une redirection vers le premier article actif. Pas besoin de configurer Sitemap ou SEO car ce Node n'affiche jamais de contenu.

---

### Node À propos FR

**Rôle** : Container pour les pages "À propos". Redirige vers le premier article actif.

#### 1. Création du Node

1. Bouton **Ajouter une rubrique**
2. Remplir les champs :
   - **Actif** : Coché
   - **Type** : `Redirect First Composite`
   - **Langue** : `Français`
   - **Nom** : `À propos`
   - **Chemin** : Hazel Path généré automatiquement (non éditable)
   - **Move** : Cocher
   - **Mode** : `Dans`
   - **Target** : Sélectionner **FR**
3. Enregistrer

#### 2. Configuration URL

Onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `fr/a-propos`
4. Enregistrer

URL publique finale : `/fr/a-propos` → redirigera vers `/fr/a-propos/pourquoi-blackcube`

> **Info**
> Ce Node fait une redirection vers le premier article actif. Pas besoin de configurer Sitemap ou SEO car ce Node n'affiche jamais de contenu.

---

### Node About EN

#### 1. Création du Node

1. Bouton **Ajouter une rubrique**
2. Remplir les champs :
   - **Actif** : Coché
   - **Type** : `Redirect First Composite`
   - **Langue** : `English`
   - **Nom** : `About`
   - **Chemin** : Hazel Path généré automatiquement (non éditable)
   - **Move** : Cocher
   - **Mode** : `Dans`
   - **Target** : Sélectionner **EN**
3. Enregistrer

#### 2. Configuration URL

Onglet **URL** :

1. **Actif** : Coché
2. **Hôte** : `*`
3. **Chemin** : `en/about`
4. Enregistrer

URL publique finale : `/en/about` → redirigera vers `/en/about/why-blackcube`

> **Info**
> Ce Node fait une redirection vers le premier article actif. Pas besoin de configurer Sitemap ou SEO car ce Node n'affiche jamais de contenu.

---

## Validation

Vérifier l'arborescence complète dans **Gestion → Rubriques**.

**Structure attendue** :

```
Root (/)
├── FR (/fr)
│   ├── Guide (/fr/guide)
│   └── À propos (/fr/a-propos)
└── EN (/en)
    ├── Guide (/en/guide)
    └── About (/en/about)
```

**Vérifications** :

- 7 Nodes créés : 1 racine + 2 home + 4 containers
- 2 Heros remplis (FR + EN) avec tous les champs obligatoires
- Liaison FR ↔ EN fonctionnelle via champ `lang`
- URLs configurées pour tous les Nodes
- Sitemap activé uniquement pour FR et EN (pas pour les redirections)
- SEO configuré uniquement pour FR et EN (pas pour les redirections)

**Test front-end** :

Accéder au site en mode prévisualisation (icône œil dans le header du back-office) :

- Accéder à `/` → doit rediriger vers `/fr` ou `/en` selon langue navigateur
- Accéder à `/fr` → doit afficher la page d'accueil française
- Accéder à `/en` → doit afficher la page d'accueil anglaise
- Language switcher fonctionne (FR ↔ EN)
- Accéder à `/fr/guide` ou `/fr/a-propos` → erreur 404 (normal, pas encore d'articles créés dans ces sections)