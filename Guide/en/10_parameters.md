# Parameters

Centralized configuration in the CMS

Parameters allow storing configuration values in the CMS. The objective: avoid hardcoding bloc type IDs and other values that may change between environments.

Concretely, instead of writing `['blocTypeId' => 1]` in code, we use `Parameters::get('BLOC', 'HERO')` which retrieves the ID from the database. Code becomes explicit, documented, and migrations between environments are facilitated.

## Why Use Parameters

**Problem without parameters:**

```php
// Hardcoded ID = difficult maintenance
$heroBloc = $composite->getBlocs()
    ->andWhere(['blocTypeId' => 1]) // What bloc type?
    ->one();
```

**Solution with parameters:**

```php
$heroBlocTypeId = Parameters::get('BLOC', 'HERO');
$heroBloc = $composite->getBlocs()
    ->andWhere(['blocTypeId' => $heroBlocTypeId])
    ->one();
```

**Advantages:**

- Explicit and documented code
- Centralized configuration
- Facilitates migrations between environments

## Creation in Backoffice

### Interface Access

**Settings** → **Parameters** in the administration menu

### Create a Parameter

1. Click on **Create a parameter**
2. Fill in the fields:
   - **Domain**: Parameter category (e.g., `BLOC`, `CONFIG`)
   - **Name**: Parameter identifier (e.g., `HERO`, `CONTENT`)
   - **Value**: Numeric or text value

> **Warning**
> Create parameters before using them in code. If a parameter doesn't exist, `Parameters::get()` returns `null`.

## How It Works

The **Parameters** helper reads parameters from the database and caches them in memory for the request duration.

**Behavior:**

1. First call: Loads all parameters from `parameter` table
2. Following calls: Returns values from static cache
3. No additional SQL query during HTTP request

> **Info**
> Nothing magic: simple database read with static cache. Parameters are reloaded on each new HTTP request.

## Usage

### Namespace

```php
use app\helpers\Parameters;
```

### Retrieve a Value

```php
Parameters::get(string $domain, string $name): mixed
```

**Example:**

```php
$heroBlocTypeId = Parameters::get('BLOC', 'HERO');
```

### In Queries

**Retrieve a specific bloc:**

```php
$heroBloc = $composite->getBlocs()
    ->andWhere(['blocTypeId' => Parameters::get('BLOC', 'HERO')])
    ->one();

$pageTitle = $heroBloc->title ?? $composite->name;
```

**Multiple bloc types:**

```php
$contentBlocs = $composite->getBlocs()
    ->andWhere(['blocTypeId' => [
        Parameters::get('BLOC', 'CONTENT'),
        Parameters::get('BLOC', 'CODE'),
        Parameters::get('BLOC', 'INFO')
    ]])
    ->all();
```

**With default value:**

```php
$perPage = Parameters::get('CONFIG', 'ARTICLES_PER_PAGE') ?? 10;
```

## Practical Example

Menu generation with title retrieval from Hero blocs.

```php
class Sidebar extends Widget
{
    public $element;

    public function run()
    {
        // Parameter retrieval
        $heroBlocTypeId = Parameters::get('BLOC', 'HERO');

        foreach($nodeList as $childNode) {
            $menu = [
                // Using parameter to retrieve title
                'title' => $childNode->getBlocs()
                        ->andWhere(['blocTypeId' => $heroBlocTypeId])
                        ->one()?->title ?? $childNode->name,
                'route' => $childNode->getRoute(),
                'active' => $childNode->id === $node->id
            ];
            
            $menuList[] = $menu;
        }

        return $this->render('sidebar', ['menuList' => $menuList]);
    }
}
```

## Use Cases

**General configuration:**

```php
$contactEmail = Parameters::get('CONFIG', 'CONTACT_EMAIL');
$siteName = Parameters::get('CONFIG', 'SITE_NAME');
```

**Type IDs:**

```php
$documentTypeId = Parameters::get('TYPE', 'DOCUMENT');
$newsTypeId = Parameters::get('TYPE', 'NEWS');
```

**Feature flags:**

```php
$enableNewsletter = Parameters::get('FEATURE', 'NEWSLETTER_ENABLED');
```

> **Info**
> Domains (`BLOC`, `CONFIG`, `TYPE`, etc.) are free. Organize according to project needs.

## Best Practices

1. **Name clearly**: Use explicit names (`HERO`, `CONTACT_EMAIL`)
2. **Group by domain**: Use consistent domains (`BLOC`, `CONFIG`, `TYPE`)
3. **Initialize at deployment**: Create parameters with a migration

## Key Points to Remember

1. **Centralized configuration**: Values stored in CMS, not hardcoded
2. **Explicit code**: `Parameters::get('BLOC', 'HERO')` is self-documented
3. **Facilitates migrations**: No need to modify code between environments
4. **Automatic cache**: Single database read per HTTP request

> **Info**
>
> Parameters transform opaque code into documented code. `['blocTypeId' => 1]` says nothing, `Parameters::get('BLOC', 'HERO')` is immediately understandable.