# Contact Pages (Content Filling)
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Section Preparation

---

## Objective

Create and fill French and English contact pages with their Hero, Cards, and Contact blocks in the back-office. Configure URLs, sitemap, and SEO.

---

## Overview

Contact pages use the **Contact** Type which allows 4 BlocTypes:
- **Hero (H1)**: Page header
- **Card**: Contact info in sidebar
- **Info**: Potential alerts
- **Contact**: Form configuration

**Final structure**:
```
/fr/contact (FR Composite)
└── Blocks:
    ├── Hero (H1)
    ├── Card × 3 (Support, GitHub, Documentation)
    ├── Info (Response time)
    └── Contact

/en/contact (EN Composite)
└── Blocks:
    ├── Hero (H1)
    ├── Card × 3 (Support, GitHub, Documentation)
    ├── Info (Response time)
    └── Contact
```

---

## Create Contact FR Composite

### Step 1: Composite Creation

**Access**: Sidebar menu → **Management → Articles** → Button **Add article**

**Article tab**:
1. **Active**: Checked
2. **Type**: Select `Contact`
3. **Language**: Select `French`
4. **Name**: `Contact`
5. **Section**: Select **FR** in dropdown
6. Save

Composite created with unique ID (visible in URL: `/bo/composite/edit?id=XX`).

---

### Step 2: Fill Hero

**Contents section** → Button **Add block** → Select **01. Hero (H1)**

**Fields to fill**:

**title** (required):
```
Contact
```

**description** (required):
```
<p>Question about Blackcube CMS? Don't hesitate to contact us, we're here to help.</p>
```

**breadcrumbTitle** (optional):
```
Contact
```

**lang** (multilingual linking):
- Select in dropdown → **Articles** → **Contact** (EN)
- This linking enables language switcher

**Unused fields**:
- image: Leave empty
- ctaTitle: Leave empty
- ctaTarget: Leave empty
- ctaSecondaryTitle: Leave empty
- ctaSecondaryTarget: Leave empty

Save block.

> **Info**
> The `lang` field can only be filled after creating Contact EN Composite. You'll need to return to modify this field after the "Create Contact EN Composite" step.

---

### Step 3: Fill Cards

#### Card 1: Support

**Contents section** → Button **Add block** → Select **04. Card**

**Fields to fill**:

**icon** (required):
- Upload SVG file `life-ring.svg`

**title** (required):
```
Support
```

**description** (required):
```
<p>For any technical questions, first check our complete documentation.</p>
```

**link** (optional):
```
View documentation >
```

**linkRoute** (required):
- Select in dropdown → **Sections** → **Guide** (FR)

**linkUrl** (optional):
- Leave empty (using linkRoute for internal link)

Save block.

---

#### Card 2: GitHub

**Contents section** → Button **Add block** → Select **04. Card**

**Fields to fill**:

**icon** (required):
- Upload SVG file `github.svg`

**title** (required):
```
GitHub
```

**description** (required):
```
<p>Find source code, report a bug, or contribute to the project on GitHub.</p>
```

**link** (optional):
```
github.com/blackcubeio
```

**linkUrl** (optional):
```
https://github.com/blackcubeio
```

**linkRoute** (optional):
- Leave empty (using linkUrl for external link)

Save block.

---

#### Card 3: Documentation

**Contents section** → Button **Add block** → Select **04. Card**

**Fields to fill**:

**icon** (required):
- Upload SVG file `book-open.svg`

**title** (required):
```
Documentation
```

**description** (required):
```
<p>Complete guides, tutorials, and API references to master Blackcube CMS.</p>
```

**link** (optional):
```
Explore documentation >
```

**linkRoute** (required):
- Select in dropdown → **Sections** → **Guide** (FR)

**linkUrl** (optional):
- Leave empty (using linkRoute for internal link)

Save block.

---

### Step 4: Fill Info Box

**Contents section** → Button **Add block** → Select **05. Info**

**Fields to fill**:

**type** (required):
- Select `info` in dropdown

**title** (required):
```
Response time
```

**content** (required):
```
<p>We strive to respond within 48 business hours.</p>
```

Save block.

---

### Step 5: Fill Contact Block

**Contents section** → Button **Add block** → Select **07. Contact**

**Fields to fill**:

**title** (required):
```
Send us a message
```

**successTitle** (required):
```
Message sent!
```

**success** (required):
```
<p>Thank you for your message. We'll get back to you as soon as possible.</p>
```

Save block.

---

### Step 6: URL Configuration

> **Info**
> In Blackcube, URL path is always **complete and absolute** for all elements (Nodes, Composites, Categories, Tags). Path is never automatically inherited from parent.

**URL tab**:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `fr/contact`
4. Save

Final public URL: `/fr/contact`

---

### Step 7: Sitemap Configuration

**Sitemap tab**:

1. **Active**: Checked
2. **Frequency**: `monthly`
3. **Priority**: `0.5`
4. Save

---

