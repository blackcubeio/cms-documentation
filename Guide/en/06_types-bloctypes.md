# Types and BlocTypes

Blackcube's essential concepts: Types and BlocTypes

Blackcube is based on a few key concepts that, once mastered, allow building any type of site. The most important: understanding well the Type vs BlocType notion, the separation between "how to display" and "what to enter".

This fundamental separation allows the developer to keep total control of the front-end while offering maximum flexibility to contributors. The Type defines display logic via a Yii2 controller, while the BlocType defines the structure of entered data via JSON Schema. Both are independent but connected: a Type authorizes certain BlocTypes, thus allowing precise control over which content types can use which fields.

> **Info**
>
> The developer defines the structure: creates Types (controllers/actions), creates BlocTypes (available fields), links Types and BlocTypes, then codes views and business logic.

## Type vs BlocType: The Essential Distinction

Common blocking point for developers coming from WordPress or Drupal: understanding this distinction well is essential.

### Type: "How to Display?"

A Type defines how content will be rendered in the front-end.

Concretely, a Type is:

1. A Yii2 controller/action (e.g., PageController::actionView())
2. Constraints on allowed entities (Node, Composite, Tag, Category)
3. A list of allowed BlocTypes for this Type

Example: Type "page-standard"

Configuration in backoffice:

```text
Name: page-standard
Route: page/view
Usable for: Node only
Allowed BlocTypes: heading, text-editor, image-gallery
```

### BlocType: "What to Enter?"

A BlocType defines which fields are available for content entry.

Concretely, a BlocType is:

1. A JSON Schema describing data structure
2. Automatic validation of entered data
3. Properties accessible as a Yii2 ActiveRecord

Example: BlocType "text-block"

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Title"
    },
    "content": {
      "type": "string",
      "format": "wysiwyg",
      "title": "Content"
    }
  },
  "required": ["title", "content"]
}
```

### The Type ↔ BlocType Relation

A Type authorizes certain BlocTypes. This relation gives flexibility while keeping control.

Visual representation:

```text
Type "page-standard"
├── Authorizes: text-block
├── Authorizes: image-gallery
└── Authorizes: video-embed

Type "news"
├── Authorizes: text-block
└── Authorizes: image-gallery
(no video-embed for news)
```

Concrete result:

1. The contributor who creates a "page-standard" can add videos
2. The contributor who creates "news" CANNOT add videos
3. The same text-block BlocType is reused in both Types

### Analogy

Restaurant analogy:

1. **Type** = Type of dish (Starter, Main, Dessert)
2. **BlocType** = Available ingredients (Tomato, Chicken, Chocolate)
3. **Relation** = Which ingredients go in which type of dish

You don't put chocolate in a savory starter, even if chocolate exists in your kitchen.

## Create Your First Types

> **Important**
>
> Create at least one Type before being able to create content. Without Type, impossible to create a page, article or any other content.

Types are Blackcube's foundation.

A Type defines:

1. The controller/action that will render content
2. Available BlocTypes for this content type
3. Whether the Type can be used for Nodes, Composites, Tags or Categories

Example: Type "Standard Page"

In the backoffice:

1. Go to Settings → Types
2. Create a new Type:
   - Name: page-standard
   - Route: page/view (corresponds to PageController::actionView())
   - Node authorized: ✓ Yes
   - Composite authorized: ✗ No
   - Tag authorized: ✗ No
   - Category authorized: ✗ No
3. Save

Then create the corresponding controller:

```php
<?php
namespace app\controllers;

use blackcube\core\components\BlackcubeController;

class PageController extends BlackcubeController
{
    public function actionView()
    {
        $node = $this->element; // Automatically injected
        
        return $this->render('view', [
            'node' => $node,
        ]);
    }
}
```

## Create Your First BlocTypes

BlocTypes define the fields your contributors can fill.

Example: BlocType "Text with Title"

In the backoffice:

1. Go to Settings → BlocTypes
2. Create a new BlocType:
   - Name: text-block
   - Template: JSON Schema defining fields
   - View (optional): View name for rendering

JSON Schema template:

```json
{
    "type": "object",
    "properties": {
        "title": {
            "type": "string",
            "title": "Title"
        },
        "content": {
            "type": "string",
            "format": "wysiwyg",
            "options": {
                "theme": "snow",
                "modules": {
                    "toolbar": [
                        [
                            "bold",
                            "italic",
                            "underline"
                        ],
                        [
                            {
                                "list": "bullet"
                            }
                        ],
                        [
                            "link"
                        ]
                    ]
                },
                "formats": [
                    "bold",
                    "italic",
                    "link",
                    "underline",
                    "list"
                ]
            },
            "title": "Content",
            "description": "Content block"
        }
    },
    "required": ["title", "content"]
}
```

Template explanation:

1. `type: "object"` = The BlocType contains multiple properties
2. `properties` = Definition of each field
3. `title` = Required simple text field
4. `content` = WYSIWYG editor with formatting options
5. `required` = List of required fields

Save.

## Link Types and BlocTypes

Once your Types and BlocTypes are created, define which BlocTypes are available for each Type.

In the backoffice:

1. Go to Settings → Types
2. Edit the page-standard Type
3. In the Allowed BlocTypes section, check text-block
4. Save

> **Info**
>
> You can also do the opposite from Settings → BlocTypes by editing a BlocType and checking the allowed Types.

You can now create content.

## Key Points to Remember

1. **Type** = How to display (controller/action)
2. **BlocType** = What to enter (fields/data)
3. A Type authorizes certain BlocTypes
4. This separation allows flexibility and control
5. Without Type, no content possible
6. BlocTypes are reusable between different Types
7. Creating Types and BlocTypes is done in the backoffice
8. Controller code must be created manually