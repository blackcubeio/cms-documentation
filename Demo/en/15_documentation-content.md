# Documentation Pages (Content Filling)
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Contact Pages (Content Filling)

---

## Objective

Create and fill sample documentation articles in Guide and About Nodes, with different block types (Hero, Title, Content, Code, Info, Cta). Configure URLs, sitemap, and SEO.

---

## Overview

Documentation pages use the **Documentation** Type which allows 8 BlocTypes:
- **Hero (H1)**: Page header
- **Title (H2-H4)**: Content structure
- **Content**: Rich paragraphs
- **Code**: Code examples
- **Info**: Colored alerts
- **Feature (H3)**: Optional highlights
- **Cta**: Call-to-actions
- **Card**: Optional insets

**Recommended structure**:
```
/fr/guide/why-choose-blackcube (FR Composite)
└── Blocks:
    ├── Hero (H1)
    ├── Title (H2)
    ├── Content
    ├── Title (H3)
    ├── Content
    ├── Info
    └── Cta

/fr/a-propos/why-choose-blackcube (FR Composite)
└── Same structure
```

---

## Create "Why choose Blackcube" Article (FR)

### Step 1: Composite Creation

**Access**: Sidebar menu → **Management → Articles** → Button **Add article**

**Article tab**:
1. **Active**: Checked
2. **Type**: Select `Documentation`
3. **Language**: Select `French`
4. **Name**: `Why choose Blackcube`
5. **Section**: Select **About** (FR) in dropdown
6. Save

Composite created with unique ID.

---

### Step 2: Fill Hero

**Contents section** → Button **Add block** → Select **01. Hero (H1)**

**Fields to fill**:

**title** (required):
```
Why choose Blackcube?
```

**description** (required):
```
Discover the advantages of Blackcube CMS for your Yii2 projects.
```

**breadcrumbTitle** (optional):
```
Why Blackcube
```

**lang** (multilingual linking):
- Select in dropdown → **Articles** → **Why choose Blackcube** (EN)
- To fill after creating EN article

**Unused fields**:
- image: Leave empty
- ctaTitle: Leave empty
- ctaTarget: Leave empty
- ctaSecondaryTitle: Leave empty
- ctaSecondaryTarget: Leave empty

Save block.

---

### Step 3: Structure Content

#### Title H2: Flexibility

**Contents section** → Button **Add block** → Select **03. Title (H2 - H4)**

**Fields to fill**:

**hn** (required):
- Select `h2` in dropdown

**title** (required):
```
Total flexibility
```

Save block.

---

#### Content: Flexibility Description

**Contents section** → Button **Add block** → Select **04. Content**

**Fields to fill**:

**content** (required):
```
<p>Blackcube doesn't impose any structure. You keep total control over your architecture, widgets, and code. Adapt the CMS to your needs, not the other way around.</p>
```

Save block.

---

#### Title H2: Performance

**Contents section** → Button **Add block** → Select **03. Title (H2 - H4)**

**Fields to fill**:

**hn** (required):
- Select `h2` in dropdown

**title** (required):
```
Optimized performance
```

Save block.

---

#### Content: Performance Description

**Contents section** → Button **Add block** → Select **04. Content**

**Fields to fill**:

**content** (required):
```
<p>The Hazel Tree architecture retrieves the entire tree structure in a single SQL query. ElasticModel avoids database migrations with every structure change.</p>
```

Save block.

---

#### Info Box

**Contents section** → Button **Add block** → Select **05. Info**

**Fields to fill**:

**type** (required):
- Select `info` in dropdown

**title** (required):
```
Yii2 required
```

**content** (required):
```
<p>Blackcube is a module for Yii2. You must have an existing Yii2 application or create a new one.</p>
```

Save block.

---

#### Final CTA

**Contents section** → Button **Add block** → Select **04. Cta**

**Fields to fill**:

**title** (required):
```
Ready to start?
```

