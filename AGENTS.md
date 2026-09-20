---
author: Javier Calvo
date: 2026-08-13
---

## 4. Indicador de Creador (Creator Indicator)
* **Visualización de la Autoría:** Para mantener un control óptimo de "Human-in-the-Loop", cualquier elemento creado dinámicamente en el sistema (ej. componentes, entregables, paquetes de trabajo o actividades) debe ir acompañado del componente `CreatorIndicator`.
* **Especificaciones del Indicador:**
  * **Humano:** Mostrar un avatar circular con fondo de color generado consistentemente según sus iniciales (derivado de su nombre o prefijo de email antes de `@`), a menos que se provea una imagen de perfil.
    * **Fórmula de Color Dinámico (stringToHslColor):** Todo avatar autogenerado debe usar la función exportada desde `@/components/CreatorIndicator` (o utilizar su misma lógica: generar un hash del string, convertir el valor a un ángulo de Hue entre 0-360, y devolver `hsl(H, 60%, 45%)` para asegurar un color armónico y legible en fondos oscuros).
  * **IA (Inteligencia Artificial):** Mostrar una píldora con fondo degradado púrpura/índigo, un icono de estrellas de 4 puntas (`Sparkles` o robot `Bot`) y el texto "IA".
  * **Preconfiguración:** Mostrar una píldora azul/cyan con el icono de un rompecabezas/puzzle (`Puzzle`) y el nombre de la plantilla.

---

# Agent Context & System Router (PM Helper)

## 1. Executive Summary
- **Project:** PM Helper (GI)
- **Architecture:** Next.js 14+ (App Router), TypeScript, Supabase, Tailwind CSS.
- **Goal:** Manage Control Panel and Project Helpers.

## 2. Agent Roles & File Scopes

| Agent Role | Primary Responsibility | Allowed Paths | Context Specs |
| :--- | :--- | :--- | :--- |
| `@ui-agent` | Layouts, UI Components, Tailwind, Design System | `src/app/`, `src/components/`, `public/` | Read `.agents/skills/design-foundations.md`, active project `DESIGN.md`, `.agents/skills/ui-patterns.md` |
| `@db-agent` | Schemas, Migrations, API Routes, RLS Policies | `supabase/`, `src/lib/`, `src/app/api/` | Read `.agents/skills/supabase-crud.md` |
| `@qa-agent` | Linting, Type Checking, E2E & Unit Tests | `tests/`, `*.test.ts`, `*.config.*` | Read `.agents/ANTIGRAVITY.md` |

## 3. Global System Rules (STRICT)

### Core Directives
- **ALWAYS:** Enforce TypeScript Strict Mode. No `any` types allowed.
- **ALWAYS:** Align numeric/financial amounts to the right using `tabular-nums` class.
- **ALWAYS:** Default to React Server Components unless client state (`useState`, `useEffect`) is mandatory.
- **NEVER:** Hardcode inline styles or use arbitrary Tailwind values (e.g., `w-[234px]`).
- **NEVER:** Import `@supabase/supabase-js` directly in UI components; use SSR client wrapper `@/lib/supabase/client`.

### Component Reuse & Standardization
- **NEVER re-code styles for library components:** Reusable UI components from `.agents/components_library/SKILL.md` (e.g., `HoldToConfirmButton`) must define and encapsulate their visual styles internally. When utilizing them, agents MUST use the component's internal design configuration or variant props (configured to match the active project's `DESIGN.md` guidelines) and **NEVER** apply custom Tailwind classes or raw inline CSS classes in the parent view to redefine their appearance.

### Component & File Constraints
- **MAX COMPONENT SIZE:** No component or file in `src/` should exceed **150-200 lines of code**.
- **DECOMPOSITION RULE:** If a component reaches >150 lines, STOP writing feature code immediately and trigger the Refactoring Skill (`.agents/skills/component-refactoring.md`).

## 4. Pre-Flight Protocol: "Check Before Write" (MANDATORY)

Before generating, editing, or refactoring ANY file, every agent MUST execute these steps silently:

