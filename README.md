# GYM-DSW 

Sistema full stack para la gestión integral de gimnasios, desarrollado como proyecto para la materia **Desarrollo de Software** (UTN FRRO).

### Demo en Vivo
- [Frontend](https://frontend-app-gym.vercel.app/)
- [Backend API](https://backendappgym.onrender.com) 

### Repositorios
- [frontend app](https://github.com/luciabolcatto/frontendAppGym.git)
- [backend app](https://github.com/luciabolcatto/backendAppGym.git)

### Tecnologías Principales
- **Backend:** Node.js, TypeScript, MikroORM (MongoDB).
- **Pagos:** Stripe API (Checkout & Webhooks).
- **Mailing:** Nodemailer (Gmail).
- **Frontend:** React + Vite + Tailwind CSS.

### Documentación del Proyecto
Para conocer más detalles sobre los objetivos y el alcance funcional del sistema, consultá:
- [📄 Propuesta del Proyecto (Proposal)](./proposal.md)
- [📊 Modelo de Dominio (DER)](./images/gym.jpg)
- [📝 Casos de Uso Detallados](./docs/casos-uso.md)

---

### Configuración del Entorno (Backend)

1. **Instalar dependencias:** <br>
bash <br>
   pnpm install <br> 

2. **Cargar datos de pueba (seed):** <br>
bash <br>
   pnpm run seed <br> 

4. **Ejecutar en modo desarrollo:** <br>
bash <br>
   pnpm run dev  <br>

### Credenciales de Prueba
Para testear el flujo de pagos (Stripe Test Mode):
- **Tarjeta:** 4000 0003 2000 0021
- **CVC:** 123
- **EXP:** Cualquier fecha futura.

### Cuentas de Prueba
* **Contraseña de todos los usuarios :**  `123456` <br>
* **Socio con Contrato Activo:** `pedro@mail.com` (Tiene historial de pagos y reservas).
* **Socio con Contrato Vencido:** `diego@mail.com` (Ideal para probar restricciones).
* **Socio Nuevo (Sin Contrato):** `esperanza@mail.com` (Ideal para probar el flujo de compra con Stripe).
* **Administrador:** `admin@gym.com` / `Miclavesegura`

### Funcionalidades Destacadas
- **Pagos Seguros:** Sincronización automática de membresías vía Webhooks.
- **Schedulers:**  Procesos automáticos de vencimiento de contratos y cierre de cupos.
- **Feedback:** Sistema de valoración de entrenadores.
