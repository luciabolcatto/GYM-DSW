# GYM-DSW 

Sistema full stack para la gestión integral de gimnasios, desarrollado como proyecto para la materia **Desarrollo de Software** (UTN FRRO).

### Demo en Vivo
- [Frontend]: https://frontend-app-gym.vercel.app/
- [Backend API]: https://backendappgym.onrender.com 

### Repositorios
- [frontend app](https://github.com/luciabolcatto/frontendAppGym.git)
- [backend app](https://github.com/luciabolcatto/backendAppGym.git)

### Tecnologías Principales
- [Backend] Node.js, TypeScript, MikroORM (MongoDB).
- [Pagos] Stripe API (Checkout & Webhooks).
- [Mailing] Nodemailer (Gmail).
- [Frontend] React + Vite + Tailwind CSS.

### Documentación del Proyecto
Para conocer más detalles sobre los objetivos y el alcance funcional del sistema, consultá:
- [📄 Propuesta del Proyecto (Proposal)](./proposal.md)
- [📊 Modelo de Dominio (DER)](./images/gym.jpg)

---

### Configuración del Entorno (Backend)

1. **Instalar dependencias:**
   ```bash
   pnpm install

2. Variables de Entorno (.env):
Configurar las claves de MongoDB, JWT, Stripe y Gmail.

3. Ejecutar en modo desarrollo:
Bash
pnpm run dev

### Credenciales de Prueba
Para testear el flujo de pagos (Stripe Test Mode):
- [Tarjeta] 4000 0003 2000 0021
- [CVC] 123
- [EXP] Cualquier fecha futura.

### Funcionalidades Destacadas
- [Pagos Seguros]: Sincronización automática de membresías vía Webhooks.
- [Schedulers]: Procesos automáticos de vencimiento de contratos y cierre de cupos.
- [Feedback]: Sistema de valoración de entrenadores.
