# Componente: HoldToConfirmButton

Botón interactivo premium diseñado para evitar clics accidentales en acciones críticas (como eliminaciones) requiriendo que el usuario mantenga presionado el botón por un lapso configurable (por defecto 1.5s).

### Ubicación del Código
- **React Component**: [HoldToConfirmButton.tsx](file:///c:/andreia/PM%20Helper/src/components/HoldToConfirmButton.tsx)

---

## Modo de Uso (React)

```jsx
import HoldToConfirmButton from '../components/HoldToConfirmButton.jsx';

<HoldToConfirmButton
  onConfirm={handleDeleteRecord}
  className="btn-danger"
  holdTime={1500} // Opcional (tiempo de retención en ms, por defecto 1500)
>
  <span className="material-symbols-outlined text-sm">delete</span>
  Mantener para Eliminar
</HoldToConfirmButton>
```

---

## Comportamiento y Feedback Visual

1. **Mantener Presionado**:
   - Al iniciar la presión (`onMouseDown` / `onTouchStart`), se inicia un intervalo que va acumulando el porcentaje de progreso.
2. **Animación de Círculo / Barra de Progreso**:
   - Muestra un círculo/borde de progreso visual continuo que se rellena radialmente.
3. **Confirmación**:
   - Al alcanzar el 100% del `holdTime`, ejecuta la función del callback `onConfirm` y emite un feedback visual de éxito.
4. **Cancelación**:
   - Si el usuario suelta el click (`onMouseUp` / `onMouseLeave` / `onTouchEnd`) antes del tiempo estipulado, el progreso se resetea inmediatamente a 0, evitando la confirmación.
5. **Evitar Alertas / Diálogos Duplicados**:
   - Dado que la acción de mantener presionado el botón por 1.5s ya valida plenamente la intención del usuario, **NUNCA** se debe activar un cuadro de diálogo de confirmación secundario (como `confirm()` nativo del navegador) al finalizar la carga. El callback `onConfirm` debe proceder de forma directa e inmediata.
