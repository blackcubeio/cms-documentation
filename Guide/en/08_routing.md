# Smart Routing

Automatic mapping of URLs to custom controllers

Blackcube's routing system is one of the features that makes the CMS so powerful and flexible. It allows automatically mapping any URL to a custom controller/action, while automatically injecting content.

Concretely, each content defines via its Type which controller/action should be executed for its display. Blackcube intercepts all requests, retrieves the element corresponding to the URL, and automatically injects it into the controller. The developer keeps total control of rendering without having to manually manage content retrieval.

## The Problem with Classic Routing

### Traditional CMS Approach

In most CMS, routing is rigid:

```
/blog/my-article → BlogController::actionView($slug)
/page/about → PageController::actionView($slug)
/product/123 → ProductController::actionView($id)
```

**Problems:**

- URL structure imposed by CMS
- One content type = one URL structure
- Difficult to customize rendering according to context
- Developer must manually manage content retrieval

### The Blackcube Solution

With Blackcube, **each content defines its own controller/action** via its Type.

**Advantages:**

- Totally free URLs: `/en/services`, `/en/about`, `/products/software`
- Same Type can handle different content
- Total rendering control via custom controllers
- Automatic content injection in `$this->element`

## Request Lifecycle

> **Warning**
> The following code examples are simplified for pedagogy. They illustrate Blackcube's internal mechanisms but do not represent copy-paste code.

Understanding how Blackcube processes a request is essential to master the CMS.

**User visits: `https://example.com/en/services`**

### Interception and Search

Blackcube intercepts the request and searches for a slug corresponding to path `/en/services`.

**Two possible cases:**

- **Slug found**: Blackcube continues processing
- **Slug not found**: Passes to classic Yii2 routing

### Element Filtering

Blackcube verifies if the element associated with the slug is accessible according to these rules:

**For Node and Composite:**

- Must be active (`active = 1`)
- Start date respected (if defined)
- End date respected (if defined)

**For Tag:**

- Its parent category must be active

**For Category:**

- Must contain at least one active tag

**Result:** If element is not accessible, Blackcube passes control to Yii2 routing (generally a 404).

> **Preview**
> Logged-in administrators can see inactive content or content outside dates via preview mode. This system is managed automatically without code in your controllers.

### Controller Execution

Blackcube executes the controller/action defined in the element's Type.

### Automatic Injection

**This is where the magic happens:**

Blackcube automatically injects the element into the controller via `$this->element`:

```php
namespace app\controllers;

use blackcube\core\components\BlackcubeController;

class PageController extends BlackcubeController
{
    public function actionView()
    {
        // Element is ALREADY available!
        $node = $this->element;
        
        return $this->render('view', [
            'node' => $node
        ]);
    }
}
```

**No need to:**

- Retrieve the slug
- Search for the node
- Check permissions
- Handle 404 errors

**Everything is done automatically.**

### View Rendering

The controller renders the view with content:

```php
// views/page/view.php
<?php foreach ($node->blocs as $bloc): ?>
    <h2><?php echo $bloc->title; ?></h2>
    <div><?php echo $bloc->content; ?></div>
<?php endforeach; ?>
```

**Result: 1 URL → 1 element → 1 custom controller → 1 custom rendering**

## Type Configuration

For routing to work, you must create Types with valid routes.

### In the Backoffice

1. Go to **Settings → Types**
2. Create or Edit a Type
3. Select the **Route**: `controller/action`
4. Check authorized content types (Node, Composite, Tag, Category)
5. Save

**Example:**

```
Name: page-standard
Route: page/view
Node authorized: ☑ Yes
Composite authorized: ☐ No
Tag authorized: ☐ No
Category authorized: ☐ No
```

## Important Constraints

**The controller MUST extend `BlackcubeController` or implement `BlackcubeControllerInterface`.**

Without this, the Type **cannot be created** in the backoffice. Blackcube validates that the controller is compatible before letting you save.

**Valid example:**

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

**Example with interface (if you must extend another class):**

```php
namespace app\controllers;

use blackcube\core\interfaces\BlackcubeControllerInterface;
use yii\web\Controller;

class PageController extends Controller implements BlackcubeControllerInterface
{
    // Implement interface
    
    public function actionView()
    {
        // node retrieval according to your implementation
        return $this->render('view', ['node' => $node]);
    }
}
```

## Slug Management

Slugs are the routing key. Each content has a unique slug that defines its URL.

### Slugs With or Without Host

