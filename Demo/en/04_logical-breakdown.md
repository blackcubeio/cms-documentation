# Logical Breakdown
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Project Initialization

---

## Objective

Identify and list all components needed for the project before starting development. This analysis step allows planning the architecture and anticipating developments.

> **Info**
> This step creates no code. We only identify and list components to develop.

---

## Identify Page Types

Based on design analysis, the site includes 3 different page types.

### Home Page

**Role**: Present Blackcube CMS and its main features

**Characteristics**:
- Full-width hero with image
- 3-column feature grid
- Conversion CTA

**Blackcube Type**: Home

### Documentation Pages

**Role**: Technical content with complete navigation

**Characteristics**:
- 3-column layout (Sidebar + Content + TOC)
- Breadcrumb and prev/next navigation
- Varied content blocks

**Blackcube Type**: Documentation

### Contact Page

**Role**: Contact form with information

**Characteristics**:
- Input form
- Info cards in sidebar
- Confirmation after submission

**Blackcube Type**: Contact

---

## Identify Layout Widgets

Layout widgets structure pages and handle navigation.

### Systematic Widgets

**Header**: Present on all pages
- Logo
- Main menu
- Language switcher (FR/EN)
- Mobile menu

**Footer**: Present on all pages
- Copyright
- Secondary menu
- External links

### Conditional Widgets

**Sidebar**: Documentation pages only
- Node/Composite tree
- Active page highlight
- Hierarchical navigation

**TOC**: Documentation pages only
- Table of contents
- List of H2/H3 headings
- Anchor links

**Breadcrumb**: Documentation pages only
- Breadcrumb trail
- Parent Node hierarchy
- Current page

**Navigation**: Documentation pages only
- Previous link
- Next link
- Between Composites of same Node

**Summary**: 6 layout widgets identified

---

## Identify Content Widgets

Content widgets display blocks created in the back-office.

### Main Widgets

**Hero**: All pages
- Main header
- Variants based on page type
- Fields: title, description, lang, image, CTA, breadcrumbTitle

**Feature**: Home page
- Highlight with icon
- 3-column grid display
- Automatic grouping

**Title**: Documentation pages
- H2, H3, H4 headings
- Anchor generation
- TOC feed

**Content**: Documentation pages
- Rich WYSIWYG content
- Paragraphs and lists

**Code**: Documentation pages
- Code blocks with highlighting
- Multi-language support
- Copy button

**Info**: Documentation pages
- Colored alerts (info, warning, error)
- Info boxes

**CTA**: Home and documentation pages
- Call-to-action
- Internal links

**Card**: Contact page
- Info cards
- SVG icons
- Optional CTAs

**Contact**: Contact page
- Form configuration
- Return messages

**Summary**: 9 content widgets identified

---

## Special Components

### Block Dispatcher

**Role**: Detect each block type and call appropriate widget

**Operation**:
- Loops through block array
- Detects individual Block or grouped Block array
- Calls corresponding widget

**Name**: Blocs.php

### Block Grouping

**Role**: Group consecutive Blocks for grid display

**Operation**:
- Detects consecutive Blocks with same blocTypeId
- Groups in array

**Example**: SectionFeatures.php widget (in addition to BlocFeature.php)

---

## Planned Architecture

### File Structure

> **Info**
> This structure is an example organization. Blackcube doesn't impose any particular structure: adapt the tree according to your project needs.

```
common/
├── admin/
│   ├── bloc_card.php
│   ├── bloc_cta.php
│   └── bloc_hero.php
├── helpers/
│   ├── Parameters.php
│   └── CmsHelper.php
└── models/
    └── MailContact.php

webapp/
├── behaviors/
│   └── HrefLangBehavior.php
├── controllers/
│   ├── CmsController.php
│   └── RedirectController.php
├── views/
│   └── cms/
│       ├── home.php
│       ├── documentation.php
│       ├── contact.php
│       └── contact-success.php
└── widgets/
    ├── Blocs.php                    # Dispatcher
    ├── BlocHero.php                 # + 3 views
    ├── BlocFeature.php
    ├── SectionFeatures.php
    ├── BlocTitle.php
    ├── BlocContent.php
    ├── BlocCode.php
    ├── BlocInfo.php
    ├── BlocCta.php
    ├── BlocCard.php
    ├── Header.php
    ├── Footer.php
    ├── Sidebar.php
    ├── Toc.php
    ├── Breadcrumb.php
    └── Navigation.php
```

### Component Dependencies

**Helpers**:
- Parameters: Used by all widgets and controllers
- CmsHelper: Used by controllers and dispatcher

**Controllers**:
- Use helpers
- Pass data to views

**Views**:
- Call widgets
- No business logic

**Widgets**:
- Use helpers
- Can call other widgets

---

## Validation

Before continuing, verify all components are identified:

**Page types**:
- Home
- Documentation
- Contact

**Layout widgets**:
- Header
- Footer
- Sidebar
- TOC
- Breadcrumb
- Navigation

**Content widgets**:
- Hero
- Feature (+ SectionFeatures)
- Title
- Content
- Code
- Info
- CTA
- Card
- Contact
- Blocs (dispatcher)

**Helpers**:
- Parameters
- CmsHelper

**Behaviors**:
- HrefLangBehavior

**Controllers**:
- CmsController
- RedirectController

**Models**:
- MailContact

**Total**: 6 layout widgets + 10 content widgets + 2 helpers + 1 behavior + 2 controllers + 1 model