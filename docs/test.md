# 🧪 Evidencia de Testing Automatizado - GYM DSW


A continuación, se detalla la cobertura de los tests ejecutados junto con la evidencia (capturas y videos) de su exitosa ejecución.

---

## 💻 1. Pruebas de Frontend (React + Jest)
 Las pruebas  se enfocaron en la renderización de componentes complejos, simulación de interacciones del usuario y pruebas End-to-End (E2E) simulando el flujo de negocio completo.
### Flujo Completo del Usuario (E2E)
- **Comando:** `pnpm test:e2e`
- **Descripción:** Simula paso a paso el recorrido de un usuario real: registro, contratación de membresía, pago y reserva de una clase.
- **Evidencia:** [🎥 Ver video de la ejecución (e2e.mp4)](./images/e2e.mp4)

### Modal de Pagos
- **Comando:** `pnpm test:pago`
- **Descripción:** Pruebas intensivas sobre el componente `PagoModal`, validando el manejo de errores, métodos no soportados y el redireccionamiento a Stripe.
- **Evidencia:** [🎥 Ver video de la ejecución (pago.mp4)](./images/pago.mp4)

### Sistema de Valoraciones
- **Comando:** `pnpm test:star`
- **Descripción:** Ejecuta tests unitarios sobre el componente de valoraciones y el renderizado de estrellas según el puntaje.
- **Evidencia:** 
  ![Test Valoraciones](./images/star.png)

---

## ⚙️ 2. Pruebas de Backend (Node.js + Jest)

La API cuenta con pruebas unitarias y pruebas de integración usando una base de datos en memoria (`gym_test`) para evitar ensuciar los datos de producción.

### Pruebas de Integración (Flujos Completos)
Estos tests levantan la base de datos de prueba y ejecutan el ciclo de vida completo de un contrato.

- **Comando:** `pnpm test:integration`
- **Descripción:** Pruebas de flujo completo de pago manual (Stripe) y su impacto en la base de datos.
- **Evidencia:** [🎥 Ver video de la ejecución (i.mp4)](./images/i.mp4)

- **Comando:** `pnpm test:integration:efectivo`
- **Descripción:** Simulación del flujo de pago automático en efectivo .
- **Evidencia:** [🎥 Ver video de la ejecución (ie.mp4)](./images/ie.mp4)

### Pruebas Unitarias por Dominio
- **Autenticación y Seguridad**
  - **Comando:** `pnpm test:auth`
  - **Descripción:** Pruebas de generación de tokens JWT, encriptación de contraseñas y middleware de seguridad.
  - **Evidencia:** 
    ![Test Auth](./images/auth.png)

- ** Reservas **
  - **Comando:** `pnpm test:reserva`
  - **Descripción:** Verifica las reglas de negocio para crear y modificar reservas.
  - **Evidencia:** 
    ![Test Reservas](./images/reserva.png)

- **Contratos**
  - **Comando:** `pnpm test:contrato`
  - **Descripción:** Comprueba la lógica de contratacion.
  - **Evidencia:** 
    ![Test Contratos](./images/contrato.png)

- **Controlador de Usuarios**
  - **Comando:** `pnpm test:usuario`
  - **Descripción:** Evalúa la sanitización de datos ingresados, el flujo de inicio de sesión (login) y la correcta gestión de las cuentas.
  - **Evidencia:** 
    ![Test Usuarios](./images/usuario.png)

