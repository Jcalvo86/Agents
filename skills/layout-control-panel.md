---
name: layout-control-panel
description: Layout architecture, spatial density, grid blueprints, tables, and form patterns for the CRM and Control Panel interfaces.
---

# Skill: Control Panel Layout & Spatial Design

## 1. Scope & Objective
This specification defines the structural rules, density metrics, layout grids, and component sizing for Control Panels, CRMs, and Dashboards.

It focuses purely on layout architecture, spatial constraints, and density optimization (separating structural rules from visual theme/color tokens found in `DESIGN.md`).

---

## 2. Density & Geometry Standards

### 2.1. Structural Sizing
* **Layout Archetype:** Compact / High-Density Data Interface.
* **Component Heights:**
  * **Standard Inputs / Selects:** `36px` to `38px` (Tailwind classes: `h-9` or `h-[38px]`).
  * **Compact Table Action Buttons:** `32px` (Tailwind class: `h-8`).
  * **Header / Toolbar Buttons:** `36px` to `38px`.
  * **Textarea Minimum Height:** `96px` (Tailwind class: `min-h-[24rem]` or `min-h-[96px]`).

### 2.2. Border Radius Limits
* **Cards & Containers:** `8px` to `12px` (Tailwind classes: `rounded-lg` or `rounded-xl`).
* **Inputs, Selects & Buttons:** `6px` to `8px` (Tailwind classes: `rounded-md`).
* **STRICT RULE:** **NEVER** use a border radius greater than `10px` (`rounded-xl` / `rounded-2xl`) on inputs, form controls, or compact buttons.

---

## 3. Form Layout & Grid Patterns

### 3.1. Multi-Column Responsive Grids
Short selection fields (dates, categories, status, numbers) **MUST** be grouped in multi-column grids to maximize horizontal space utility.

* **Standard Grid Blueprint:**
  ```html
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <!-- Fields here -->
  </div>
  ```

### 3.2. Form Labels & Field Layout
* Labels **MUST** use a `12px` or `13px` semi-bold font size (`text-xs font-semibold` or `text-sm font-semibold`), positioned directly above the field with a tight `6px` gap (`space-y-1.5`).
* Required fields **MUST** feature a red asterisk (`*`) immediately following the label text.

### 3.3. Coupled Input Groups
When an input requires an immediate action (e.g., Add Tag, Search, Filter), the button **MUST** be physically coupled with the input to form a single cohesive visual unit.

* **HTML/CSS Pattern:**
  ```html
  <div class="flex rounded-md shadow-sm">
    <input 
      type="text" 
      class="h-9 flex-1 rounded-l-md border border-r-0 border-slate-200 px-3 text-sm focus:outline-none focus:ring-1 focus:ring-indigo-500" 
      placeholder="Buscar..."
    />
    <button class="h-9 px-3.5 rounded-r-md border border-slate-200 bg-slate-50 text-xs font-semibold text-slate-700 hover:bg-slate-100">
      Acción
    </button>
  </div>
  ```

---

## 4. High-Density Data Tables

### 4.1. Table Structure & Cell Alignments
* **Row Height:** Compact (`44px` to `48px` max height per row).
* **Text Alignment Rules:**
  * **Text / Names / Categories** ➔ Left aligned (`text-left`)
  * **Dates / Timestamps / Status Badges** ➔ Center or Left aligned (`text-center` or `text-left`)
  * **Financial Amounts / Numbers / Quantities** ➔ **ALWAYS** Right aligned using monospaced tabular numbers (`text-right tabular-nums`).

### 4.2. Action Column Rules
* The **Actions** column **MUST** be right-aligned or fixed to the right.
* Primary actions per row should use compact icon buttons (`32px` / `h-8`) or a single primary button. Secondary actions **MUST** be grouped inside an action menu dropdown (`...`).

---

## 5. Summary KPI Cards & Layout Containers

### 5.1. Dashboard Header KPIs
* KPI Cards **MUST** use a 2 to 4-column responsive grid layout:
  ```html
  <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
    <!-- KPI cards -->
  </div>
  ```
* Metric values **MUST** use prominent bold typography (`20px` to `24px` / `text-xl` to `text-2xl font-bold`) with metadata/percentage changes placed underneath or aligned to the right.

### 5.2. Page Shell Structure
Every control panel page should respect the following grid layout structure:
1. **Sidebar Area:** Left column containing navigation, settings, and profile shortcuts.
2. **Top Bar:** Header area for breadcrumbs and global page actions.
3. **KPI Grid:** Top metrics container across 1, 2, or 4 columns.
4. **Main Content Area:** Central container for filter toolbars, data tables, and multi-column forms.
