# Instrucción de Configuración del Ambiente y Herramientas (Onboarding)

Este documento describe el protocolo que debe seguir el agente de desarrollo para inicializar y validar las herramientas visuales y dependencias del ambiente de trabajo en CoralFlow / PM Helper.

---

## 🛠️ 1. Verificación de Herramientas del Entorno

Al iniciar un nuevo chat o sesión, el agente debe verificar que las siguientes herramientas de asistencia al desarrollo estén listas:

### A. Agentation (Visual Feedback)
- **Propósito**: Canal de comunicación bidireccional y anotaciones visuales en tiempo real sobre la interfaz web del proyecto.
- **Estado**: Debe estar registrado en `package.json` (`devDependencies`), importado dinámicamente en el root layout (`src/app/layout.tsx`), y activo solo en el entorno de desarrollo (`process.env.NODE_ENV === "development"`).
- **Verificación**: 
  - Validar que el servidor de desarrollo local esté ejecutándose (generalmente en `http://localhost:3000` o `http://localhost:5173`).
  - Si el usuario cuenta con el servidor MCP de Agentation activo en el puerto `4747`, conectarse a él utilizando el protocolo MCP para recibir feedback de interfaz en tiempo real.

### B. Submódulo de Git (`agents/`)
- **Propósito**: Repositorio centralizado de submódulos y configuraciones para el comportamiento de agentes del proyecto.
- **Ubicación**: Carpeta raíz `/agents` (`https://github.com/Jcalvo86/Agents.git`).
- **Verificación**: Confirmar que la carpeta no esté vacía. Si no está inicializado, solicitar al usuario que ejecute `git submodule update --init --recursive`.

---

## 🚀 2. Protocolo de Inicio para Agentes de "Web/View Coding" (Visual Context)

Cualquier agente que realice modificaciones estéticas, maquetación o flujos de usuario complejos (Web Coding) debe apalancarse en estas herramientas:

1. **Uso de Agentation en el Navegador**:
   - Inspeccionar las anotaciones visuales pendientes dejadas por el usuario.
   - Las anotaciones son persistidas localmente y sincronizadas automáticamente.
2. **Sincronización del Submódulo**:
   - Asegurarse de leer y respetar las guías de diseño y prompts almacenados dentro de `/agents` antes de proceder con refactorizaciones visuales complejos.
