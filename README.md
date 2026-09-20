# Documentación del Submódulo `.agents` (Antigravity Customization System)

Este directorio contiene las personalizaciones, reglas y flujos de trabajo que el sistema de Agentes de Inteligencia Artificial (Antigravity) utiliza para entender el contexto del proyecto y actuar de manera alineada a los estándares de **PM Helper**.

El sistema descubre y carga estos archivos automáticamente para pasar de ser un asistente genérico a un experto integrado en nuestra base de código.

---

## 🏗️ Arquitectura de la Carpeta

### 1. Reglas Globales (Rules)
* **Archivos clave:** `AGENTS.md` o cualquier Markdown dentro de `.agents/rules/`.
* **Uso:** Estos archivos definen **restricciones obligatorias**, estilos de arquitectura, directrices de código y comandos permitidos.
* **Carga:** Son reglas "Always On" (o cargadas jerárquicamente por directorio). El agente las lee de forma automática al abrir archivos del proyecto, sin necesidad de que el usuario las pida explícitamente.

### 2. Habilidades Específicas (Skills)
* **Ubicación:** `.agents/skills/<nombre-del-skill>/SKILL.md`
* **Uso:** Actúan como manuales de procedimientos paso a paso. Se utilizan para enseñarle al agente flujos complejos (ej. *Cómo crear un componente de UI*, *Cómo hacer setup del entorno*).
* **Formato Obligatorio:** Todo archivo `SKILL.md` debe incluir un encabezado YAML (Frontmatter) en la parte superior con el nombre y la descripción de la habilidad. Ejemplo:
  ```yaml
  ---
  name: nombre-del-skill
  description: Descripción detallada de qué hace y cuándo debe usarse esta habilidad.
  ---
  ```
* **Carga (Progressive Disclosure):** Para no saturar la memoria, el sistema solo carga los nombres y descripciones de los skills de fondo. Cuando el agente detecta que la tarea actual requiere esta habilidad, **lee dinámicamente** el contenido completo del archivo.

### 3. Registro Manual (`skills.json`)
* **Uso:** Archivo JSON que permite registrar explícitamente rutas de habilidades o directorios que no siguen la estructura estándar o que están profundamente anidados (ej. `SuenoTravel/icon-sheet-architect`).

### 4. Plugins y MCP (Avanzado)
* Si se requieren integraciones con herramientas externas o agrupar múltiples reglas y skills, se utilizan los directorios `.agents/plugins/` (con un `plugin.json`) y configuraciones de servidores `mcp_config.json`.

---

## 📝 Mejores Prácticas para Redactar Documentos para Agentes

1. **Sé directo y estricto:** Los agentes responden mejor a instrucciones claras y absolutas (usa palabras como "SIEMPRE", "NUNCA", "DEBES").
2. **Usa Frontmatter en los Skills:** Es la única forma de que el router del sistema indexe tu habilidad correctamente.
3. **Mantenlo atómico:** Es mejor tener 3 skills pequeños y específicos que un solo archivo gigante y confuso.
4. **Referencia cruzada:** Puedes pedirle a un agente dentro de una regla global (`AGENTS.md`) que cargue un skill específico bajo ciertas condiciones (ej. "Si ves un archivo mayor a 150 líneas, carga el skill *component-refactoring*").
