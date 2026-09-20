---
name: design-foundations
description: Cimientos globales del sistema de diseño visual, escala tipográfica, ritmo de espaciado base-4, hitboxes de iconos y escala de animaciones.
author: Tomas Bravo
date: 2026-08-13
---

# Cimientos del Sistema de Diseño (Design Foundations)

## 1. Escala Tipográfica y Color (Typography Scale & Color)
- **Tamaños de Fuente:** Utilizar únicamente **5 tamaños de fuentes** en todo el proyecto. Cada tamaño debe estar separado por un factor de **1.25** respecto al anterior, creando una escala modular estricta (ej: 13px, 16px, 20px, 25px, 31px/32px).
- **Colores de Tinta:** Usar solamente **3 tintas**, respetando siempre lo que indique el archivo `DESIGN.md` del proyecto activo:
  - **Primaria:** Para textos principales y alta legibilidad (ej. `#181818`).
  - **Secundaria:** Un poco más clara para jerarquía secundaria (ej. `#52525B`).
  - **Suave:** Para textos de apoyo, placeholders o metadatos (ej. `#A1A1A1`).
- **Pesos de Fuente (Font Weights):** Utilizar solamente **2 pesos**:
  - `400` (Regular): Para lectura de párrafos y textos largos.
  - `600` (Semi-bold o Bold): Para títulos, etiquetas y escaneo rápido del usuario.
- **Interlineado (Leading/Line-height):** Utilizar un leading *inversamente proporcional* al tamaño de la tipografía para asegurar la coherencia:
  - **Tamaños grandes (ej. 25px - 32px):** Usar un leading ajustado de **1.10** o **1.20** (ej. `leading-tight`).
  - **Tamaños pequeños/lectura (ej. 13px - 16px):** Usar un leading amplio de **1.50** (ej. `leading-normal` o `leading-relaxed`) para mejor legibilidad.

## 2. Espaciado y Rejilla (Spacing & Grid)
- **Ritmo de Espaciado:** Se utiliza una unidad base de `4px` para crear ritmo vertical y horizontal. Todos los márgenes, rellenos (paddings) y separaciones (gaps) deben ser múltiplos de 4 (ej: 4, 8, 12, 16, 24, 32, 48px).
- **Sistema de Grid (12 Columnas):** Usar un sistema de rejilla de 12 columnas para asegurar un espaciado consistente entre vistas:
  - Columnas: 12
  - Espacio de separación (Gutter): 16-24px
  - Margen lateral: 24-48px

## 3. Estados de Componentes (UI Components)
Los componentes son las piezas fundamentales. Define siempre todos sus estados interactivos: por defecto (default), al pasar el cursor (hover), activo (active), deshabilitado (disabled) y foco (focus).

## 4. Iconografía y Estilo (Icons & Iconography)
Los iconos deben ser consistentes en estilo, grosor y tamaño. Elige una sola biblioteca para cada proyecto y mantente fiel a ella.
- **Reglas de Estilo de Iconos:**
  - Grosor de línea consistente de 2px.
  - Extremos y uniones redondeadas (Rounded caps & joins).
  - Alineación óptica en la rejilla.
  - Caja contenedora (bounding box) por defecto de 24×24px.
  - Utilizar `currentColor` para los rellenos y trazos.
- **Regla de Accesibilidad Táctil:** Aunque el icono visualmente mida 20px, su área interactiva/táctil (hitbox) debe ser de al menos **44×44px** para cumplir con los estándares de accesibilidad táctil.
- **Sets de Iconos Recomendados:**
  - **Lucide:** Limpio, consistente, open-source.
  - **Phosphor:** 6 grosores distintos, flexible.
  - **Heroicons:** Diseñado por el equipo de Tailwind.
  - **Radix:** Iconos minimalistas en rejilla de 15×15.

## 5. Movimiento y Animación (Motion & Animation)
El movimiento da vida a la UI. Debe ser intencional: cada animación debe informar y guiar, nunca distraer.
- **Transiciones:** Utilizar curvas de tipo `ease-out`, `ease-in-out` y `ease-in` según corresponda.
- **Escala de Duraciones:**
  - **100ms (Micro):** Para hovers, toggles, desvanecidos rápidos (fades).
  - **200ms (Small):** Pulsación de botones, tooltips.
  - **300ms (Medium):** Modales, menús desplegables (dropdowns).
  - **400ms (Large):** Transición de páginas, slides/desplazamientos de secciones.
  - **500ms (XL):** Animaciones o transiciones complejas encadenadas.
- **Principios de Animación:**
  - **Propósito:** Guiar la atención del usuario y mostrar la relación entre componentes.
  - **Velocidad:** Nunca bloquear la interacción del usuario. Máximo 300ms para interacciones directas.
  - **Física Natural:** Seguir leyes físicas naturales. Usar `ease-out` para elementos que entran a pantalla y `ease-in` para elementos que salen.

## 6. Buenas y Malas Prácticas (Do's and Don'ts)
- **SÍ:** Usar espaciados consistentes y múltiplos de 4.
- **SÍ:** Limitar la paleta de colores del proyecto.
- **SÍ:** Seguir la escala tipográfica modular establecida.
- **SÍ:** Diseñar y codificar todos los estados de un componente interactivo.
- **SÍ:** Utilizar tokens de colores semánticos.
- **NO:** Calcular o inventar espaciados de forma visual aleatoria (a ojo).
- **NO:** Mezclar paletas y usar colores excesivos sin coherencia.
- **NO:** Usar tamaños de fuentes arbitrarios que salgan de la escala.
- **NO:** Diseñar únicamente el camino feliz (diseña también estados de carga y error).
- **NO:** Declarar colores con valores hexadecimales de forma directa (hardcode). Utilizar siempre variables/tokens de CSS.
