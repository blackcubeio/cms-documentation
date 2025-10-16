# Project Definition
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: PHP, Yii2 MVC Framework is a plus

---

## Objective

Build a **professional documentation site** for Blackcube CMS that demonstrates the CMS capabilities in a real-world situation.

This project serves two goals:

1. **Technical showcase**: Concrete demonstration of Blackcube capabilities
2. **Real-world use case**: Complete and functional implementation example

> **Info**
> This guide presents architecture and usage examples. Blackcube doesn't impose anything: adapt structures, widgets, and approaches to your project needs. Developers remain in full control of their code and choices.

---

## Target Audience

Developers looking to:
- Evaluate Blackcube CMS for their projects
- Understand how to structure a Blackcube project
- See a concrete implementation example

---

## Tech Stack

**Backend**:
- Yii2 (PHP 8.3+)
- Blackcube (core + admin)
- MySQL 8.0 / MariaDB 10.11

**Frontend**:
- Tailwind CSS v4
- Vanilla JS
- Heroicons (SVG)

---

## Features Demonstrated

### Content Structure

**BlocTypes and Types**:
- 9 BlocTypes with JSON Schema
- 5 page Types
- Custom admin templates

**Tree Structure**:
- Nodes (Sections)
- Composites (Articles)
- Parent/child relationships

### Multilingual

- Parallel FR/EN structure
- Language switcher
- Automatic hreflang tags

### SEO

- Complete per-element configuration
- XML sitemap
- Clean URLs

### Navigation

- Hierarchical sidebar
- Automatic breadcrumb
- TOC extracted from headings
- Prev/next navigation

### Administration

- Centralized parameters
- Configurable menus
- Custom input interface

---

## Deliverables

### Back-Office Configuration

- 9 configured BlocTypes
- 5 content Types
- Centralized parameters
- Menus (header/footer FR/EN)
- Complete tree structure

### PHP Code

- Helpers (Parameters, CmsHelper)
- Controllers (Cms, Redirect)
- Behavior (HrefLang)
- Model (MailContact)
- Block and layout widgets
- Views
- Admin templates

### Content

- Home pages (FR/EN)
- Contact pages (FR/EN)
- Documentation articles

---

## Site Structure

### Tree Structure

```
/ (Language Redirect)
├── /fr (Home FR)
│   ├── /fr/guide (Documentation FR)
│   │   └── /fr/guide/* (Articles)
│   ├── /fr/a-propos (About FR)
│   │   └── /fr/a-propos/* (Articles)
│   └── /fr/contact (Contact FR)
│
└── /en (Home EN)
    ├── /en/guide (Documentation EN)
    │   └── /en/guide/* (Articles)
    ├── /en/about (About EN)
    │   └── /en/about/* (Articles)
    └── /en/contact (Contact EN)
```

### Page Types

**Home page**:
- Type: Home
- Layout: Header + Hero + Features + CTA + Footer

**Documentation pages**:
- Type: Documentation
- Layout: Header + Sidebar + Content + TOC + Footer

**Contact page**:
- Type: Contact
- Layout: Header + Hero + Form + Sidebar + Footer

---

## Architectural Flexibility

The widget/dispatcher approach presented is ONE example. Each project can have its own architecture:
- Reusable widgets
- Direct code in views
- Mixed approach

> **Info**
> Blackcube doesn't enforce any particular structure.

---

## Validation

Before continuing:
- Project objective understood
- Tech stack validated
- Features to demonstrate identified