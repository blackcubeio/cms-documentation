# Back-Office Configuration
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Contact Page

---

## Objective

Configure the Blackcube back-office by creating the 9 BlocTypes, 5 Types, 3 custom admin templates, 8 parameters, and 4 navigation menus.

---

## Create BlocTypes

**Access**: Sidebar menu → **Settings → Block Types** → Button **Add block type**

> **Warning**
> Note each BlocType's ID after creation. These IDs will be needed to create Parameters.

### 01. Hero (H1)

**Name**: `01. Hero (H1)`  
**View**: `bloc_hero`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "lang": {
      "type": "string",
      "title": "Associated content",
      "description": "Content for the other language"
    },
    "title": {
      "type": "string",
      "title": "H1 title",
      "description": "Hero title"
    },
    "breadcrumbTitle": {
      "type": "string",
      "title": "Breadcrumb title",
      "description": "Title for the breadcrumb, if not set title will be used"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}],
            ["link"]
          ]
        },
        "formats": ["bold", "italic", "link", "underline", "list"]
      },
      "title": "Description",
      "description": "Not too long, it's for the Hero"
    },
    "image": {
      "type": "string",
      "format": "file",
      "fileType": "png,jpg",
      "title": "Image",
      "description": "Image should be 1200x*"
    },
    "ctaTitle": {
      "type": "string",
      "title": "CTA title",
      "description": "Title of the primary CTA"
    },
    "ctaTarget": {
      "type": "string",
      "title": "Target content",
      "description": "Select the content/page which will be the target of the CTA"
    },
    "ctaSecondaryTitle": {
      "type": "string",
      "title": "CTA title",
      "description": "Title of the secondary CTA"
    },
    "ctaSecondaryTarget": {
      "type": "string",
      "title": "Target content",
      "description": "Select the content/page which will be the target of the CTA"
    }
  },
  "required": ["title", "description"]
}
```

---

### 02. Feature (H3)

**Name**: `02. Feature (H3)`  
**View**: `bloc_feature`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "H3 title",
      "description": "Title for the Hero"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}]
          ]
        },
        "formats": ["bold", "italic", "underline", "list"]
      },
      "title": "Description",
      "description": "Not too long, it's for the feature"
    },
    "icon": {
      "type": "string",
      "format": "file",
      "fileType": "svg",
      "title": "Svg icon",
      "description": "Get the icon from Heroicon"
    }
  },
  "required": ["icon", "title", "description"]
}
```

---

### 03. Title (H2 - H4)

**Name**: `03. Title (H2 - H4)`  
**View**: `bloc_title`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "hn": {
      "type": "string",
      "multiple": false,
      "format": "dropdownlist",
      "items": [
        {"title": "H2", "value": "h2"},
        {"title": "H3", "value": "h3"},
        {"title": "H4", "value": "h4"}
      ],
      "title": "Level",
      "description": "Heading level"
    },
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Intermediate title"
    }
  },
  "required": ["title", "hn"]
}
```

---

### 04. Card

**Name**: `04. Card`  
**View**: `bloc_card`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Section title (h1)"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"]
          ]
        },
        "formats": ["bold", "italic", "underline"]
      },
      "title": "Description",
      "description": "Section description"
    },
    "icon": {
      "type": "string",
      "format": "file",
      "fileType": "svg",
      "title": "Icon",
      "description": "SVG icon"
    },
    "link": {
      "type": "string",
      "title": "CTA",
      "description": "Link title"
    },
    "linkRoute": {
      "type": "string",
      "title": "Target",
      "description": "Link target element"
    },
    "linkUrl": {
      "type": "string",
      "title": "Target (URL)",
      "description": "Raw link (will be used if no route)"
    }
  },
  "required": ["title", "description", "icon"]
}
```

---

### 04. Content

**Name**: `04. Content`  
**View**: `bloc_content`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "content": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}],
            ["link"]
          ]
        },
        "formats": ["bold", "italic", "link", "underline", "list"]
      },
      "title": "Content",
      "description": "Content block"
    }
  },
  "required": ["content"]
}
```

---

### 04. Cta

**Name**: `04. Cta`  
**View**: `bloc_cta`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Title for the CTA"
    },
    "description": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}]
          ]
        },
        "formats": ["bold", "italic", "underline", "list"]
      },
      "title": "Description",
      "description": "Not too long, it's for the CTA"
    },
    "ctaTitle": {
      "type": "string",
      "title": "CTA title",
      "description": "Title of the primary CTA"
    },
    "ctaTarget": {
      "type": "string",
      "title": "Target content",
      "description": "Select the content/page which will be the target of the CTA"
    }
  },
  "required": ["title", "description", "ctaTitle", "ctaTarget"]
}
```