**description** (required):
```
<p>Check out our installation guide to get started with Blackcube.</p>
```

**ctaTitle** (required):
```
View installation
```

**ctaTarget** (required):
- Select in dropdown → **Articles** → **Installation** (FR)
- If article doesn't exist yet, leave empty and return later

Save block.

---

### Step 4: URL Configuration

> **Info**
> In Blackcube, URL path is always **complete and absolute** for all elements (Nodes, Composites, Categories, Tags). Path is never automatically inherited from parent.

**URL tab**:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `fr/a-propos/why-choose-blackcube`
4. Save

Final public URL: `/fr/a-propos/why-choose-blackcube`

---

### Step 5: Sitemap Configuration

**Sitemap tab**:

1. **Active**: Checked
2. **Frequency**: `monthly`
3. **Priority**: `0.7`
4. Save

---

### Step 6: SEO Configuration

**SEO tab**:

1. **Active**: Checked
2. **Title**: `Why choose Blackcube CMS?`
3. **Description**: `Discover Blackcube advantages: total flexibility, optimized performance, and modular architecture for your Yii2 projects.`
4. Save

---

## Create "Why choose Blackcube" Article (EN)

### Step 1: Composite Creation

**Access**: Sidebar menu → **Management → Articles** → Button **Add article**

**Article tab**:
1. **Active**: Checked
2. **Type**: Select `Documentation`
3. **Language**: Select `English`
4. **Name**: `Why choose Blackcube`
5. **Section**: Select **About** (EN) in dropdown
6. Save

---

### Step 2: Fill Hero

**Contents section** → Button **Add block** → Select **01. Hero (H1)**

**Fields to fill**:

**title** (required):
```
Why choose Blackcube?
```

**description** (required):
```
Discover the advantages of Blackcube CMS for your Yii2 projects.
```

**breadcrumbTitle** (optional):
```
Why Blackcube
```

**lang** (multilingual linking):
- Select in dropdown → **Articles** → **Why choose Blackcube** (FR)

Save block.

---

### Step 3: Structure Content

#### Title H2: Flexibility

**Contents section** → Button **Add block** → Select **03. Title (H2 - H4)**

**hn**: `h2`  
**title**:
```
Total flexibility
```

Save.

---

#### Content: Flexibility Description

**Contents section** → Button **Add block** → Select **04. Content**

**content**:
```
<p>Blackcube doesn't impose any structure. You keep total control over your architecture, widgets and code. Adapt the CMS to your needs, not the other way around.</p>
```

Save.

---

#### Title H2: Performance

**Contents section** → Button **Add block** → Select **03. Title (H2 - H4)**

**hn**: `h2`  
**title**:
```
Optimized performance
```

Save.

---

#### Content: Performance Description

**Contents section** → Button **Add block** → Select **04. Content**

**content**:
```
<p>The Hazel Tree architecture retrieves the entire tree structure in a single SQL query. ElasticModel avoids database migrations with every structure change.</p>
```

Save.

---

#### Info Box

**Contents section** → Button **Add block** → Select **05. Info**

**type**: `info`  
**title**:
```
Yii2 required
```

**content**:
```
<p>Blackcube is a module for Yii2. You must have an existing Yii2 application or create a new one.</p>
```

Save.

---

#### Final CTA

**Contents section** → Button **Add block** → Select **04. Cta**

**title**:
```
Ready to start?
```

**description**:
```
<p>Check out our installation guide to get started with Blackcube.</p>
```

**ctaTitle**:
```
View installation
```

**ctaTarget**:
- Select in dropdown → **Articles** → **Installation** (EN)

Save.

---

### Step 4: URL Configuration

**URL tab**:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `en/about/why-choose-blackcube`
4. Save

Final public URL: `/en/about/why-choose-blackcube`

---

### Step 5: Sitemap Configuration

**Sitemap tab**:

1. **Active**: Checked
2. **Frequency**: `monthly`
3. **Priority**: `0.7`
4. Save

