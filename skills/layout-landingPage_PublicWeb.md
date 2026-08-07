---
name: layout-landingPage_PublicWeb
description: Layout, spacing, section hierarchy, conversion flows, and responsive behavior for Landing Pages and Public Web interfaces.
---

# Skill: Landing Page & Public Web Layout Design

## 1. Scope & Objective
This specification defines the structural rules, spacing metrics, section hierarchy, and component scales for Landing Pages, Marketing Websites, and Product Sales pages.

It focuses purely on layout architecture, conversion flow, and visual breathing space (separating structural rules from brand colors and theme tokens in `DESIGN.md`).

---

## 2. Layout & Spacing Principles

### 2.1. Structural Density vs. Control Panel
* **Density Profile:** Low Density / High Whitespace (Generous padding to guide user focus).
* **Section Padding (Vertical):** `64px` to `112px` (Tailwind classes: `py-16` to `py-28`).
* **Container Max-Width:**
  * **Standard Content:** `1280px` (Tailwind class: `max-w-7xl`).
  * **Text / Copy Centric Sections:** `768px` (Tailwind class: `max-w-3xl`).

### 2.2. Component Heights & Curves
* **Primary CTAs (Buttons):** `48px` to `56px` (Tailwind classes: `h-12` or `h-14`).
* **Inputs (Lead Capture / Newsletter):** `48px` (Tailwind class: `h-12`).
* **Border Radius Limits:**
  * **Cards & Feature Blocks:** `16px` to `24px` (Tailwind classes: `rounded-2xl` or `rounded-3xl`).
  * **Buttons & Inputs:** `8px` to `9999px` (Tailwind classes: `rounded-lg` or `rounded-full`).

---

## 3. Section Hierarchy & Conversion Flow
A landing page **MUST** follow a logical narrative structure from top to bottom:

```
+-------------------------------------------------------------------------------+
| HEADER / NAVBAR: Logo | Nav Links | Secondary CTA | Primary CTA               |
+-------------------------------------------------------------------------------+
| HERO SECTION: H1 Title + Subtitle + Primary CTA + Product Preview / Hero Image |
+-------------------------------------------------------------------------------+
| SOCIAL PROOF / TRUST BADGES: Client Logos or Key Metrics                     |
+-------------------------------------------------------------------------------+
| VALUE PROPOSITION / FEATURES GRID: 3-Column Benefits or Alternating Rows      |
+-------------------------------------------------------------------------------+
| DEMO / PRODUCT INTERACTION: Interactive Preview or Video Slot                 |
+-------------------------------------------------------------------------------+
| PRICING TABLES: Tiers / Plans with Highlights                                |
+-------------------------------------------------------------------------------+
| TESTIMONIALS / FAQ: Social Validation & Friction Removal                      |
+-------------------------------------------------------------------------------+
| FINAL CALL TO ACTION (CTA BANNER): High Impact Lead Capture                   |
+-------------------------------------------------------------------------------+
| FOOTER: Links | Legal | Social Icons                                         |
+-------------------------------------------------------------------------------+
```

---

## 4. Key Component Layout Standards

### 4.1. Hero Section Architecture
* **Layout Structure:**
  * **Option A (Centered):** H1 Title (Max 60 chars) + Subtitle (Max 160 chars) + CTA Group + Large Image/Video Mockup underneath.
  * **Option B (Split 2-Column):** Left side copy/CTAs (col-span 6) + Right side high-impact product graphic (col-span 6).
* **Hero CTA Coupling:** Primary button (`h-12` or `h-14`) paired with a secondary text/icon link (e.g., "Watch Demo" or "Explore Features").

### 4.2. Feature Grids (Value Proposition)
* **3-Column Feature Cards:**
  ```html
  <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
    <!-- Feature cards -->
  </div>
  ```
* **Alternating Z-Pattern (Feature Focus):** Image on left / Text on right, swapping sides on every row.
* **Card Structure:** Icon/Illustration top ➔ Bold Heading (`h3`) ➔ Paragraph body text (`text-slate-600` / `text-muted-foreground`).

### 4.3. Pricing Cards Layout
* **Grid Blueprint:**
  ```html
  <div class="grid grid-cols-1 md:grid-cols-3 gap-8 items-stretch">
    <!-- Pricing plans -->
  </div>
  ```
* **Highlighted Plan (Popular Tier):** **MUST** feature a visual distinction (e.g., subtle elevation/shadow, colored border, or a "Most Popular" top badge) and be slightly scaled or vertically offset.
* **Button Placement:** Pricing action buttons **MUST** be anchored at the bottom of the card.

---

## 5. Responsive Behavior Rules
For screens below the **Mobile Breakpoint** (`<768px`):
* All multi-column grids collapse into a single vertical column (`grid-cols-1`).
* Hero image moves **BELOW** the copy and CTAs.
* Navbar menu collapses into a clean Sheet / Hamburger drawer.
* Buttons become full width (`w-full`).