1. **Check File Metrics:** Measure lines of code (`wc -l`). If target file > 150 lines, trigger `component-refactoring.md`.
2. **Scan Skills Index:** Check Section 5 below to identify if a recipe/skill matches the task intent.
3. **Declare Applied Skill:** State in 1 line which skill is being loaded before printing code output (e.g., *"Using skill: component-refactoring.md"*).

## 5. Automatic Skill Triggers & Auto-Routing

Inspect the user prompt and file target to auto-load modules **only on demand**:

| Condition / Symptom | Action / Required Skill | Scope |
| :--- | :--- | :--- |
| Starting a new session, onboarding or setting up workspace tools | ➔ Read `.agents/skills/environment-setup.md` | Global / Workspace |
| Target file is > 150 LOC or JSX is overly complex | ➔ ALWAYS read `.agents/skills/component-refactoring.md` | `src/components/` |
| Writing DB queries, Supabase actions, or migrations | ➔ Read `.agents/skills/supabase-crud.md` | `supabase/`, `src/lib/` |
| Building, testing, linting, or executing CLI commands | ➔ Read `.agents/ANTIGRAVITY.md` | Root / Terminal |
| Reviewing or recording system architecture shifts | ➔ Read/Write `.agents/decisions/` | `.agents/decisions/` |
| Sizing, animations, grids, spacing, or icon hitboxes | ➔ Read `.agents/skills/design-foundations.md` | Global / UI |
| Applying brand colors, typography, or style tokens | ➔ Read `<active-project>/DESIGN.md` | Global / Brand |
| Building or editing Dashboard / Control Panel layouts, tables, or forms | ➔ Read `.agents/skills/layout-control-panel.md` y `.agents/skills/ui-patterns.md` | `src/app/dashboard/`, `src/components/` |
| Building or editing landing pages or public web views | ➔ Read `.agents/skills/layout-landingPage_PublicWeb.md` | `src/app/public/`, `src/components/` |
| Implementing file uploads or hold-to-confirm buttons | ➔ Read `.agents/components_library/SKILL.md` (y sus referencias) | `src/components/` |
| Generating retro maps, icon sheets, or travel JSON data | ➔ Read `.agents/SuenoTravel/` (relevant skill) | `src/data/`, `src/prompts/` |

## 6. Skills Quick-Index Library

### Global Skills (`.agents/skills/`)
- `environment-setup.md`: Protocolo de inicio del ambiente de trabajo (Agentation, submódulos de Git e integración visual).
- `design-foundations.md`: Cimientos globales de diseño creados por Tomas Bravo (escala de animación, rejilla base-4, tamaño mínimo de hitboxes).
- `component-refactoring.md`: Rules for decomposing large files (>150 LOC) into custom hooks and atomic sub-components.
- `ui-patterns.md`: Guidelines for forms, autocompletes, accordions, and tables in the Control Panel.
- `supabase-crud.md`: Safe SSR data fetching, Zod schema validations, and RLS policy setup.
- `layout-control-panel.md`: Layout architecture, grids, spatial density standards for Control Panels.
- `layout-landingPage_PublicWeb.md`: Design principles, margins, grids, and CTA sizing for Public Web / Landings.

### Reusable UI Components (`.agents/components_library/`)
- `components_library/SKILL.md`: Reference index for premium reusable components (`ImageUploader`, `HoldToConfirmButton`).

### Project-Specific Skills
*   **Sueño Travel (`.agents/SuenoTravel/`)**:
    - `travel-data-generator`: Strictly formats region and location JSON files.
    - `map-prompt-architect`: Midjourney/Flux prompts for vintage map backgrounds.
    - `icon-sheet-architect`: Asset sheet prompts for isolated architectural landmark icons.

## 7. Task Completion Protocol (Definition of Done)

Before marking any task as resolved, every agent MUST execute this checklist:

1. **Verify Types:** Ensure zero TypeScript errors (`npm run type-check`).
2. **Verify Formatting:** Ensure zero ESLint warnings on modified files.
3. **Audit File Sizes:** Verify no newly created/edited file exceeds 150 LOC.
4. **Audit Context Drift:**
   - Did you add/modify UI tokens or component guidelines? ➔ Update global `design-foundations.md` or active project `DESIGN.md`.
   - Did you change an architectural decision or schema convention? ➔ Append a 5-line summary ADR in `.agents/decisions/`.
