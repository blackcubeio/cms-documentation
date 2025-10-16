# Design Analysis
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Project Definition

---

## Objective

Analyze mockups to identify page types, recurring components, and their variations. This analysis determines the structure of BlocTypes and Types to create.

---

## Identified Page Types

### Home Page

**Characteristics**:
- Full-width hero with background image
- Main H1 heading
- Short description
- 2 CTA buttons (primary and secondary)
- Feature section in 3-column grid
- Final conversion CTA

**Layout**:
```
Header
Hero (full-width, background image)
Features (3-column grid with icons)
CTA (full-width section)
Footer
```

### Documentation Pages

**Characteristics**:
- 3-column layout
- Left sidebar navigation (fixed)
- Center content (limited width for readability)
- Right TOC (fixed)
- Breadcrumb above content
- Prev/next navigation at bottom
- Simplified hero without image

**Layout**:
```
Header
┌──────────┬─────────────────────┬──────────┐
│ Sidebar  │ Breadcrumb          │ TOC      │
│ (fixed)  │ Hero (simplified)   │ (fixed)  │
│          │ Content (variable)  │          │
│          │ Navigation          │          │
└──────────┴─────────────────────┴──────────┘
Footer
```

### Contact Page

**Characteristics**:
- Simple hero without image
- 2-column unequal layout
- Form on left (60%)
- Info cards on right (40%)
- Simplified hero

**Layout**:
```
Header
Hero (simplified)
┌───────────────────────┬──────────────┐
│ Form (60%)            │ Cards (40%)  │
│ - Name                │ - Email      │
│ - Email               │ - Support    │
│ - Subject             │ - Github     │
│ - Message             │              │
└───────────────────────┴──────────────┘
Footer
```

---

## Layout Components

### Header

**Recurrent**: All pages

**Content**:
- Clickable logo
- Main navigation menu
- Language switcher (FR/EN)
- Mobile menu (hamburger)

### Footer

**Recurrent**: All pages

**Content**:
- Dynamic copyright
- Secondary navigation menu
- External links (Github)

### Sidebar

**Conditional**: Documentation pages only

**Content**:
- Hierarchical Node/Composite tree
- Active page highlight
- Automatic generation

### TOC

**Conditional**: Documentation pages only

**Content**:
- Table of contents
- List of H2/H3 headings
- Anchor links
- Fixed on right

### Breadcrumb

**Conditional**: Documentation pages only

**Content**:
- Breadcrumb trail
- Parent Node hierarchy
- Non-clickable current page

### Navigation

**Conditional**: Documentation pages only

**Content**:
- Previous link (if exists)
- Next link (if exists)
- Navigation between Composites of same Node

---

## Content Components

### Hero

**Usage**: All pages

**BlocType fields**:
- H1 title
- WYSIWYG description
- `lang` field for multilingual linking
- Background image (optional)
- 2 primary and secondary CTAs (optional)
- `breadcrumbTitle` field for breadcrumb (optional)

**Variants handled in PHP**:
- Home: Shows image + 2 CTAs
- Documentation: Shows breadcrumbTitle, no image or CTA
- Contact: No image or CTA

### Feature

**Usage**: Home page

**BlocType fields**:
- SVG icon
- H3 title
- Short WYSIWYG description

**Display**:
- Grouped in 3-column grid
- Automatic grouping of consecutive features

### Title

**Usage**: Documentation pages

**BlocType fields**:
- Level (H2, H3, H4) via dropdown
- Title text

**Behavior**:
- Generates automatic anchor
- Feeds the TOC

### Content

**Usage**: Documentation pages

**BlocType fields**:
- Rich WYSIWYG content
- Limited toolbar (bold, italic, list, link)

### Code

**Usage**: Documentation pages

**BlocType fields**:
- Language via dropdown (PHP, SQL, JSON, TypeScript, HTML, SCSS, Javascript, CSS, Bash, Text, GraphQL)
- Raw code (textarea)

**Display**:
- Syntax highlighting
- Copy button

### Info

**Usage**: Documentation pages

**BlocType fields**:
- Type via dropdown (info, warning, error)
- Title
- WYSIWYG content

**Display**:
- Color based on type (blue, yellow, red)
- Thick left border

### CTA

**Usage**: Home page, documentation pages

**BlocType fields**:
- H2 title
- WYSIWYG description
- Button text
- Target via dropdown (Node/Composite selection)

### Card

**Usage**: Contact page

**BlocType fields**:
- SVG icon
- Title
- WYSIWYG description
- Optional link (text + target)

### Contact

**Usage**: Contact page only

**BlocType fields**:
- Form title
- Success title
- WYSIWYG success message

**Behavior**:
- Configures the form
- Defines return messages

---

## Variation Summary

### Components with Variants

**Hero**: Variants handled at PHP widget level based on page type
- All fields in BlocType
- Optional fields used as needed

**Features**: Dynamic grouping
- Individual: Simple card display
- Multiple consecutive: 3-column grid

### Single-Variant Components

All other components (Title, Content, Code, Info, CTA, Card, Contact) have a single variant.

---

## BlocType Implications

### BlocTypes to Create

Based on design analysis:

**Hero**: 1 BlocType with all fields
- Fields: title, description, lang, image, ctaTitle, ctaTarget, ctaSecondaryTitle, ctaSecondaryTarget, breadcrumbTitle
- Variants handled in PHP widget

**Feature**: 1 BlocType
- Fields: icon, title, description

**Title**: 1 BlocType
- Fields: hn, title

**Content**: 1 BlocType
- Fields: content

**Code**: 1 BlocType
- Fields: language, code

**Info**: 1 BlocType
- Fields: type, title, content

**CTA**: 1 BlocType
- Fields: title, description, ctaTitle, ctaTarget

**Card**: 1 BlocType
- Fields: icon, title, description, link, linkRoute, linkUrl

**Contact**: 1 BlocType
- Fields: title, successTitle, success

**Total**: 9 BlocTypes

---

## Type Implications

### Types to Create

**Home**:
- Route: cms/home
- Allowed BlocTypes: Hero, Feature, CTA
- Usage: Home pages

**Documentation**:
- Route: cms/documentation
- Allowed BlocTypes: Hero, Title, Content, Code, Info, Feature, CTA, Card
- Usage: Documentation articles

**Contact**:
- Route: cms/contact
- Allowed BlocTypes: Hero, Card, Info, Contact
- Usage: Contact pages

**Redirect Language**:
- Route: cms/redirect-language
- Allowed BlocTypes: None
- Usage: Root node for language detection

**Redirect First Composite**:
- Route: cms/redirect-first-composite
- Allowed BlocTypes: None
- Usage: Container nodes

**Total**: 5 Types

---

## Admin Templates to Plan

Some BlocTypes require custom interfaces for easier input.

### Hero

**Need**: Dropdown to select linked content (`lang` field)

**Reason**: Allows FR/EN linking for language switcher and hreflang tags

### CTA

**Need**: Dropdown to select target (`ctaTarget` field)

**Reason**: Avoids manual route entry, provides all Nodes/Composites

### Card

**Need**: Dropdown to select target (`linkRoute` field)

**Reason**: Avoids manual route entry, provides all Nodes/Composites

**Total**: 3 admin templates

---

## Validation

Before continuing:
- 3 page types identified
- Layout components listed (6 widgets)
- Content components listed (9 widgets)
- Component variations identified
- 9 BlocTypes defined
- 5 Types defined
- 3 admin templates planned