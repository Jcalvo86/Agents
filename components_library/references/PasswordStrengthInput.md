# Componente: PasswordStrengthInput

Campo de entrada de contraseña interactivo con visualizador de robustez de 4 niveles y validación interactiva de requisitos en tiempo real. Diseñado para formularios de creación y cambio de contraseñas de cuentas.

### Ubicación del Código
- **React Component**: [PasswordStrengthInput.jsx](file:///c:/andreia/Web-sueno-travel/controlpanel/components/PasswordStrengthInput.jsx)

---

## Modo de Uso (React)

```jsx
import PasswordStrengthInput from '../components/PasswordStrengthInput.jsx';

const [password, setPassword] = useState('');

<PasswordStrengthInput
  value={password}
  onChange={(e) => setPassword(e.target.value)}
  id="password"
  placeholder="Crea una contraseña segura"
  required={true}
/>
```

---

## Comportamiento y Feedback Visual

1. **Visibilidad de Contraseña**:
   - Cuenta con un botón (icono de ojo) que permite alternar la propiedad `type` del campo entre `password` (oculto) y `text` (visible).

2. **Indicador de Robustez (4 niveles)**:
   - Evalúa la contraseña en tiempo real asignando una puntuación de 1 a 4 según las reglas cumplidas.
   - Dibuja 4 segmentos horizontales coloreados dinámicamente según el nivel:
     - **1 segmento (Rojo)**: Muy Débil.
     - **2 segmentos (Naranja)**: Débil / Aceptable.
     - **3 segmentos (Amarillo)**: Buena.
     - **4 segmentos (Verde)**: Fuerte.

3. **Checklist de Requerimientos**:
   - Muestra de forma interactiva el estado de cumplimiento de las siguientes directrices obligatorias:
     - Mínimo 8 caracteres.
     - Al menos una letra mayúscula.
     - Al menos un número.
     - Al menos un símbolo/carácter especial.
   - Cada regla se actualiza dinámicamente: las cumplidas cambian a verde (`#10b981`) con un icono de check (`check_circle`), mientras que las pendientes permanecen en gris claro (`#94a3b8`) con un icono circular vacío (`circle`).
