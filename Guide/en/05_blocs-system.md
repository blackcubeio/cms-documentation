# Bloc System

Modular page composition with total rendering control

Blackcube's bloc system allows composing pages flexibly while maintaining total control over rendering. Each content (Node, Composite, Tag, Category) can contain multiple blocs of different types defined by the developer.

Concretely, a bloc is an instance of a BlocType: the BlocType defines the data structure via JSON Schema, and the bloc contains the actual data. This architecture allows great flexibility while maintaining consistency and automatic content validation. Each Type defines which BlocTypes are allowed, thus offering precise control over page composition.

## What is a Bloc?

A **bloc** is a concrete instance of a **BlocType**. It's a piece of structured content that can be added to an element.

### Building Analogy

If we compare to construction:

- **BlocType** = The blueprint of a module (e.g., "standard 120x80 window")
- **Bloc** = A window installed in a specific house

Consequences:

- A bloc belongs to **a single content**
- Creating 10 pages with the "heading" BlocType = creating 10 different blocs
- Modifying a bloc affects **only that content**
- **No shared blocs** between contents

## Storage Architecture

### Involved Tables

```sql
-- Blocs table (actual content)
CREATE TABLE blocs (
    id          BIGINT AUTO_INCREMENT PRIMARY KEY,
    blocTypeId  BIGINT NOT NULL,              -- Reference to BlocType
    active      TINYINT(1) DEFAULT 0,         -- Bloc validity
    data        BLOB,                         -- JSON containing data
    dateCreate  DATETIME NOT NULL,
    dateUpdate  DATETIME NULL,
    FOREIGN KEY (blocTypeId) REFERENCES blocTypes(id)
);

-- Link tables (many-to-many)
CREATE TABLE nodes_blocs (
    nodeId      BIGINT NOT NULL,
    blocId      BIGINT NOT NULL,
    `order`     INT NOT NULL,                 -- Display order
    dateCreate  DATETIME NOT NULL,
    dateUpdate  DATETIME NULL,
    PRIMARY KEY (nodeId, blocId),
    FOREIGN KEY (nodeId) REFERENCES nodes(id),
    FOREIGN KEY (blocId) REFERENCES blocs(id)
);

-- Similar tables for composites_blocs, tags_blocs, categories_blocs
```

### Why This Architecture?

Separation of responsibilities:

1. **Blocs table**: Stores actual content (independent of element)
2. **Link tables**: Associate blocs with contents and manage order

Advantages:

- Homogeneous contents
- Optimized queries
- Simple order management
- Facilitated cascade deletion

## Bloc Lifecycle

### Creation in the Backoffice

> **Warning**
> 
> The following code is given for illustration purposes.

Steps:

1. The contributor edits a Node (for example)
2. Selects a BlocType from the dropdown list
3. Clicks on **"+"** to add the bloc
4. The form displays with fields defined by JSON Schema
5. The contributor fills in the fields
6. Clicks on **"Save"**

What happens in the database:

```sql
-- 1. Insertion in blocs
INSERT INTO blocs (blocTypeId, active, data)
VALUES (5, 0, '{}');  -- active = 0 because not yet valid

-- 2. Link with node
INSERT INTO nodes_blocs (nodeId, blocId, `order`)
VALUES (42, 123, 1);  -- order = 1 (first bloc)
```

### Automatic Validation

Blackcube validates the bloc according to JSON Schema:

If valid:

```sql
UPDATE blocs SET active = 1, data = '{"title":"My title", ...}'
WHERE id = 123;
```

If invalid:

- The bloc remains active = 0
- Errors are displayed in the backoffice

The contributor must correct before being able to save.

> **Important**
> 
> An invalid bloc (`active = 0`) **never** appears on the public site, even if the parent content is active.

### Display on Site

In your controller:

```php
public function actionView()
{
    $node = $this->element;
    
    // Blocs are automatically:
    // - Filtered (active = 1 only)
    // - Sorted (by order ASC)
    // - Loaded with their BlocType
    foreach ($node->blocs as $bloc) {
        // $bloc is an ElasticModel with JSON Schema properties
        echo $bloc->title;
        echo $bloc->content;
    }
}
```

Generated SQL query:

```sql
SELECT b.*, bt.* 
FROM blocs b
INNER JOIN nodes_blocs nb ON nb.blocId = b.id
INNER JOIN blocTypes bt ON bt.id = b.blocTypeId
WHERE nb.nodeId = 42
  AND b.active = 1
ORDER BY nb.order ASC;
```

One query. All blocs. Already sorted.

## Order Management

### In the Backoffice

Blocs display in the order they were created. The interface allows reorganizing blocs via dedicated controls.

Operation:

Initial order:

- 1. Heading Bloc
- 2. Text Bloc
- 3. Image Bloc

After reorganization:

- 1. Heading Bloc
- 2. Image Bloc ← Moves up
- 3. Text Bloc ← Moves down

### Automatic Numbering

At creation:

```sql
-- New bloc added after 3 existing blocs
INSERT INTO nodes_blocs (nodeId, blocId, `order`)
VALUES (42, 124, 4);  -- order = 4 automatically
```

