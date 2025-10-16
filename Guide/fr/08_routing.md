# Routing intelligent

Mapping automatique d'URLs vers des controllers personnalisés

Le système de routing de Blackcube est l'une des fonctionnalités qui rend le CMS si puissant et flexible. Il permet de mapper automatiquement n'importe quelle URL vers un controller/action personnalisé, tout en injectant automatiquement le contenu.

Concrètement, chaque contenu définit via son Type quel controller/action doit être exécuté pour son affichage. Blackcube intercepte toutes les requêtes, récupère l'élément correspondant à l'URL, et l'injecte automatiquement dans le controller. Le développeur garde un contrôle total du rendu sans avoir à gérer manuellement la récupération du contenu.

## Le problème du routing classique

### Approche CMS traditionnels

Dans la plupart des CMS, le routing est rigide :

```
/blog/mon-article → BlogController::actionView($slug)
/page/a-propos → PageController::actionView($slug)
/produit/123 → ProductController::actionView($id)
```

**Problèmes :**

- Structure d'URL imposée par le CMS
- Un type de contenu = une structure d'URL
- Difficile de personnaliser le rendu selon le contexte
- Le développeur doit gérer manuellement la récupération du contenu

### La solution Blackcube

Avec Blackcube, **chaque contenu définit son propre controller/action** via son Type.

**Avantages :**

- URLs totalement libres : `/fr/services`, `/en/about`, `/produits/logiciels`
- Un même Type peut gérer des contenus différents
- Contrôle total du rendu via des controllers personnalisés
- Injection automatique du contenu dans `$this->element`

## Le cycle de vie d'une requête

> **Attention**
> Les exemples de code suivants sont simplifiés pour la pédagogie. Ils illustrent les mécanismes internes de Blackcube mais ne représentent pas du code à copier-coller.

Comprendre comment Blackcube traite une requête est essentiel pour maîtriser le CMS.

**L'utilisateur visite : `https://example.com/fr/services`**

### Interception et recherche

Blackcube intercepte la requête et cherche un slug correspondant au chemin `/fr/services`.

**Deux cas possibles :**

- **Slug trouvé** : Blackcube continue le traitement
- **Slug non trouvé** : Passe au routing Yii2 classique

### Filtrage de l'élément

Blackcube vérifie si l'élément associé au slug est accessible selon ces règles :

**Pour Node et Composite :**

- Doit être actif (`active = 1`)
- Date de début respectée (si définie)
- Date de fin respectée (si définie)

**Pour Tag :**

- Sa catégorie parente doit être active

**Pour Category :**

- Doit contenir au moins un tag actif

**Résultat :** Si l'élément n'est pas accessible, Blackcube passe la main au routing Yii2 (généralement une 404).

> **Prévisualisation**
> Les administrateurs connectés peuvent voir les contenus inactifs ou hors dates via le mode prévisualisation. Ce système est géré automatiquement sans code dans vos controllers.

### Exécution du controller

Blackcube exécute le controller/action défini dans le Type de l'élément.

### Injection automatique

**C'est ici que la magie opère :**

Blackcube injecte automatiquement l'élément dans le controller via `$this->element` :

```php
namespace app\controllers;

use blackcube\core\components\BlackcubeController;

class PageController extends BlackcubeController
{
    public function actionView()
    {
        // L'élément est DÉJÀ disponible !
        $node = $this->element;
        
        return $this->render('view', [
            'node' => $node
        ]);
    }
}
```

**Pas besoin de :**

- Récupérer le slug
- Chercher le node
- Vérifier les permissions
- Gérer les erreurs 404

**Tout est fait automatiquement.**

### Rendu de la vue

Le controller rend la vue avec le contenu :

```php
// views/page/view.php
<?php foreach ($node->blocs as $bloc): ?>
    <h2><?php echo $bloc->title; ?></h2>
    <div><?php echo $bloc->content; ?></div>
<?php endforeach; ?>
```

**Résultat : 1 URL → 1 élément → 1 controller personnalisé → 1 rendu sur mesure**

## Configuration des Types

Pour que le routing fonctionne, vous devez créer des Types avec des routes valides.

### Dans le backoffice

1. Allez dans **Paramètres → Types**
2. Créez ou Éditez un Type
3. Sélectionnez la **Route** : `controller/action`
4. Cochez les types de contenu autorisés (Node, Composite, Tag, Category)
5. Sauvegardez

**Exemple :**

```
Nom : page-standard
Route : page/view
Node autorisé : ☑ Oui
Composite autorisé : ☐ Non
Tag autorisé : ☐ Non
Category autorisé : ☐ Non
```

## Contraintes importantes

**Le controller DOIT étendre `BlackcubeController` ou implémenter `BlackcubeControllerInterface`.**

Sans cela, le Type **ne peut pas être créé** dans le backoffice. Blackcube valide que le controller est compatible avant de vous laisser sauvegarder.

**Exemple valide :**

```php
namespace app\controllers;

use blackcube\core\components\BlackcubeController;

class PageController extends BlackcubeController
{
    public function actionView()
    {
        $node = $this->element;
        return $this->render('view', ['node' => $node]);
    }
}
```

**Exemple avec interface (si vous devez étendre une autre classe) :**

```php
namespace app\controllers;

use blackcube\core\interfaces\BlackcubeControllerInterface;
use yii\web\Controller;

class PageController extends Controller implements BlackcubeControllerInterface
{
    // Implémenter l'interface
    
    public function actionView()
    {
        // récupération du node en fonction de votre implémentation
        return $this->render('view', ['node' => $node]);
    }
}
```

## Gestion des Slugs

