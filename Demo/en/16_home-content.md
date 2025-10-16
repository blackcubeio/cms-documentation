# Home Pages (Content Filling)
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Documentation Pages (Content Filling)

---

## Objective

Fill Hero, Features, and CTA blocks on French and English home pages in the back-office. FR/EN Home Nodes were already created in step 13, now just need to complete their content blocks.

---

## Overview

Home pages use the **Home** Type which allows 3 BlocTypes:
- **Hero (H1)**: Main header with CTA
- **Feature (H3)**: Highlights (grouped by 3)
- **Cta**: Final call-to-action

**Final structure**:
```
/fr (FR Node - Type: Home)
└── Blocks:
    ├── Hero (H1)
    ├── Feature × 3 (Total Control, Flexible, Performant)
    └── Cta

/en (EN Node - Type: Home)
└── Blocks:
    ├── Hero (H1)
    ├── Feature × 3 (Total Control, Flexible, Performant)
    └── Cta
```

**Total**: 5 blocks per page

> **Info**
> FR/EN Home Nodes were already created in step 13 with URL, Sitemap, and SEO configuration. This step only covers filling content blocks.

---

## Fill Home FR Node

### Access Node

**Sidebar menu** → **Management → Sections** → Click pencil to edit **FR** Node

---

### Step 1: Complete Hero

Hero was already created in step 13. Now complete it with all fields.

**Contents section** → Click existing Hero to edit

**Fields to fill**:

**title** (required):
```
Blackcube CMS Documentation
```

**description** (required):
```
<p>The professional CMS that gives total control to developers. Code your rendering and manage your content with flexibility.</p>
```

**image** (optional):
- Upload PNG/JPG background image if available
- Leave empty if no image

**ctaTitle** (required):
```
Get Started >
```

**ctaTarget** (required):
- Select in dropdown → **Articles** → **Why choose Blackcube** (FR)

**ctaSecondaryTitle** (required):
```
Documentation
```

**ctaSecondaryTarget** (required):
- Select in dropdown → **Sections** → **Guide** (FR)

**breadcrumbTitle** (optional):
- Leave empty

**lang** (multilingual linking):
- Leave empty for now
- To fill after creating Hero EN

Save block.

---

### Step 2: Add 3 Features

#### Feature 1: Total Control

**Contents section** → Button **Add block** → Select **02. Feature (H3)**

**Fields to fill**:

**title** (required):
```
Total Control
```

**description** (required):
```
<p>Developers code 100% of the rendering. No template constraints, total freedom on the front-end.</p>
```

**icon** (required):
- Upload SVG icon (source code)
- Recommended SVG for "Total Control":

```svg
<svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4"></path>
</svg>
```

**Unused fields**:
- link: Leave empty
- linkRoute: Leave empty
- linkUrl: Leave empty

Save block.

---

#### Feature 2: Flexible

**Contents section** → Button **Add block** → Select **02. Feature (H3)**

**Fields to fill**:

**title** (required):
```
Flexible
```

**description** (required):
```
<p>JSON Schema to define your content. No SQL migration, instant evolution of your structure.</p>
```

**icon** (required):
- Upload SVG icon (layout/grid)
- Recommended SVG for "Flexible":

```svg
<svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 13a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H5a1 1 0 01-1-1v-6zM16 13a1 1 0 011-1h2a1 1 0 011 1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-6z"></path>
</svg>
```

**Unused fields**:
- link: Leave empty
- linkRoute: Leave empty
- linkUrl: Leave empty

Save block.

---

#### Feature 3: Performant

**Contents section** → Button **Add block** → Select **02. Feature (H3)**

**Fields to fill**:

**title** (required):
```
Performant
```

**description** (required):
```
<p>Hazel Tree: a single query to load the entire tree structure.</p>
```

**icon** (required):
- Upload SVG icon (lightning/performance)
- Recommended SVG for "Performant":

```svg
<svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path>
</svg>
```

**Unused fields**:
- link: Leave empty
- linkRoute: Leave empty
- linkUrl: Leave empty

Save block.

---

### Step 3: Add Final CTA

**Contents section** → Button **Add block** → Select **08. Cta**

**Fields to fill**:

**title** (required):
```
Ready to get started?
```

**description** (required):
```
<p>Discover how Blackcube can transform your development workflow.</p>
```

**ctaTitle** (required):
```
Start now >
```

**ctaTarget** (required):
- Select in dropdown → **Articles** → **Why choose Blackcube** (FR)

Save block.

---

## Fill Home EN Node

### Access Node

**Sidebar menu** → **Management → Sections** → Click pencil to edit **EN** Node

---

### Step 1: Complete Hero