After deletion and save:

Initial order:

- 1. Bloc A
- 2. Bloc B
- 3. Bloc C

Deletion of Bloc B:

- 1. Bloc A
- 3. Bloc C ← Temporarily

When saving content:

- 1. Bloc A ← Recalculated
- 2. Bloc C ← Recalculated

Why recalculate?

- Always consistent order: 1, 2, 3, 4... (no gaps)
- Facilitates display and debugging
- Avoids numbering issues

> **Important**
> 
> Recalculation happens automatically when saving content in the backoffice. You have nothing to manage.

## Type → BlocType Relation

### Configuration in Type

Each Type defines which BlocTypes are allowed.

Example:

Type "page-standard"

Allowed BlocTypes:

- ☑ heading
- ☑ text-block
- ☑ image-gallery
- ☐ video-embed
- ☐ contact-form

Result: When a contributor creates a Node with this Type, they can only add the **checked** BlocTypes.

### Link Table

```sql
CREATE TABLE types_blocTypes (
    typeId      BIGINT NOT NULL,
    blocTypeId  BIGINT NOT NULL,
    allowed     TINYINT(1) DEFAULT 1,         -- 1 = allowed, 0 = forbidden
    dateCreate  DATETIME NOT NULL,
    dateUpdate  DATETIME NULL,
    PRIMARY KEY (typeId, blocTypeId),
    FOREIGN KEY (typeId) REFERENCES types(id),
    FOREIGN KEY (blocTypeId) REFERENCES blocTypes(id)
);
```

Query to get allowed BlocTypes:

```sql
SELECT bt.*
FROM blocTypes bt
INNER JOIN types_blocTypes tbt ON tbt.blocTypeId = bt.id
WHERE tbt.typeId = 1
  AND tbt.allowed = 1;
```

### Why This Constraint?

Advantages:

1. **Design control**: Avoid "Christmas trees" where anything goes
2. **Consistency**: A "News" page has different blocs than a "Contact" page
3. **Simplification**: The contributor sees only relevant blocs
4. **Maintenance**: Disabling a BlocType for a Type doesn't affect others

Concrete example:

```
Type "landing-page"
→ BlocTypes: hero, features, testimonials, cta

Type "blog-post"
→ BlocTypes: heading, text, image, quote, code

Type "product-page"
→ BlocTypes: product-info, gallery, specs, related-products
```

Each Type has blocs adapted to its use.

## Manipulation in Code

### Retrieve Blocs

Automatic via relation:

```php
$node = $this->element;

// Relation defined in Node::getBlocs()
foreach ($node->blocs as $bloc) {
    // Access to ElasticModel properties
    echo $bloc->title;
    echo $bloc->content;
    echo $bloc->image;
}
```

Blocs are:

- Filtered (`active = 1`)
- Sorted (`order ASC`)
- Loaded with their BlocType
- Ready to use

### Filter by BlocType

```php
// Retrieve only blocs of a specific type
foreach ($node->blocs as $bloc) {
    if ($bloc->blocType->name === 'heading') {
        // Specific processing for headings
    }
}
```

## Bloc Rendering

### Approach 1: Simple Rendering

```php
// views/page/view.php
<?php 
use blackcube\core\helpers\Html;
use blackcube\core\helpers\Quill;
?>

<?php foreach ($node->blocs as $bloc): ?>
    <div class="bloc bloc-<?= $bloc->blocType->name ?>">
        <h2><?= Html::encode($bloc->title) ?></h2>
        <div><?= Quill::cleanHtml($bloc->content) ?></div>
    </div>
<?php endforeach; ?>
```

### Approach 2: Partial Views

```php
// views/page/view.php
<?php foreach ($node->blocs as $bloc): ?>
    <?= $this->render('_bloc-' . $bloc->blocType->name, [
        'bloc' => $bloc
    ]) ?>
<?php endforeach; ?>
```

Advantage: Organized code, easy maintenance.

## Best Practices

### To Do

Name BlocTypes clearly:

- `heading`, `text-block`, `image-gallery`, `cta`, `testimonial`
- Avoid: `bloc1`, `bloc2`, `bloc3`

Reuse BlocTypes:

- A single `heading` reused in multiple Types
- Avoid: `heading-page`, `heading-article`, `heading-landing`

### To Avoid

Too complex blocs:

- Avoid: A bloc with 30 fields
- Prefer: Multiple simple and reusable blocs

## Conclusion

### The Bloc System in Summary

1. **Modular**: Compose pages bloc by bloc
2. **Flexible**: Each Type defines its allowed blocs
3. **Isolated**: One bloc = one content (no sharing)
4. **Ordered**: Simple order management
5. **Validated**: Automatic validation via JSON Schema
6. **Filtered**: Only valid blocs are displayed

### The Complete Trio

Type + BlocType + Bloc = Customized Page

- **Type**: Defines controller/action and allowed blocs
- **BlocType**: Defines data structure (JSON Schema)
- **Bloc**: Concrete instance with actual data

The developer keeps total control. The contributor creates rich content without technical constraints.