---

### 05. Info

**Name**: `05. Info`  
**View**: `bloc_info`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "type": {
      "title": "Type",
      "description": "Kind of alert",
      "type": "string",
      "format": "dropdownlist",
      "items": [
        {"title": "Info", "value": "info"},
        {"title": "Warning", "value": "warning"},
        {"title": "Error", "value": "error"}
      ]
    },
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Title of the infobox"
    },
    "content": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"],
            [{"list": "bullet"}]
          ]
        },
        "formats": ["bold", "italic", "underline", "list"]
      },
      "title": "Content",
      "description": "Content of the infobox"
    }
  },
  "required": ["type", "title", "content"]
}
```

---

### 06. Code

**Name**: `06. Code`  
**View**: `bloc_code`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "language": {
      "title": "Language",
      "description": "Language used for display",
      "type": "string",
      "format": "dropdownlist",
      "items": [
        {"title": "PHP", "value": "php"},
        {"title": "SQL", "value": "sql"},
        {"title": "JSON", "value": "json"},
        {"title": "Typescript", "value": "typescript"},
        {"title": "HTML", "value": "html"},
        {"title": "SCSS", "value": "scss"},
        {"title": "Javascript", "value": "javascript"},
        {"title": "CSS", "value": "css"},
        {"title": "Bash", "value": "bash"},
        {"title": "Text", "value": "text"},
        {"title": "GraphQL", "value": "graphql"}
      ]
    },
    "code": {
      "type": "string",
      "format": "textarea",
      "title": "Code",
      "description": "Block of code"
    }
  },
  "required": ["language", "code"]
}
```

---

### 07. Contact

**Name**: `07. Contact`  
**View**: `bloc_contact`

**JSON Schema**:

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string",
      "title": "Title",
      "description": "Section title (h2)"
    },
    "successTitle": {
      "type": "string",
      "title": "Success title",
      "description": "Success section title (h2)"
    },
    "success": {
      "type": "string",
      "format": "wysiwyg",
      "options": {
        "theme": "snow",
        "modules": {
          "toolbar": [
            ["bold", "italic", "underline"]
          ]
        },
        "formats": ["bold", "italic", "underline"]
      },
      "title": "Success",
      "description": "Success message"
    }
  },
  "required": ["title", "successTitle", "success"]
}
```

---

## Create Types

**Access**: Sidebar menu → **Settings → Types** → Button **Add type**

### Type Redirect Lang

**Name**: `Redirect Lang`  
**Route**: Select `cms/redirect-language`

**Permissions**:
- Section: Checked
- Article: Not checked
- Category: Not checked
- Tag: Not checked

**Allowed BlocTypes**: None

---

### Type Home

**Name**: `Home`  
**Route**: Select `cms/home`

**Permissions**:
- Section: Checked
- Article: Not checked
- Category: Not checked
- Tag: Not checked

**Allowed BlocTypes**:
- 01. Hero (H1)
- 02. Feature (H3)
- 04. Cta

---

### Type Documentation

**Name**: `Documentation`  
**Route**: Select `cms/documentation`

**Permissions**:
- Section: Not checked
- Article: Checked
- Category: Not checked
- Tag: Not checked

**Allowed BlocTypes**:
- 01. Hero (H1)
- 03. Title (H2 - H4)
- 04. Content
- 04. Cta
- 05. Info
- 06. Code

---

### Type Contact

**Name**: `Contact`  
**Route**: Select `cms/contact`

**Permissions**:
- Section: Not checked
- Article: Checked
- Category: Not checked
- Tag: Not checked

**Allowed BlocTypes**:
- 01. Hero (H1)
- 04. Card
- 05. Info
- 07. Contact

---

### Type Redirect First Composite

**Name**: `Redirect First Composite`  
**Route**: Select `cms/redirect-first-composite`

**Permissions**:
- Section: Checked
- Article: Not checked
- Category: Not checked
- Tag: Not checked

**Allowed BlocTypes**: None

---

## Create Admin Templates

Admin templates customize the input interface in the back-office for certain BlocTypes. They allow replacing text fields with smart dropdowns.

> **Info**
> `CmsHelper::getTargets()` returns the list of all site Nodes and Composites, grouped by type (Sections/Articles), to populate selection dropdowns.

### Template bloc_hero.php

**Role**: Replaces `lang`, `ctaTarget`, and `ctaSecondaryTarget` fields with selection dropdowns.

**File**: `common/admin/bloc_hero.php`

```php
<?php
/**
 * bloc_hero.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $element \blackcube\core\models\Tag|\blackcube\core\models\Composite|\blackcube\core\models\Category|\blackcube\core\models\Node
 * @var $bloc \blackcube\core\models\Bloc
 * @var $this \yii\web\View
 * @var $i
 */

