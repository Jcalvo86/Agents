# Estándares de Diseño y Comportamiento de Filtros en Alexandria

Cualquier agente que trabaje en esta base de código debe seguir estas convenciones para mantener la coherencia de la experiencia de usuario (UX):

## 1. Comportamiento de los Filtros en Botones (Chips)
* **Sin botón "Todos":** En los listados de filtros principales (como áreas de trabajo o necesidades de resultados), se debe omitir un botón explícito de "Todos".
* **Lógica Toggle-to-All:**
  * Si ningún chip está seleccionado, se asume implícitamente que se muestran todos los elementos.
  * Al hacer clic en un filtro activo, este se desactiva (toggle) volviendo a mostrar todos los elementos.
* **Filtros Dependientes Dinámicos:**
  * Los selectores secundarios (como tipo de contenido o necesidades específicas) deben calcularse de forma dinámica (`useMemo`) a partir del conjunto de datos filtrado por el término de búsqueda actual.
  * Si el valor seleccionado en un filtro secundario deja de estar disponible debido a cambios en la búsqueda, se debe restablecer automáticamente a su estado general (`'all'`).

## 2. Modelo de Categorización en Base de Datos (Supabase)
* **Categorías como JSON:** La columna `category` de la tabla `terms` se almacena como una cadena JSON serializada para evitar migraciones de base de datos frecuentes.
* **Estructura Esperada:**
  ```json
  {
    "workArea": "codigo | diseño | mrkt | gestion",
    "contentType": "herramienta | framework | regla | metodologia",
    "targetResult": "reducir_tokens | no_parezca_ai | crear_marca | otro"
  }
  ```
* **Función de Lectura (parseCategory):** Siempre se debe utilizar e implementar la función `parseCategory` para asegurar compatibilidad con registros antiguos (cadenas de texto plano) y aplicar valores por defecto de forma segura.

## 3. Convención de Nomenclatura del Panel de Control
* **Uso del término "Concepto" (Concept):** En lugar de utilizar términos genéricos o ambiguos como "Entrada" (Entry), se debe emplear de manera consistente el término **"Concepto"** (y **"Conceptos"** para el plural) en todos los títulos, botones, pestañas y mensajes del panel de control. Esto proporciona una mejor semántica del contenido que se cataloga (metodologías, guías, herramientas, frameworks, reglas, etc.) diferenciándolo claramente de otros módulos como el "UI Kit".

---

# Agent Context & System Router (CRM Control Panel)

## 1. Executive Summary
- **Project:** Alexandria Control Panel
- **Architecture:** Next.js 14+ (App Router), TypeScript, Supabase, Tailwind CSS.
- **Goal:** Manage Control Panel.

## 2. Agent Roles & File Scopes

| Agent Role | Primary Responsibility | Allowed Paths | Context Specs |
| :--- | :--- | :--- | :--- |
| `@ui-agent` | Layouts, UI Components, Tailwind, Design System | `src/app/`, `src/components/`, `public/` | Read `.agents/DESIGN.md`, `.agents/skills/rules.md` |
| `@db-agent` | Schemas, Migrations, API Routes, RLS Policies | `supabase/`, `src/lib/`, `src/app/api/` | Read `.agents/skills/supabase-crud.md` |
| `@qa-agent` | Linting, Type Checking, E2E & Unit Tests | `tests/`, `*.test.ts`, `*.config.*` | Read `.agents/ANTIGRAVITY.md` |

## 3. Global System Rules (STRICT)

### Core Directives
- **ALWAYS:** Enforce TypeScript Strict Mode. No `any` types allowed.
- **ALWAYS:** Align numeric/financial amounts to the right using `tabular-nums` class.
- **ALWAYS:** Default to React Server Components unless client state (`useState`, `useEffect`) is mandatory.
- **NEVER:** Hardcode inline styles or use arbitrary Tailwind values (e.g., `w-[234px]`).
- **NEVER:** Import `@supabase/supabase-js` directly in UI components; use SSR client wrapper `@/lib/supabase/client`.

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
| Target file is > 150 LOC or JSX is overly complex | ➔ ALWAYS read `.agents/skills/component-refactoring.md` | `src/components/` |
| Writing DB queries, Supabase actions, or migrations | ➔ Read `.agents/skills/supabase-crud.md` | `supabase/`, `src/lib/` |
| Building, testing, linting, or executing CLI commands | ➔ Read `.agents/ANTIGRAVITY.md` | Root / Terminal |
| Reviewing or recording system architecture shifts | ➔ Read/Write `.agents/decisions/` | `.agents/decisions/` |
| Building or editing Dashboard / Control Panel layouts, tables, or forms | ➔ Read `.agents/skills/layout-control-panel.md` | `src/app/dashboard/`, `src/components/` |
| Defining brand colors, themes, or status badges | ➔ Read `.agents/DESIGN.md` | Global |

## 6. Skills Quick-Index Library

- `component-refactoring.md`: Rules for decomposing large files (>150 LOC) into custom hooks and atomic sub-components.
- `supabase-crud.md`: Safe SSR data fetching, Zod schema validations, and RLS policy setup.
- `DESIGN.md`: Color tokens, typography scales, card layouts, and status pill badges for AntEater UI.

## 7. Task Completion Protocol (Definition of Done)

Before marking any task as resolved, every agent MUST execute this checklist:

1. **Verify Types:** Ensure zero TypeScript errors (`npm run type-check`).
2. **Verify Formatting:** Ensure zero ESLint warnings on modified files.
3. **Audit File Sizes:** Verify no newly created/edited file exceeds 150 LOC.
4. **Audit Context Drift:**
   - Did you add/modify UI tokens or component guidelines? ➔ Update `.agents/DESIGN.md`.
   - Did you change an architectural decision or schema convention? ➔ Append a 5-line summary ADR in `.agents/decisions/`.