---

### Step 6: SEO Configuration

**SEO tab**:

1. **Active**: Checked
2. **Title**: `Why choose Blackcube CMS?`
3. **Description**: `Discover the advantages of Blackcube: total flexibility, optimized performance and modular architecture for your Yii2 projects.`
4. Save

---

## Create Technical Articles

To demonstrate **Code** block usage, create a technical article.

### Article "Installation" (FR)

#### Creation

**Access**: Sidebar menu → **Management → Articles** → Button **Add article**

1. **Active**: Checked
2. **Type**: `Documentation`
3. **Language**: `French`
4. **Name**: `Installation`
5. **Section**: **Guide** (FR)
6. Save

---

#### Hero

**title**:
```
Installation
```

**description**:
```
Blackcube CMS installation guide for your Yii2 project.
```

**breadcrumbTitle**:
```
Installation
```

**lang**: Link with **Installation** (EN) after creation

Save.

---

#### Title H2: Prerequisites

**hn**: `h2`  
**title**:
```
Prerequisites
```

Save.

---

#### Content: Prerequisites List

**content**:
```
<p>Before installing Blackcube, ensure you have:</p>
<ul>
<li>PHP 8.3 or higher</li>
<li>A working Yii2 application</li>
<li>Composer installed</li>
<li>MySQL 8.0 or MariaDB 10.11</li>
</ul>
```

Save.

---

#### Title H2: Installation via Composer

**hn**: `h2`  
**title**:
```
Installation via Composer
```

Save.

---

#### Content: Instructions

**content**:
```
<p>Install Blackcube with Composer:</p>
```

Save.

---

#### Code: Composer Command

**Contents section** → Button **Add block** → Select **06. Code**

**Fields to fill**:

**language** (required):
- Select `bash` in dropdown

**code** (required):
```
composer require blackcube/core
composer require blackcube/admin
```

Save block.

---

#### Info Box: Important Note

**type**: `warning`  
**title**:
```
Configuration required
```

**content**:
```
<p>After installation, you must configure the modules in your configuration file.</p>
```

Save.

---

#### Title H2: Configuration

**hn**: `h2`  
**title**:
```
Configuration
```

Save.

---

#### Content: Configuration Introduction

**content**:
```
<p>Add the Blackcube modules in your configuration:</p>
```

Save.

---

#### Code: PHP Configuration

**language**: `php`  
**code**:
```
'modules' => [
    'admin' => [
        'class' => 'blackcube\admin\Module',
    ],
    'core' => [
        'class' => 'blackcube\core\Module',
    ],
],
```

Save.

---

#### CTA: Next Step

**title**:
```
Next step
```

**description**:
```
<p>Now that Blackcube is installed, configure your database.</p>
```

**ctaTitle**:
```
Database configuration
```

**ctaTarget**:
- Select next article in dropdown

Save.

---

#### URL/Sitemap/SEO Configuration

**URL**: `fr/guide/installation`  
**Sitemap**: Active, weekly, 0.8  
**SEO**: `Blackcube CMS Installation | Complete Guide`

---

## Link FR/EN Content

Once all articles created, return to each FR Hero to link with corresponding EN article via `lang` field.

---

## Recommended Articles

For a complete documentation site, create at minimum:

**Guide Section (FR/EN)**:
- Installation
- Configuration
- Getting Started
- Creating BlocTypes
- Creating Types

**About Section (FR/EN)**:
- Why choose Blackcube
- BSD 3-Clause License
- Contributing

---

## Validation

Check in **Management → Articles**:

**Created articles**:
- At least 2 articles per language
- Type: Documentation
- URLs configured with complete paths
- Sitemap enabled
- SEO configured
- FR ↔ EN linking working

**Block structure**:
- Hero present on all articles
- Title/Content alternation for clear structure
- Code blocks for technical examples
- Info Boxes for important notes
- CTA for navigation