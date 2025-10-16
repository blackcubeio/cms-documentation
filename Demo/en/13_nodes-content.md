# Section Preparation
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Back-Office Configuration

---

## Objective

Create the complete site tree structure in the back-office: root Node, FR/EN Nodes with their Heroes, container Nodes (Guide, About). This step structures the site and fills initial content.

---

## Configure Root Node

**Role**: Site entry point, detects browser language and redirects to /fr or /en.

**Access**: Sidebar menu → **Management → Sections**

> **Info**
> The Root Node was automatically created when running the `php yii bc:init` command. It already exists in the tree, just needs configuration.

### 1. Edit Node

1. In section list, click pencil to edit existing Root Node
2. Modify fields:
   - **Active**: Checked
   - **Type**: `Redirect Lang`
   - **Language**: `French`
   - **Name**: `Root` (if not already set)
3. Save

### 2. URL Configuration

After saving, click **URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: *Leave empty*
4. Save

Final public URL: `/`

> **Info**
> This Node contains no blocks because `Redirect Lang` Type allows no BlocTypes. Its only role is to redirect. No need to configure Sitemap or SEO as this Node does immediate redirect.

---

## Create Home FR/EN Nodes

### Node Home FR

**Role**: French home page with Hero, Features, and CTA.

#### 1. Node Creation

1. Button **Add section**
2. Fill fields:
   - **Active**: Checked
   - **Type**: `Home`
   - **Language**: `French`
   - **Name**: `FR`
   - **Path**: Hazel Path automatically generated (non-editable)
   - **Move**: Check
   - **Mode**: `Into`
   - **Target**: Select **Root**
3. Save

#### 2. URL Configuration

> **Info**
> In Blackcube, URL path is always **complete and absolute** for all elements (Nodes, Composites, Categories, Tags). Path is never automatically inherited from parent.

**URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `fr`
4. Save

#### 3. Sitemap Configuration

**Sitemap** tab:

1. **Active**: Checked
2. **Frequency**: `daily`
3. **Priority**: `1.0`
4. Save

#### 4. SEO Configuration

**SEO** tab:

1. **Active**: Checked
2. **Title**: `Blackcube CMS`
3. **Description**: `CMS for Yii2. Official French documentation.`
4. Save

Final public URL: `/fr`

---

### Node Home EN

**Role**: English home page with Hero, Features, and CTA.

#### 1. Node Creation

1. Button **Add section**
2. Fill fields:
   - **Active**: Checked
   - **Type**: `Home`
   - **Language**: `English`
   - **Name**: `EN`
   - **Path**: Hazel Path automatically generated (non-editable)
   - **Move**: Check
   - **Mode**: `Into`
   - **Target**: Select **Root**
3. Save

#### 2. URL Configuration

**URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `en`
4. Save

#### 3. Sitemap Configuration

**Sitemap** tab:

1. **Active**: Checked
2. **Frequency**: `daily`
3. **Priority**: `1.0`
4. Save

#### 4. SEO Configuration

**SEO** tab:

1. **Active**: Checked
2. **Title**: `Blackcube CMS`
3. **Description**: `CMS for Yii2. Official English documentation.`
4. Save

Final public URL: `/en`

---

## Fill Home FR/EN Heroes

### Add Hero FR

1. Edit **FR** Node (click pencil in section list)
2. At bottom of page, **Contents** section: Select **01. Hero (H1)** in dropdown → Click **+** button
3. Hero block displays with its fields. Fill:

**lang**: *Leave empty* (to fill after creating Hero EN)

**title**:
```
Blackcube CMS
```

**description**:
```
Flexible and performant CMS for Yii2. Build custom sites with modular architecture and powerful block system.
```

**image**: Upload PNG/JPG background image (optional)

**ctaTitle**:
```
Documentation
```

**ctaTarget**: Select in dropdown → **Sections** → **Guide FR**

**ctaSecondaryTitle**:
```
Github
```

**ctaSecondaryTarget**: Enter route:
```
redirect/github
```

4. Save Node

---

### Add Hero EN

1. Edit **EN** Node
2. At bottom of page, **Contents** section: Select **01. Hero (H1)** in dropdown → Click **+** button
3. Hero block displays with its fields. Fill:

**lang**: *Leave empty* (to fill after)

**title**:
```
Blackcube CMS
```

**description**:
```
Flexible and performant CMS for Yii2. Build custom websites with a modular architecture and powerful block system.
```

**image**: Upload same image as FR (or EN variant)

**ctaTitle**:
```
Documentation
```

**ctaTarget**: Select in dropdown → **Sections** → **Guide EN**

**ctaSecondaryTitle**:
```
Github
```

