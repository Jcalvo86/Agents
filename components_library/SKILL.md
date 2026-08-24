---
name: components_library
description: Biblioteca de componentes premium reutilizables del panel de control de PM Helper. Contiene la lista y guías de uso de componentes como ImageUploader, HoldToConfirmButton e InlineAutoSave.
---

# Biblioteca de Componentes Reutilizables (CRM / Control Panel)

Esta es la biblioteca de referencia de componentes de interfaz que ya existen en el proyecto. 
**Antes de crear un nuevo componente o utilizar un input básico, consulta esta biblioteca para mantener la consistencia estética y de comportamiento.**

## Componentes Disponibles

1. **[ImageUploader](file:///c:/andreia/PM%20Helper/.agents/components_library/references/ImageUploader.md)**:
   Componente premium de carga de archivos e imágenes con drag-and-drop, estadísticas de velocidad/tiempo restante en tiempo real, alertas de error con botón de reintento y previsualización editable.
   
2. **[HoldToConfirmButton](file:///c:/andreia/PM%20Helper/.agents/components_library/references/HoldToConfirmButton.md)**:
   Botón interactivo de confirmación por retención para acciones críticas (como borrados). Requiere mantener presionado durante 1.5 segundos mostrando animación circular de carga.

3. **[PasswordStrengthInput](file:///c:/andreia/PM%20Helper/.agents/components_library/references/PasswordStrengthInput.md)**:
   Campo de entrada de contraseña interactivo con visualizador de robustez de 4 niveles y validación interactiva de requisitos en tiempo real.

4. **[InlineAutoSave](file:///c:/andreia/PM%20Helper/.agents/components_library/references/InlineAutoSave.md)**:
   Patrón de interacción para edición en línea silenciosa de campos de texto mediante temporización debounce de 800ms, optimizando la sincronización con Firebase en segundo plano.

---

## Directrices para el Agente
- Utiliza siempre los componentes descritos aquí cuando la interfaz requiera estas acciones.
- Los archivos físicos de código se ubican en `src/components/`.

