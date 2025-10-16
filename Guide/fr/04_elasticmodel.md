# ElasticModel

Quand le JSON devient ActiveRecord pour simplifier le tout et le rendre transparent

ElasticModel permet de stocker des données structurées en JSON tout en les manipulant comme des propriétés PHP classiques. Cette couche technique combine la flexibilité du JSON avec la puissance des ActiveRecord Yii2, offrant ainsi un modèle de données qui peut évoluer sans casser le code existant.

Concrètement, ElasticModel résout le problème classique des CMS : comment gérer des types de contenu différents sans multiplier les tables ni créer des colonnes inutilisées. La solution repose sur trois piliers : stockage JSON flexible, validation via JSON Schema, et manipulation transparente via ActiveRecord.

> **Important**
> 
> ElasticModel répond à des cas d'usage très spécifiques dans Blackcube. Pour des besoins de stockage JSON plus généraux, privilégier une base de données orientée documents.

## Pourquoi ElasticModel ?

### Le problème des CMS traditionnels

Dans un CMS, gérer différents types de contenu pose un problème architectural :

**Option 1 : Une table par type de contenu**
- Table `articles`, table `produits`, table `temoignages`, etc.
- Complexe à maintenir quand on a des dizaines de types
- Code dupliqué pour chaque type

**Option 2 : Colonnes multiples dans une seule table**
- Colonnes `field1`, `field2`... `field50`
- Utilisées ou pas selon le type
- Confusion : quelle colonne pour quel type ?
- Gaspillage d'espace

**Option 3 : Stocker du HTML**
- Flexible mais pas structuré
- Impossible de valider
- Pas de recherche sur les champs
- Risques de sécurité

**Option 4 : ElasticModel**
- Une seule table avec JSON structuré
- Schéma défini par type
- Validation automatique
- Structure maintenue

### La solution : modèle élastique

ElasticModel combine :

1. **JSON** pour stocker les données de manière flexible
2. **JSON Schema** pour valider la structure selon le type
3. **ActiveRecord Yii2** pour manipuler comme du code classique

Chaque type de contenu a sa propre structure, stockée de manière cohérente, sans multiplication de tables ou de colonnes.

### Exemple concret d'évolution

Version 1 - Structure "contact" :

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    }
  },
  "required": ["name", "email"]
}
```

Données existantes :

```json
{
  "name": "Marie Dupont",
  "email": "marie@example.com"
}
```

Version 2 - Ajout d'un téléphone :

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    },
    "phone": {
      "type": "string"
    }
  },
  "required": ["name", "email"]
}
```

Les anciennes données restent valides. Le champ `phone` est optionnel. Le code continue de fonctionner, `$item->phone` retourne `null` pour les anciens enregistrements.

> **Attention**
>
> La rétrocompatibilité ne fonctionne que dans un sens : ajouter des champs optionnels. Si un champ est supprimé du schéma, même si les données existent encore en base, la propriété n'est plus accessible via ElasticModel.
>
> Exemple : schéma v1 avec `username` et `email`, schéma v2 sans `username`. Même si les anciennes données contiennent encore `username` en base, `$bloc->username` lèvera une exception car le champ n'est plus déclaré dans le schéma.

Pour gérer ces cas, vérifier l'existence de l'attribut :

```php
if ($bloc->hasAttribute('username')) {
    echo $bloc->username;
} else {
    // Le champ n'est plus dans le schéma actuel
}
```

### Avantages concrets

1. **Modèle élastique** : La structure peut grossir sans toucher au code
2. **Rétrocompatibilité** : Les anciennes données continuent de fonctionner
3. **Validation automatique** : Chaque sauvegarde vérifie la conformité au schéma
4. **Flexibilité** : Ajouter/supprimer des champs à volonté

## Comment ça marche ?

### Le schéma définit la structure

Un JSON Schema décrit la structure attendue des données :

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    }
  }
}
```

### Les données sont stockées en JSON

Dans la base de données, un champ `data` contient les valeurs réelles :

```
table_example
├─ id : 1
├─ schemaId : 5
└─ data : {"title": "Contact", "email": "contact@example.com"}
```

### Manipulable comme un ActiveRecord

ElasticModel transforme le JSON en propriétés PHP accessibles naturellement :

```php
$item = Item::findOne(1);

// Lecture
echo $item->title;  // "Contact"
echo $item->email;  // "contact@example.com"

// Écriture
$item->title = "Nouveau titre";
$item->email = "nouveau@example.com";
$item->save();
```

Sous le capot :

1. `$item->title` lit dans `data->title`
2. `$item->title = "..."` modifie `data->title`
3. `$item->save()` valide contre le JSON Schema puis sauvegarde

### Requêtes sur les données JSON

ElasticModel permet de requêter directement dans le JSON :

```php
// Rechercher par email
$items = ElasticModel::find()
    ->andWhere(['email' => 'johndoe@example.com'])
    ->all();

// Rechercher par nom
$items = ElasticModel::find()
    ->andWhere(['name' => 'John Doe'])
    ->all();

