---
name: ui-patterns
description: Estándares de Diseño y Usabilidad para formularios dinámicos, acordeones, autocompletados y visualización de datos en el Control Panel.
---

# Estándares de Diseño y Usabilidad — CRM Control Panel

Estas reglas describen las decisiones de UI/UX estándar que deben aplicarse a todos los módulos, formularios y componentes interactivos creados para el Control Panel.

## 1. Diseño de Formularios Dinámicos y Complejos

- **Patrón de 3 Columnas para Estructuras Anidadas:**
  Para campos donde se definen entidades principales y sub-entidades (por ejemplo: *Países y sus Ciudades*, *Ciudades y sus Hoteles*, *Secciones y Contenidos*), utiliza una distribución en rejilla:
  - Columna Izquierda (span 4): Contenedor o contexto principal.
  - Columnas Derechas (span 8): Lista de elementos hijos vinculados con una línea divisoria vertical sutil (`border-l border-[var(--outline-variant)]`).
  
- **Botón de Cierre/Eliminación Integrado en el Input:**
  Para listados dinámicos simples donde cada línea cuenta con un botón para removerla (como variables, ciudades, u hoteles alternativos), el botón de remover (`close`) se posiciona **dentro del input**, en el extremo derecho (utilizando contenedores `relative` y un padding-right de `32px` en el input).

- **Ubicación de Botones "Añadir":**
  Los botones para agregar elementos a una lista dinámica (e.g. *Añadir Ciudad*, *Añadir Actividad*, *Añadir Hotel*, *Añadir Variable*) deben ir **siempre en la parte inferior** de su respectivo listado. No se deben colocar en las cabeceras o etiquetas superiores para mantener el orden de lectura vertical.

- **Botones de Acción Destructiva Flotantes:**
  Los botones de eliminación global para tarjetas o secciones grandes deben ubicarse en la **esquina superior derecha** de la tarjeta (`absolute top-4 right-4`).
  Se utiliza el botón circular tipo icono con el símbolo de basurero (`delete`) y la acción destructiva controlada `HoldToConfirmButton` (ver especificación y comportamiento en la biblioteca de componentes).

## 2. Autocompletados Híbridos (Inputs Autocompletables)

- **Tecnología Datalist:**
  Utiliza elementos estándar **HTML5 <datalist>** enlazados mediante el atributo `list` en los inputs de texto para permitir sugerencias rápidas (como el régimen hotelero, momentos de actividades o categorías) sin bloquear la escritura libre de valores personalizados.

## 3. Acordeones Colapsables Inteligentes

- **Transición de Altura:**
  Los bloques extensos se presentan en acordeones animados con transiciones de CSS Grid (`grid-template-rows: 0fr -> 1fr`).
- **Comportamiento Dinámico:**
  Al abrir un registro existente inician colapsados para limpieza visual. Al crear un nuevo elemento en la lista, colapsa de inmediato los anteriores y expande de forma automática únicamente el nuevo elemento creado para agilizar el ingreso de datos.

## 4. Visualización en Tablas (ItemsTable)

- **Píldoras de Ubicación/Metadatos:**
  La visualización de registros dinámicos y agrupados en la tabla principal debe utilizar píldoras compactas (`chip`) con formato jerárquico que destaquen primero el contenedor principal en negrita y a continuación los sub-elementos entre paréntesis (ej: **Egipto** (El Cairo, Luxor)).

## 5. Creación de Entidades Complejas (Wizards)

- **Patrón de Wizard (Paso a Paso):**
  Para la primera vez que se crea una entidad compleja o densa en información (como un ICP, Producto o Campaña), se recomienda dividir el formulario en un flujo "Wizard" por pasos lógicos (ej: 1. Datos Generales -> 2. Psicografía / Dolores -> 3. Relaciones / Productos).
  - Reduce la carga cognitiva inicial del usuario.
  - Permite guardar borradores o auto-guardar entre pasos.
  - Para la *edición posterior*, se puede optar por una vista unificada (todas las secciones visibles) o mantener la navegación por pestañas (tabs).

- **Tono Conversacional e Íntimo:**
  Los formularios dentro del Wizard deben sentirse personales y guiados. En lugar de usar etiquetas secas de bases de datos (ej. "Nombre del Arquetipo", "Rol"), utiliza preguntas directas y conversacionales (ej. "¿Cuál es su nombre?", "¿Qué rol o puesto ocupa?"). Esto facilita la inmersión del usuario al definir entidades abstractas.

- **Navegación y Stepper Superior:**
  El indicador visual de progreso (Stepper) en la parte superior debe contar con tooltips interactivos. Al pasar el cursor sobre los números o indicadores de pasos, el usuario debe poder leer el nombre de la sección actual y previsualizar qué secciones (pasos) vienen a continuación, mejorando la orientación espacial dentro del formulario.
