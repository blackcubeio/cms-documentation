# Types Planning
## Development Guide - Blackcube CMS Documentation Site

**Prerequisites**: Logical Breakdown

---

## Objective

Define the project's content Types and identify which BlocTypes each Type can use. This planning structures content organization in the back-office.

---

## What is a Type?

A Type is a page template that associates:
- **Allowed BlocTypes**: Which blocks can be added
- **A route**: Which controller/action will display the content

## Type vs BlocType Difference

**BlocType**: Data structure (input fields)
**Type**: Page template (BlocType combination + controller)

---

## Types to Create

### Type 1: Home

**Usage**: FR/EN home pages

**Route**: `cms/home`

**Allowed BlocTypes**:
- Hero (H1)
- Feature (H3)
- Cta

**Justification**:
- Hero for main header with image and 2 CTAs
- Features to present functionalities in 3-column grid
- Cta for final conversion

**Article allowed**: Checked

---

### Type 2: Documentation

**Usage**: Technical documentation articles

**Route**: `cms/documentation`

**Allowed BlocTypes**:
- Hero (H1)
- Title (H2 - H4)
- Content
- Code
- Info
- Feature (H3)
- Cta
- Card

**Justification**:
- Hero for page header
- Title to structure content (H2, H3, H4)
- Content for paragraphs
- Code for examples
- Info for alerts
- Optional Feature for highlights
- Cta for navigation
- Optional Card for insets

**Article allowed**: Checked

---

### Type 3: Contact

**Usage**: FR/EN contact pages

**Route**: `cms/contact`

**Allowed BlocTypes**:
- Hero (H1)
- Card
- Info
- Contact

**Justification**:
- Hero for header
- Card for contact info in sidebar
- Info for potential alerts
- Contact to configure form

**Article allowed**: Checked

---

### Type 4: Redirect Language

**Usage**: Root node for language detection

**Route**: `cms/redirect-language`

**Allowed BlocTypes**: None

**Justification**:
- This Type contains no content
- It automatically redirects to /fr or /en based on browser language

**Article allowed**: Not checked

---

### Type 5: Redirect First Composite

**Usage**: Container nodes (Guide, About)

**Route**: `cms/redirect-first-composite`

**Allowed BlocTypes**: None

**Justification**:
- This Type contains no content
- It automatically redirects to first active Composite of Node

**Article allowed**: Not checked

---

## Allowed BlocTypes by Type

### Type Home
- Hero
- Feature
- Cta

### Type Documentation
- Hero
- Feature
- Title
- Content
- Code
- Info
- Cta
- Card

### Type Contact
- Hero
- Card
- Info
- Contact

### Type Redirect Language
No BlocType

### Type Redirect First Composite
No BlocType

---

## Restriction Logic

### Types with Limited Content

**Home**: Only Hero + Features + Cta
- Voluntary limitation to keep homogeneous format
- No rich content to stay on presentation page

**Contact**: Only Hero + Cards + Info + Contact
- Fixed structure: form + info sidebar
- No rich content as utility page

### Types with Rich Content

**Documentation**: Almost all BlocTypes
- Maximum flexibility to create varied technical content
- Code support, alerts, advanced formatting

### Types without Content

**Redirect Language** and **Redirect First Composite**: No BlocType
- Purely technical, no editorial content
- Only automatic redirects

---

## Validation

Before continuing, verify:

**Defined types**:
- Home (3 BlocTypes)
- Documentation (8 BlocTypes)
- Contact (4 BlocTypes)
- Redirect Language (0 BlocType)
- Redirect First Composite (0 BlocType)

**Total**: 5 Types

**Complete matrix**: Each Type has its allowed BlocTypes clearly identified