In some cases, you'll want to use the same BO and same front for multiple sites.

This is where Host comes in, allowing you to define which sites the CMS should respond to.

#### NULL Host (multi-domain)

```
host: NULL
path: services
```

**Operation:** This slug works on **all domains**:

- `https://example.com/services`
- `https://example.fr/services`
- `https://www.example.com/services`

#### Specific Host

```
host: example.fr
path: services
```

**Operation:** This slug works **only** on `example.fr`:

- `https://example.fr/services` (valid)
- `https://example.com/services` (slug not found)

### Multilingual Slugs

For a multilingual site, two approaches:

#### Approach A: Different Domains

```
-- FR Slug
host: example.fr
path: services

-- EN Slug
host: example.com
path: services
```

**URLs:**

- `https://example.fr/services` → French version
- `https://example.com/services` → English version

#### Approach B: Language Prefixes

```
-- FR Slug
host: NULL
path: fr/services

-- EN Slug
host: NULL
path: en/services
```

**URLs:**

- `https://example.com/fr/services` → French version
- `https://example.com/en/services` → English version

**Choose the approach that matches your SEO strategy.**

### Slug Uniqueness

**The pair `(host, path)` must be unique.**

**Valid examples:**

```
-- OK: Same path, different hosts
(host: example.fr, path: services)
(host: example.com, path: services)

-- OK: Different paths, same host
(host: example.fr, path: services)
(host: example.fr, path: products)
```

**Invalid examples:**

```
-- ERROR: Exact duplicate
(host: example.fr, path: services)
(host: example.fr, path: services)

-- ERROR: Duplicate with NULL
(host: NULL, path: services)
(host: NULL, path: services)
```

**The backoffice automatically prevents duplicate creation.**

## Redirects

Blackcube allows creating redirects directly via slugs, without code.

### Use Case: URL Migration

**Scenario:** You change a page's URL:

- Old URL: `/old-services`
- New URL: `/en/services`

**Solution:** Create a redirect slug.

### Creation in Backoffice

1. Go to **Management → URLs**
2. Click on **"Create"**
3. Fill in:
   - **Host**: choose the host for the redirect response (or * for all)
   - **Path**: `old-services`
   - **Target URL**: `https://example.com/services`
   - **HTTP Code**: `301` (permanent redirect)
   - **Active**: ☑
4. Save

### Supported HTTP Codes

- **301**: Permanent redirect (most common)
- **302**: Temporary redirect

### External Redirects

You can redirect to external URLs:

```
Path: partner
Target URL: https://www.partner.com
HTTP Code: 302
```

**Result:** `/partner` redirects to `https://www.partner.com`

## Coexistence with Yii2

Blackcube coexists perfectly with classic Yii2 routing.

### Processing Order

**Blackcube Routing (priority)**

Blackcube intercepts **all** requests first.

**Yii2 Routing Fallback**

If slug is **not found**, Blackcube passes control to standard Yii2 routing.

### Concrete Example

**Configuration:**

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

**Requests:**

- `/en/services` → Blackcube (CMS content)
- `/api/users` → Yii2 (REST API)

**Result:** You can mix:

- **Blackcube** for editorial content (pages, articles, etc.)
- **Yii2** for APIs, tools, technical features

### Best Practices

**Blackcube URLs:**

- /en/..., /fr/... (multilingual content)
- /services, /products, /news (CMS content)
- Any "editorial" URL

**Yii2 URLs:**

- /api/... (REST APIs)
- /bo/... (Blackcube or custom backoffice)
- /search, /login, /logout (features outside blackcube)

**Advantage:** Clear separation of responsibilities.

## Routing Performance

### Automatic Cache

Blackcube caches slug search results.

**Configuration:**

```php
'modules' => [
    'blackcube' => [
        'cache' => 'cache', // Yii2 cache component
    ],
],
```

**Impact:** Slug search is nearly instantaneous after first hit.

## Key Points to Remember

1. **Free URLs**: No structure constraint imposed by CMS
2. **Automatic injection**: Element available in `$this->element` without additional code
3. **Total flexibility**: Each content defines its own controller and action via its Type
4. **Yii2 coexistence**: CMS routing coexists with standard Yii2 routing for APIs and technical features
5. **Integrated redirects**: URL migration without code via slugs

> **Info**
>
> Blackcube routing combines Hazel Tree for tree structure, ElasticModel for content flexibility, and automatic injection to simplify development. This architecture allows creating complex sites while keeping code simple and maintainable.