**ctaSecondaryTarget**: Enter route:
```
redirect/github
```

4. Save Node

---

## Link FR/EN Content

**Objective**: Enable language switcher to work by linking FR and EN Heroes via `lang` field.

### Link Hero FR → Hero EN

1. Edit **FR** Node
2. In existing **Hero (H1)** block, modify **lang** field: Select in dropdown → **Sections** → **EN**
3. Save Node

### Link Hero EN → Hero FR

1. Edit **EN** Node
2. In existing **Hero (H1)** block, modify **lang** field: Select in dropdown → **Sections** → **FR**
3. Save Node

> **Info**
> The `lang` field allows `HrefLangBehavior` to automatically generate SEO `<link rel="alternate" hreflang="...">` tags and makes the language switcher work.

---

## Create Container Nodes

### Node Guide FR

**Role**: Container for French documentation articles. Redirects to first active article.

#### 1. Node Creation

1. Button **Add section**
2. Fill fields:
   - **Active**: Checked
   - **Type**: `Redirect First Composite`
   - **Language**: `French`
   - **Name**: `Guide`
   - **Path**: Hazel Path automatically generated (non-editable)
   - **Move**: Check
   - **Mode**: `Into`
   - **Target**: Select **FR**
3. Save

#### 2. URL Configuration

**URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `fr/guide`
4. Save

Final public URL: `/fr/guide` → will redirect to `/fr/guide/first-article`

> **Info**
> This Node redirects to first active article. No need to configure Sitemap or SEO as this Node never displays content.

---

### Node Guide EN

#### 1. Node Creation

1. Button **Add section**
2. Fill fields:
   - **Active**: Checked
   - **Type**: `Redirect First Composite`
   - **Language**: `English`
   - **Name**: `Guide`
   - **Path**: Hazel Path automatically generated (non-editable)
   - **Move**: Check
   - **Mode**: `Into`
   - **Target**: Select **EN**
3. Save

#### 2. URL Configuration

**URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `en/guide`
4. Save

Final public URL: `/en/guide` → will redirect to `/en/guide/first-article`

> **Info**
> This Node redirects to first active article. No need to configure Sitemap or SEO as this Node never displays content.

---

### Node About FR

**Role**: Container for "About" pages. Redirects to first active article.

#### 1. Node Creation

1. Button **Add section**
2. Fill fields:
   - **Active**: Checked
   - **Type**: `Redirect First Composite`
   - **Language**: `French`
   - **Name**: `About`
   - **Path**: Hazel Path automatically generated (non-editable)
   - **Move**: Check
   - **Mode**: `Into`
   - **Target**: Select **FR**
3. Save

#### 2. URL Configuration

**URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `fr/a-propos`
4. Save

Final public URL: `/fr/a-propos` → will redirect to `/fr/a-propos/why-blackcube`

> **Info**
> This Node redirects to first active article. No need to configure Sitemap or SEO as this Node never displays content.

---

### Node About EN

#### 1. Node Creation

1. Button **Add section**
2. Fill fields:
   - **Active**: Checked
   - **Type**: `Redirect First Composite`
   - **Language**: `English`
   - **Name**: `About`
   - **Path**: Hazel Path automatically generated (non-editable)
   - **Move**: Check
   - **Mode**: `Into`
   - **Target**: Select **EN**
3. Save

#### 2. URL Configuration

**URL** tab:

1. **Active**: Checked
2. **Host**: `*`
3. **Path**: `en/about`
4. Save

Final public URL: `/en/about` → will redirect to `/en/about/why-blackcube`

> **Info**
> This Node redirects to first active article. No need to configure Sitemap or SEO as this Node never displays content.

---

## Validation

Check complete tree in **Management → Sections**.

**Expected structure**:

```
Root (/)
├── FR (/fr)
│   ├── Guide (/fr/guide)
│   └── About (/fr/a-propos)
└── EN (/en)
    ├── Guide (/en/guide)
    └── About (/en/about)
```

**Verifications**:

- 7 Nodes created: 1 root + 2 home + 4 containers
- 2 Heroes filled (FR + EN) with all required fields
- FR ↔ EN linking working via `lang` field
- URLs configured for all Nodes
- Sitemap enabled only for FR and EN (not for redirects)
- SEO configured only for FR and EN (not for redirects)

**Front-end test**:

Access site in preview mode (eye icon in back-office header):

- Access `/` → should redirect to `/fr` or `/en` based on browser language
- Access `/fr` → should display French home page
- Access `/en` → should display English home page
- Language switcher works (FR ↔ EN)
- Access `/fr/guide` or `/fr/a-propos` → 404 error (normal, no articles created yet in these sections)