// Combiner plusieurs conditions
$items = ElasticModel::find()
    ->andWhere(['active' => true])
    ->andWhere(['email' => 'johndoe@example.com'])
    ->all();
```

Contrairement au HTML brut, les données restent requêtables même en JSON.

## JSON Schema : validation automatique

### Qu'est-ce que JSON Schema ?

JSON Schema est un standard pour décrire et valider des structures JSON. Il définit :

- Les propriétés attendues
- Leurs types (string, number, boolean, etc.)
- Les contraintes (requis, format, longueur, etc.)

### Exemple de schéma complet

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "minLength": 3,
      "maxLength": 100
    },
    "email": {
      "type": "string",
      "format": "email"
    },
    "description": {
      "type": "string"
    },
    "active": {
      "type": "boolean",
      "default": false
    }
  },
  "required": ["name", "email"]
}
```

Ce schéma dit :

- `name` : chaîne obligatoire, entre 3 et 100 caractères
- `email` : email obligatoire, validé automatiquement
- `description` : chaîne optionnelle
- `active` : booléen optionnel, false par défaut

### Validation à la sauvegarde

Quand des données sont sauvegardées, Blackcube :

1. Récupère le JSON Schema associé
2. Valide les données saisies
3. Si valide : `active = 1`, les données sont utilisables
4. Si invalide : `active = 0`, les données restent en brouillon

Exemple de validation échouée :

```json
{
  "name": "AB",
  "email": "contact@example.com"
}
```

Erreur : `name` fait 2 caractères, minimum requis = 3. Les données restent `active = 0` jusqu'à correction.

## Formats spéciaux

### Formats supportés par JSON Schema

Le JSON Schema supporte plusieurs formats de validation :

- `"format": "email"` : Validation d'email
- `"format": "uri"` : URL valide
- `"format": "date"` : Date au format ISO
- `"format": "date-time"` : Date + heure
- `"format": "ipv4"` : Adresse IPv4
- `"format": "ipv6"` : Adresse IPv6

Exemple :

```json
{
  "email": {
    "type": "string",
    "format": "email"
  },
  "website": {
    "type": "string",
    "format": "uri"
  }
}
```

### Format spécifique Blackcube : file

Blackcube ajoute un format `file` pour les fichiers uploadés :

```json
{
  "avatar": {
    "type": "string",
    "format": "file"
  }
}
```

Le fichier est stocké avec un préfixe spécial `@blackcubefs/` :

```json
{
  "avatar": "@blackcubefs/photo-2024.jpg"
}
```

## Cas d'usage

### Quand utiliser ElasticModel ?

Parfait pour :

1. Contenu structuré variable
2. Données qui évoluent fréquemment
3. Structures différentes selon le contexte
4. Validation de formulaires dynamiques

Exemples concrets :

- Contenu éditorial structuré
- Formulaires configurables
- Paramètres d'applications
- Configuration dynamique

### Quand NE PAS utiliser ElasticModel ?

Éviter pour :

1. Données relationnelles complexes
2. Requêtes SQL avancées sur les données
3. Index de recherche dans les données JSON
4. Relations many-to-many dans le JSON

Exemples où SQL classique est préférable :

- Tables d'utilisateurs
- Système de commandes e-commerce
- Relations produits/catégories
- Logs avec recherche avancée

### Limites à connaître

1. **Performance** : JSON moins rapide que colonnes SQL natives pour les requêtes complexes
2. **Index** : Index possible sur certains champs mais pas aussi performant que SQL natif
3. **Relations** : Les foreign keys ne fonctionnent pas dans le JSON

ElasticModel utilise les fonctions JSON de MySQL/MariaDB pour rendre les données requêtables, ce qui est un avantage par rapport au HTML brut.

## Application dans Blackcube

ElasticModel est la couche technique qui permet à Blackcube de gérer du contenu structuré flexible.

Blackcube utilise ElasticModel pour stocker des données structurées dont le schéma peut évoluer. Le modèle s'adapte automatiquement quand la structure grossit.

En pratique :

```php
// Un élément avec son schéma :
{
  "title": { "type": "string" },
  "description": { "type": "string" },
  "email": { "type": "string", "format": "email" }
}

// Manipulation via ElasticModel :
$item->title = "Mon élément";
$item->description = "Description détaillée";
$item->email = "contact@example.com";
$item->save();
```

Le modèle est élastique : ajouter un champ `phone` dans le schéma ne casse pas le code existant. `$item->phone` retourne simplement `null` pour les anciens enregistrements.

## Points clés à retenir

1. **Modèle élastique** : La structure peut grossir sans remettre le code en question
2. **JSON Schema** : Validation automatique de la structure
3. **Rétrocompatibilité** : Les anciennes données continuent de fonctionner
4. **Requêtable** : Les données JSON sont interrogeables comme du Yii2 classique
5. **Transparent** : Le développeur manipule des propriétés PHP, pas du JSON
6. **Utilisé par le système de blocs** : Base technique de Blackcube
7. **Cas spécifiques** : Pour du stockage JSON général, préférer une base de données orientée documents