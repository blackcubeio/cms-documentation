# BlocTypes Planning
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Types Planning

---

## Objective

Define the structure of each BlocType with its fields, identify variants, and plan custom admin templates.

> **Info**
> This step is planning. Actual BlocType creation in the back-office will be done after PHP code development.

---

## Define BlocTypes

> **Info**
> Numeric prefixes (01., 02., etc.) in BlocType names are only used to order them in back-office lists. They are not displayed to contributors when creating content.

### BlocType 1: Hero (H1)

**Usage**: All pages

**Name**: `01. Hero (H1)`
**View**: `bloc_hero`

**Fields**:
- `lang`: Associated content for other language (string)
- `title`: H1 title (string, required)
- `breadcrumbTitle`: Title for breadcrumb (string, optional)
- `description`: WYSIWYG description (string, required)
- `image`: Background image (file PNG/JPG, optional)
- `ctaTitle`: Primary CTA title (string, optional)
- `ctaTarget`: Primary CTA target (string, optional)
- `ctaSecondaryTitle`: Secondary CTA title (string, optional)
- `ctaSecondaryTarget`: Secondary CTA target (string, optional)

**Required fields**: title, description

---

### BlocType 2: Title (H2 - H4)

**Usage**: Documentation pages

**Name**: `03. Title (H2 - H4)`
**View**: `bloc_title`

**Fields**:
- `hn`: Title level (dropdown H2/H3/H4, required)
- `title`: Title text (string, required)

**Required fields**: hn, title

---

### BlocType 3: Content

**Usage**: Documentation pages

**Name**: `04. Content`
**View**: `bloc_content`

**Fields**:
- `content`: Rich content (WYSIWYG, required)

**WYSIWYG options**:
- Toolbar: bold, italic, underline, list, link
- Allowed formats: bold, italic, link, underline, list

**Required fields**: content

---

### BlocType 4: Code

**Usage**: Documentation pages

**Name**: `06. Code`
**View**: `bloc_code`

**Fields**:
- `language`: Language used (dropdown, required)
- `code`: Code block (textarea, required)

**Available languages**: PHP, SQL, JSON, Typescript, HTML, SCSS, Javascript, CSS, Bash, Text, GraphQL

**Required fields**: language, code

---

### BlocType 5: Info

**Usage**: Documentation pages

**Name**: `05. Info`
**View**: `bloc_info`

**Fields**:
- `type`: Alert type (dropdown info/warning/error, required)
- `title`: Infobox title (string, required)
- `content`: Infobox content (WYSIWYG, required)

**WYSIWYG options**:
- Toolbar: bold, italic, underline, list
- Allowed formats: bold, italic, underline, list

**Required fields**: type, title, content

---

### BlocType 6: Feature (H3)

**Usage**: Home page

**Name**: `02. Feature (H3)`
**View**: `bloc_feature`

**Fields**:
- `icon`: SVG icon (file SVG, required)
- `title`: H3 title (string, required)
- `description`: Short description (WYSIWYG, required)

**WYSIWYG options**:
- Toolbar: bold, italic, underline, list
- Allowed formats: bold, italic, underline, list

**Required fields**: icon, title, description

---

### BlocType 7: Cta

**Usage**: Home and documentation pages

**Name**: `04. Cta`
**View**: `bloc_cta`

**Fields**:
- `title`: H2 title (string, required)
- `description`: Description (WYSIWYG, required)
- `ctaTitle`: Button title (string, required)
- `ctaTarget`: Link target (string, required)

**WYSIWYG options**:
- Toolbar: bold, italic, underline, list
- Allowed formats: bold, italic, underline, list

**Required fields**: title, description, ctaTitle, ctaTarget

---

### BlocType 8: Card

**Usage**: Contact page

**Name**: `04. Card`
**View**: `bloc_card`

**Fields**:
- `icon`: SVG icon (file SVG, required)
- `title`: Title (string, required)
- `description`: Description (WYSIWYG, required)
- `link`: Link title (string, optional)
- `linkRoute`: Link target (string, optional)
- `linkUrl`: Raw URL (string, optional)

**WYSIWYG options**:
- Toolbar: bold, italic, underline
- Allowed formats: bold, italic, underline

**Required fields**: icon, title, description

---

### BlocType 9: Contact

**Usage**: Contact page only

**Name**: `07. Contact`
**View**: `bloc_contact`

**Fields**:
- `title`: Form H2 title (string, required)
- `successTitle`: Success H2 title (string, required)
- `success`: Success message (WYSIWYG, required)

**WYSIWYG options**:
- Toolbar: bold, italic, underline
- Allowed formats: bold, italic, underline

**Required fields**: title, successTitle, success

---

## Identify Variants

### Hero: Variants by Page Type

The Hero BlocType contains all possible fields. Variants are handled at PHP widget level:

**Home Variant**:
- Shows: title, description, image, ctaTitle, ctaTarget, ctaSecondaryTitle, ctaSecondaryTarget
- Hides: breadcrumbTitle
- Layout: Full-width with background image

**Documentation Variant**:
- Shows: title, description, breadcrumbTitle
- Hides: image, CTA
- Layout: Simplified without image

**Contact Variant**:
- Shows: title, description
- Hides: image, CTA, breadcrumbTitle
- Layout: Simplified without image

**lang field**: Present in all variants for multilingual linking

---

## Admin Templates

Some BlocTypes require custom interfaces in the back-office to improve input experience.

### Template 1: bloc_hero.php

**BlocType**: Hero (H1)

**Need**: Dropdowns to select targets

**Affected fields**:
- `lang`: Dropdown listing all Nodes/Composites for FR/EN linking
- `ctaTarget`: Dropdown listing all Nodes/Composites
- `ctaSecondaryTarget`: Dropdown listing all Nodes/Composites

**Reason**: Avoids manual route entry, displays organized list by type (Sections/Articles) with language indication

---

### Template 2: bloc_cta.php

**BlocType**: Cta

**Need**: Dropdown to select target

**Affected fields**:
- `ctaTarget`: Dropdown listing all Nodes/Composites

**Reason**: Avoids manual route entry, provides all available content

---

### Template 3: bloc_card.php

**BlocType**: Card

**Need**: Dropdown to select target

**Affected fields**:
- `linkRoute`: Dropdown listing all Nodes/Composites

**Reason**: Avoids manual route entry, allows easy target content selection

---

## Summary

### Defined BlocTypes

**Total**: 9 BlocTypes

**With variants**: 1 (Hero)
**With admin templates**: 3 (Hero, Cta, Card)

### Complete List

- Hero (H1) - bloc_hero - Admin template
- Title (H2-H4) - bloc_title
- Content - bloc_content
- Code - bloc_code
- Info - bloc_info
- Feature (H3) - bloc_feature
- Cta - bloc_cta - Admin template
- Card - bloc_card - Admin template
- Contact - bloc_contact

---

## Validation

Before continuing, verify:

- 9 BlocTypes defined with their fields
- Required fields identified for each BlocType
- Hero variants planned
- 3 admin templates to develop identified