use app\helpers\CmsHelper;
use blackcube\admin\helpers\Bo;
use blackcube\admin\helpers\BlackcubeHtml;
use blackcube\admin\helpers\Html;
use yii\helpers\ArrayHelper;
use yii\helpers\Url;

Bo::registerExternalAssets($element, $this);


foreach($bloc->elasticAttributes as $attribute => $value) {

    $structure = $bloc->structure[$attribute];
    $field = $structure['field'];
    switch ($field) {
        case 'file':
        case 'files':
            echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute, [
                'upload-url' => Url::to(['file-upload']),
                'preview-url' => Url::to(['file-preview', 'name' => '__name__']),
                'delete-url' => Url::to(['file-delete', 'name' => '__name__'])
            ]);
            break;
        default:
            if (in_array($attribute, ['lang', 'ctaTarget', 'ctaSecondaryTarget'])) {
                $dropData = CmsHelper::getTargets();
                $dropData = ArrayHelper::map($dropData, 'route', 'title', 'group');
                echo BlackcubeHtml::activeDropDownList($bloc, '[' . $i . ']' . $attribute, $dropData);
            } else {
                echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute);
            }
            break;
    }
}
```

**Explanation**:

- Loops through all elastic attributes of the block
- Detects field type via `$structure['field']`
- For files: displays uploader with upload/preview/delete URLs
- For `lang`, `ctaTarget`, `ctaSecondaryTarget`: replaces with dropdown populated by `CmsHelper::getTargets()`
- For other fields: displays standard field

---

### Template bloc_cta.php

**Role**: Replaces `ctaTarget` field with selection dropdown.

**File**: `common/admin/bloc_cta.php`

```php
<?php
/**
 * bloc_cta.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $element \blackcube\core\models\Tag|\blackcube\core\models\Composite|\blackcube\core\models\Category|\blackcube\core\models\Node
 * @var $bloc \blackcube\core\models\Bloc
 * @var $this \yii\web\View
 * @var $i
 */

use app\helpers\CmsHelper;
use blackcube\admin\helpers\Bo;
use blackcube\admin\helpers\BlackcubeHtml;
use blackcube\admin\helpers\Html;
use yii\helpers\ArrayHelper;
use yii\helpers\Url;

Bo::registerExternalAssets($element, $this);


foreach($bloc->elasticAttributes as $attribute => $value) {

    $structure = $bloc->structure[$attribute];
    $field = $structure['field'];
    switch ($field) {
        case 'file':
        case 'files':
            echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute, [
                'upload-url' => Url::to(['file-upload']),
                'preview-url' => Url::to(['file-preview', 'name' => '__name__']),
                'delete-url' => Url::to(['file-delete', 'name' => '__name__'])
            ]);
            break;
        default:
            if (in_array($attribute, ['ctaTarget'])) {
                $dropData = CmsHelper::getTargets();
                $dropData = ArrayHelper::map($dropData, 'route', 'title', 'group');
                echo BlackcubeHtml::activeDropDownList($bloc, '[' . $i . ']' . $attribute, $dropData);
            } else {
                echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute);
            }
            break;
    }
}
```

**Explanation**:

- Same logic as `bloc_hero.php`
- Only replaces `ctaTarget` field with dropdown

---

### Template bloc_card.php

**Role**: Replaces `linkRoute` field with selection dropdown.

**File**: `common/admin/bloc_card.php`

```php
<?php
/**
 * bloc_card.php
 *
 * PHP Version 8.3+
 *
 * @author Philippe Gaultier <pgaultier@gmail.com>
 * @copyright 2010-2025 Blackcube
 * @license https://blackcube.io/en/license
 * @link https://blackcube.io
 *
 * @var $element \blackcube\core\models\Tag|\blackcube\core\models\Composite|\blackcube\core\models\Category|\blackcube\core\models\Node
 * @var $bloc \blackcube\core\models\Bloc
 * @var $this \yii\web\View
 * @var $i
 */

use app\helpers\CmsHelper;
use blackcube\admin\helpers\Bo;
use blackcube\admin\helpers\BlackcubeHtml;
use blackcube\admin\helpers\Html;
use yii\helpers\ArrayHelper;
use yii\helpers\Url;

Bo::registerExternalAssets($element, $this);