Les slugs sont la clé du routing. Chaque contenu a un slug unique qui définit son URL.

### Slugs avec ou sans host

Dans certains cas, vous voudrez pouvoir utiliser le même BO et le même front pour plusieurs sites.

C'est là que le Host intervient, il permet de définir pour quels sites le CMS doit répondre.

#### Host NULL (multi-domaines)

```
host: NULL
path: services
```

**Fonctionnement :** Ce slug fonctionne sur **tous les domaines** :

- `https://example.com/services`
- `https://example.fr/services`
- `https://www.example.com/services`

#### Host spécifique

```
host: example.fr
path: services
```

**Fonctionnement :** Ce slug fonctionne **uniquement** sur `example.fr` :

- `https://example.fr/services` (valide)
- `https://example.com/services` (slug non trouvé)

### Slugs multilingues

Pour un site multilingue, deux approches :

#### Approche A : Domaines différents

```
-- Slug FR
host: example.fr
path: services

-- Slug EN
host: example.com
path: services
```

**URLs :**

- `https://example.fr/services` → Version française
- `https://example.com/services` → Version anglaise

#### Approche B : Préfixes de langue

```
-- Slug FR
host: NULL
path: fr/services

-- Slug EN
host: NULL
path: en/services
```

**URLs :**

- `https://example.com/fr/services` → Version française
- `https://example.com/en/services` → Version anglaise

**Choisissez l'approche qui correspond à votre stratégie SEO.**

### Unicité des slugs

**Le couple `(host, path)` doit être unique.**

**Exemples valides :**

```
-- OK : Même path, hosts différents
(host: example.fr, path: services)
(host: example.com, path: services)

-- OK : Paths différents, même host
(host: example.fr, path: services)
(host: example.fr, path: produits)
```

**Exemples invalides :**

```
-- ERREUR : Doublon exact
(host: example.fr, path: services)
(host: example.fr, path: services)

-- ERREUR : Doublon avec NULL
(host: NULL, path: services)
(host: NULL, path: services)
```

**Le backoffice empêche automatiquement la création de doublons.**

## Redirections

Blackcube permet de créer des redirections directement via les slugs, sans code.

### Cas d'usage : Migration d'URLs

**Scénario :** Vous changez l'URL d'une page :

- Ancienne URL : `/old-services`
- Nouvelle URL : `/fr/services`

**Solution :** Créer un slug de redirection.

### Création dans le backoffice

1. Allez dans **Gestion → URLs**
2. Cliquez sur **"Créer"**
3. Remplissez :
   - **Hôte** : choisir l'hôte pour lequel répondre la redirection (ou * pour tous)
   - **Chemin** : `old-services`
   - **URL Cible** : `https://example.fr/services`
   - **HTTP Code** : `301` (redirection permanente)
   - **Active** : ☑
4. Sauvegardez

### Codes HTTP supportés

- **301** : Redirection permanente (la plus courante)
- **302** : Redirection temporaire

### Redirections externes

Vous pouvez rediriger vers des URLs externes :

```
Path : partenaire
Target URL : https://www.partenaire.com
HTTP Code : 302
```

**Résultat :** `/partenaire` redirige vers `https://www.partenaire.com`

## Cohabitation avec Yii2

Blackcube cohabite parfaitement avec le routing Yii2 classique.

### Ordre de traitement

**Routing Blackcube (priorité)**

Blackcube intercepte **toutes** les requêtes en premier.

**Fallback routing Yii2**

Si le slug n'est **pas trouvé**, Blackcube passe la main au routing Yii2 standard.

### Exemple concret

**Configuration :**

```php
'urlManager' => [
    'enablePrettyUrl' => true,
    'showScriptName' => false,
    'rules' => [
        'api/users' => 'api/user/index',
        'api/users/<id:\d+>' => 'api/user/view',
    ],
],
```

**Requêtes :**

- `/fr/services` → Blackcube (contenu CMS)
- `/api/users` → Yii2 (API REST)

**Résultat :** Vous pouvez mixer :

- **Blackcube** pour le contenu éditorial (pages, articles, etc.)
- **Yii2** pour les APIs, outils, fonctionnalités techniques

### Bonnes pratiques

**URLs Blackcube :**

- /fr/..., /en/... (contenu multilingue)
- /services, /produits, /actualites (contenu CMS)
- Toute URL "éditoriale"

**URLs Yii2 :**

- /api/... (APIs REST)
- /bo/... (backoffice Blackcube ou custom)
- /search, /login, /logout (fonctionnalités hors blackcube)

**Avantage :** Séparation claire des responsabilités.

## Performance du routing

### Cache automatique

Blackcube met en cache les résultats de recherche de slugs.

**Configuration :**

```php
'modules' => [
    'blackcube' => [
        'cache' => 'cache', // Composant de cache Yii2
    ],
],
```

**Impact :** La recherche de slug est quasi-instantanée après le premier hit.

## Points clés à retenir

1. **URLs libres** : Aucune contrainte de structure imposée par le CMS
2. **Injection automatique** : L'élément est disponible dans `$this->element` sans code supplémentaire
3. **Flexibilité totale** : Chaque contenu définit son propre controller et action via son Type
4. **Cohabitation Yii2** : Le routing CMS coexiste avec le routing Yii2 standard pour les APIs et fonctionnalités techniques
5. **Redirections intégrées** : Migration d'URLs sans code via les slugs

> **Info**
>
> Le routing Blackcube combine Hazel Tree pour l'arborescence, ElasticModel pour la flexibilité des contenus, et l'injection automatique pour simplifier le développement. Cette architecture permet de créer des sites complexes tout en gardant un code simple et maintenable.