### Step 8: SEO Configuration

**SEO tab**:

1. **Active**: Checked
2. **Title**: `Contact - Blackcube CMS`
3. **Description**: `Contact the Blackcube team. Questions, suggestions, or technical support, we're here to help.`
4. Save

---

## Create Contact EN Composite

### Step 1: Composite Creation

**Access**: Sidebar menu → **Management → Articles** → Button **Add article**

**Article tab**:
1. **Active**: Checked
2. **Type**: Select `Contact`
3. **Language**: Select `English`
4. **Name**: `Contact`
5. **Section**: Select **EN** in dropdown
6. Save

---

### Step 2: Fill Hero

**Contents section** → Button **Add block** → Select **01. Hero (H1)**

**Fields to fill**:

**title** (required):
```
Contact Us
```

**description** (required):
```
<p>Have a question? A suggestion? We'd love to hear from you about Blackcube CMS.</p>
```

**breadcrumbTitle** (optional):
```
Contact
```

**lang** (multilingual linking):
- Select in dropdown → **Articles** → **Contact** (FR)

**Unused fields**:
- image: Leave empty
- ctaTitle: Leave empty
- ctaTarget: Leave empty
- ctaSecondaryTitle: Leave empty
- ctaSecondaryTarget: Leave empty

Save block.

---

### Step 3: Fill Cards

#### Card 1: Support

**Contents section** → Button **Add block** → Select **04. Card**

**Fields to fill**:

**icon** (required):
- Upload SVG file `life-ring.svg`

**title** (required):
```
Support
```

**description** (required):
```
<p>For any technical questions, please check our comprehensive documentation first.</p>
```

**link** (optional):
```
View documentation >
```

**linkRoute** (required):
- Select in dropdown → **Sections** → **Guide** (EN)

Save block.

---

#### Card 2: GitHub

**Contents section** → Button **Add block** → Select **04. Card**

**Fields to fill**:

**icon** (required):
- Upload SVG file `github.svg`

**title** (required):
```
GitHub
```

**description** (required):
```
<p>Find the source code, report a bug or contribute to the project on GitHub.</p>
```

**link** (optional):
```
github.com/blackcubeio
```

**linkUrl** (optional):
```
https://github.com/blackcubeio
```

Save block.

---

#### Card 3: Documentation

**Contents section** → Button **Add block** → Select **04. Card**

**Fields to fill**:

**icon** (required):
- Upload SVG file `book-open.svg`

**title** (required):
```
Documentation
```

**description** (required):
```
<p>Complete guides, tutorials and API references to master Blackcube CMS.</p>
```

**link** (optional):
```
Explore documentation >
```

**linkRoute** (required):
- Select in dropdown → **Sections** → **Guide** (EN)

Save block.

---

### Step 4: Fill Info Box

**Contents section** → Button **Add block** → Select **05. Info**

**Fields to fill**:

**type** (required):
- Select `info` in dropdown

**title** (required):
```
Response time
```

**content** (required):
```
<p>We strive to respond within 48 business hours.</p>
```

Save block.

---

### Step 5: Fill Contact Block

**Contents section** → Button **Add block** → Select **07. Contact**

**Fields to fill**:

**title** (required):
```
Send us a message
```

**successTitle** (required):
```
Message sent!
```

**success** (required):
```
<p>Thank you for your message. We'll get back to you as soon as possible.</p>
```

Save block.

---

### Step 6: URL Configuration

**URL tab**:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `en/contact`
4. Save

Final public URL: `/en/contact`

---

### Step 7: Sitemap Configuration

**Sitemap tab**:

1. **Active**: Checked
2. **Frequency**: `monthly`
3. **Priority**: `0.5`
4. Save

---

### Step 8: SEO Configuration

**SEO tab**:

1. **Active**: Checked
2. **Title**: `Contact - Blackcube CMS`
3. **Description**: `Contact the Blackcube team. Questions, suggestions or technical support, we're here to help.`
4. Save

---

## Link FR/EN Content (Continued)

Now that both Contact Composites exist, finalize linking.

### Update Hero FR

1. Edit **Contact** (FR) Composite
2. Edit **Hero (H1)** block
3. **lang** field: Select in dropdown → **Articles** → **Contact** (EN)
4. Save

### Update Hero EN

1. Edit **Contact** (EN) Composite
2. Edit **Hero (H1)** block
3. **lang** field: Select in dropdown → **Articles** → **Contact** (FR)
4. Save

---

## Validation

Check in **Management → Articles**:

**Created composites**:
- Contact (FR) - Type: Contact - Language: French
- Contact (EN) - Type: Contact - Language: English

**Blocks per Composite**:
- 1 Hero (H1)
- 3 Cards
- 1 Info
- 1 Contact
- Total: 6 blocks per page

**Configuration**:
- URLs configured: `/fr/contact` and `/en/contact`
- Sitemap enabled with monthly frequency
- SEO configured with title and description
- FR ↔ EN linking working via `lang` field