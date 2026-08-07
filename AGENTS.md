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