Hero was already created in step 13. Now complete it.

**Contents section** → Click existing Hero to edit

**Fields to fill**:

**title** (required):
```
Blackcube CMS Documentation
```

**description** (required):
```
<p>The professional CMS that gives total control to developers. Code your rendering and manage your content with flexibility.</p>
```

**image** (optional):
- Upload same image as FR if available
- Leave empty if no image

**ctaTitle** (required):
```
Get Started >
```

**ctaTarget** (required):
- Select in dropdown → **Articles** → **Why choose Blackcube** (EN)

**ctaSecondaryTitle** (required):
```
Documentation
```

**ctaSecondaryTarget** (required):
- Select in dropdown → **Sections** → **Guide** (EN)

**breadcrumbTitle** (optional):
- Leave empty

**lang** (multilingual linking):
- Leave empty for now
- To fill in next step

Save block.

---

### Step 2: Add 3 Features

#### Feature 1: Total Control

**Contents section** → Button **Add block** → Select **02. Feature (H3)**

**Fields to fill**:

**title** (required):
```
Total Control
```

**description** (required):
```
<p>Developers code 100% of the rendering. No template constraints, total freedom on the front-end.</p>
```

**icon** (required):
- Upload same SVG icon as Feature 1 FR (source code)

**Unused fields**:
- link: Leave empty
- linkRoute: Leave empty
- linkUrl: Leave empty

Save block.

---

#### Feature 2: Flexible

**Contents section** → Button **Add block** → Select **02. Feature (H3)**

**Fields to fill**:

**title** (required):
```
Flexible
```

**description** (required):
```
<p>JSON Schema to define your content. No SQL migration, instant evolution of your structure.</p>
```

**icon** (required):
- Upload same SVG icon as Feature 2 FR (layout/grid)

**Unused fields**:
- link: Leave empty
- linkRoute: Leave empty
- linkUrl: Leave empty

Save block.

---

#### Feature 3: Performant

**Contents section** → Button **Add block** → Select **02. Feature (H3)**

**Fields to fill**:

**title** (required):
```
Performant
```

**description** (required):
```
<p>Hazel Tree: a single query to load the entire tree structure.</p>
```

**icon** (required):
- Upload same SVG icon as Feature 3 FR (lightning/performance)

**Unused fields**:
- link: Leave empty
- linkRoute: Leave empty
- linkUrl: Leave empty

Save block.

---

### Step 3: Add Final CTA

**Contents section** → Button **Add block** → Select **08. Cta**

**Fields to fill**:

**title** (required):
```
Ready to get started?
```

**description** (required):
```
<p>Discover how Blackcube can transform your development workflow.</p>
```

**ctaTitle** (required):
```
Start now >
```

**ctaTarget** (required):
- Select in dropdown → **Articles** → **Why choose Blackcube** (EN)

Save block.

---

## Multilingual Linking FR ↔ EN

### Update Hero FR lang Field

1. Edit **FR** Node → Click **Hero** block
2. **lang** field: Select in dropdown → **Sections** → **EN**
3. Save

### Update Hero EN lang Field

1. Edit **EN** Node → Click **Hero** block
2. **lang** field: Select in dropdown → **Sections** → **FR**
3. Save

> **Info**
> This linking enables language switcher to toggle between `/fr` and `/en`. HrefLang behavior automatically generates `<link rel="alternate" hreflang="..." />` tags for SEO.

---

## Validation

Check in **Management → Sections**:

**Modified nodes**:
- FR (Type: Home) - Language: French
- EN (Type: Home) - Language: English

**Blocks per Node**:
- Hero (H1): Title + description + 2 CTAs
- Feature (H3) × 3: Icon + title + description
- Cta: Title + description + CTA
- **Total**: 5 blocks per page

**Configuration**:
- FR ↔ EN linking working via Hero's `lang` field
- All required fields filled
- All blocks active (JSON Schema validation passed)

> **Info**
> URLs, Sitemap, and SEO were already configured in step 13. This step only covered filling content blocks.

---

## Recommendations

### Features Content

The 3 Features present Blackcube strengths:
- **Total Control**: Developer total freedom on rendering
- **Flexible**: JSON Schema, no SQL migration
- **Performant**: Hazel Tree, optimized architecture

These messages target developers and highlight the CMS technical advantages.

### CTA and Navigation

CTAs point to:
- **Hero primary CTA**: "Why choose Blackcube" article (discovery)
- **Hero secondary CTA**: Guide (technical documentation)
- **Final CTA**: "Why choose Blackcube" article (conversion)

This navigation gradually guides developers from discovery to action.

---

**End of Step 16 - Complete Development Guide**