foreach($bloc->elasticAttributes as $attribute => $value) {

    $structure = $bloc->structure[$attribute];
    $field = $structure['field'];
    switch ($field) {
        case 'file':
        case 'files':
            echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute, [
                'upload-url' => Url::to(['file-upload']),
                'preview-url' => Url::to(['file-preview', 'name' => '__name__']),
                'delete-url' => Url::to(['file-delete', 'name' => '__name__'])
            ]);
            break;
        default:
            if (in_array($attribute, ['linkRoute'])) {
                $dropData = CmsHelper::getTargets();
                $dropData = ArrayHelper::map($dropData, 'route', 'title', 'group');
                echo BlackcubeHtml::activeDropDownList($bloc, '[' . $i . ']' . $attribute, $dropData);
            } else {
                echo BlackcubeHtml::activeElasticField($bloc, '[' . $i . ']' . $attribute);
            }
            break;
    }
}
```

**Explanation**:

- Same logic as `bloc_hero.php`
- Only replaces `linkRoute` field with dropdown

> **Info**
> Admin templates are optional. Without them, fields display as free text. With them, contributors select targets in dropdowns rather than manually entering routes.

---

## Create Parameters

**Access**: Sidebar menu → **Settings → Parameters** → Button **Add parameter**

Parameters centralize BlocType IDs to avoid hardcoding in PHP code.

> **Warning**
> Use the IDs noted during BlocType creation.

### Parameter BLOC / HERO

**Domain**: `BLOC`  
**Name**: `HERO`  
**Value**: *Hero (H1) BlocType ID*

### Parameter BLOC / TITLE

**Domain**: `BLOC`  
**Name**: `TITLE`  
**Value**: *Title (H2 - H4) BlocType ID*

### Parameter BLOC / CONTENT

**Domain**: `BLOC`  
**Name**: `CONTENT`  
**Value**: *Content BlocType ID*

### Parameter BLOC / CODE

**Domain**: `BLOC`  
**Name**: `CODE`  
**Value**: *Code BlocType ID*

### Parameter BLOC / INFO

**Domain**: `BLOC`  
**Name**: `INFO`  
**Value**: *Info BlocType ID*

### Parameter BLOC / FEATURE

**Domain**: `BLOC`  
**Name**: `FEATURE`  
**Value**: *Feature (H3) BlocType ID*

### Parameter BLOC / CTA

**Domain**: `BLOC`  
**Name**: `CTA`  
**Value**: *Cta BlocType ID*

### Parameter BLOC / CARD

**Domain**: `BLOC`  
**Name**: `CARD`  
**Value**: *Card BlocType ID*

> **Info**
> These parameters are used in PHP code via `Parameters::get('BLOC', 'HERO')` to dynamically retrieve Hero BlocType ID.

---

## Create Menus

**Access**: Sidebar menu → **Settings → Menus** → Button **Add menu**

### Menu Header FR

**Name**: `header`  
**Language**: `French`  
**Active**: Checked

**Items** (Button **Add menu item**):

**Item Why Blackcube**
- Name: `Why Blackcube`
- Route: Dropdown with all site content - Select article "Why Blackcube FR"

**Item Documentation**
- Name: `Documentation`
- Route: Dropdown with all site content - Select section "Guide FR"

**Item Github**
- Name: `Github`
- Route: Dropdown with all site content - Select `redirect/github`

---

### Menu Header EN

**Name**: `header`  
**Language**: `English`  
**Active**: Checked

**Items**:

**Item Why Blackcube**
- Name: `Why Blackcube`
- Route: Dropdown with all site content - Select article "Why Blackcube EN"

**Item Documentation**
- Name: `Documentation`
- Route: Dropdown with all site content - Select section "Guide EN"

**Item Github**
- Name: `Github`
- Route: Dropdown with all site content - Select `redirect/github`

---

### Menu Footer FR

**Name**: `footer`  
**Language**: `French`  
**Active**: Checked

**Items**:

**Item Documentation**
- Name: `Documentation`
- Route: Dropdown with all site content - Select section "Guide FR"

**Item Contact us**
- Name: `Contact us`
- Route: Dropdown with all site content - Select article "Contact FR"

**Item Github**
- Name: `Github`
- Route: Dropdown with all site content - Select `redirect/github`

---

### Menu Footer EN

**Name**: `footer`  
**Language**: `English`  
**Active**: Checked

**Items**:

**Item Documentation**
- Name: `Documentation`
- Route: Dropdown with all site content - Select section "Guide EN"

**Item Contact us**
- Name: `Contact us`
- Route: Dropdown with all site content - Select article "Contact EN"

**Item Github**
- Name: `Github`
- Route: Dropdown with all site content - Select `redirect/github`

---

## Validation

Before continuing:

- 9 BlocTypes created with their JSON Schema
- 5 Types created with their allowed BlocTypes
- 3 Admin templates created in `common/admin/`
- 8 Parameters created with BlocType IDs
- 4 Menus created with their items