# Patrón de Diseño: InlineAutoSave (Autoguardado con Debounce)

Patrón de interacción de interfaz diseñado para formularios rápidos, modales de configuración y paneles laterales de edición en línea. Permite guardar la información de manera silenciosa en segundo plano (asíncrona) reduciendo la fatiga del usuario y optimizando la carga en el servidor/base de datos (Firebase).

### Archivos de Referencia en el Código
* **Lógica del temporizador**: [`src/components/EDTView.tsx`](file:///c:/andreia/PM%20Helper/src/components/EDTView.tsx#L693-L710)
* **Control de estado en formulario**: [`src/components/EDTEditPanel.tsx`](file:///c:/andreia/PM%20Helper/src/components/EDTEditPanel.tsx)

---

## Estructura del Patrón (React)

Para implementar este comportamiento, se debe declarar el estado del formulario de forma local y utilizar un efecto de efecto (`useEffect`) con un temporizador que dispare la sincronización con el servidor tras **800ms** de inactividad de teclado.

```tsx
import React, { useState, useEffect } from "react";
import { saveNodeData } from "@/lib/actions";

export const EditPanel: React.FC<{ initialData: any, projectId: string }> = ({ initialData, projectId }) => {
  const [editingNode, setEditingNode] = useState(initialData);
  const [isSaving, setIsSaving] = useState(false);

  // 1. Efecto de Autoguardado con Debounce
  useEffect(() => {
    if (!editingNode) return;

    // Dispara el guardado tras 800ms de inactividad
    const timer = setTimeout(async () => {
      try {
        setIsSaving(true);
        await saveNodeData(projectId, editingNode);
      } catch (err) {
        console.error("Auto-save error:", err);
      } finally {
        setIsSaving(false);
      }
    }, 800);

    // 2. Cleanup: Cancela el temporizador anterior si el usuario presiona otra tecla antes de los 800ms
    return () => clearTimeout(timer);
  }, [editingNode, projectId]);

  return (
    <div className="space-y-4">
      <input
        type="text"
        value={editingNode.name}
        onChange={(e) => setEditingNode(prev => ({ ...prev, name: e.target.value }))}
        className="w-full bg-slate-900 border border-white/10 rounded-xl px-4 py-2 text-white"
      />
      {isSaving && (
        <span className="text-xs text-blue-400 font-semibold animate-pulse">
          Guardando cambios automáticamente...
        </span>
      )}
    </div>
  );
};
```

---

## Directrices de Diseño y UX

1. **Evitar Congelar la UI**:
   - Nunca bloquees ni muestres loaders a pantalla completa durante el guardado de inputs individuales. El autoguardado debe ocurrir en segundo plano de manera fluida.
2. **Visualización de Carga Silenciosa**:
   - Muestra un spinner o texto discreto (`isSaving`) en el pie del panel o en la cabecera general del dashboard.
3. **Control de Ciclos / Frecuencia**:
   - El tiempo de debounce estándar es de **800ms**. Tiempos menores (como 300ms) pueden saturar la API de Firebase al escribir textos largos, mientras que tiempos mayores (como 1500ms) pueden hacer que el usuario cierre el panel antes de que se guarde el dato.
4. **Persistencia Preventiva**:
   - Si el panel lateral de edición cuenta con un botón para cerrarse o el usuario hace clic fuera de él (desenfoca el panel), asegúrate de persistir el último estado local inmediatamente sin esperar a que se cumpla el temporizador (evento `onBlur` o handler de cierre).
