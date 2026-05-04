# Documentación de Casos de Uso

Este documento detalla la lógica de negocio y el flujo técnico de los principales Casos de Uso (CU) implementados en el sistema **GYM-DSW**.

### 1. Gestión de Membresías y Pagos (Stripe)

- **Descripción**: Permite al socio adquirir planes de entrenamiento mediante un flujo de pago digital seguro.
- **Flujo**: El socio elige un plan, el backend genera una sesión de **Stripe Checkout** y, tras el pago exitoso, un **Webhook** activa el contrato automáticamente.
- **Lógica Técnica**:
  - Uso de firmas digitales (`STRIPE_WEBHOOK_SECRET`) para validar la autenticidad de las notificaciones de pago.
  - Sincronización asincrónica para garantizar que el contrato se active independientemente de la navegación del usuario.

### 2. Reserva de Clases

- **Descripción**: El socio asegura su lugar en una actividad específica según la disponibilidad horaria.
- **Lógica Técnica**:
  - El sistema valida que el socio posea un contrato en estado `PAGADO` antes de procesar la solicitud.
  - Al confirmar la reserva, se decrementa automáticamente el `cupo_disp` en la entidad `Clase` para reflejar la disponibilidad real.

### 3. Cancelación de Reserva y Liberación de Cupos

- **Descripción**: Permite al usuario desistir de una clase, recuperando la disponibilidad para otros socios del gimnasio.
- **Lógica Técnica**:
  - **Regla de Negocio**: Validación temporal que impide cancelaciones si faltan menos de 30 minutos para el inicio de la clase.
  - **Impacto**: Al cancelar, el estado de la reserva pasa a `CANCELADA` y se incrementa automáticamente el `cupo_disp` de la clase vinculada.

### 4. Valoración de Entrenadores

- **Descripción**: Los socios califican (1 a 5 estrellas) y comentan el desempeño de los profesores.
- **Lógica Técnica (Upsert)**:
  - **Unicidad**: El sistema busca si ya existe una relación previa entre el `usuario` y el `entrenador`.
  - **Comportamiento**: Si ya existe una valoración, el controlador ejecuta un _update_ del `rating` y `comentario`. Si no existe, realiza un _insert_. Esto garantiza una sola reseña por socio por entrenador.
  - **Resumen de Datos**: El backend expone un endpoint de `resumen` que calcula el promedio decimal y la cantidad total de valoraciones para mostrar en el perfil del staff.

### Automatización

- **Cierre de Reservas**: Tareas programadas que transicionan reservas `PENDIENTES` a `CERRADAS` cuando el tiempo de cancelación ha expirado o la clase ha iniciado.
- **Control de Vencimientos**: Verificación periódica de la `fecha_hora_fin` de los contratos para actualizar su estado a `VENCIDO` de forma autónoma.

### Estados de Dominio

#### Contrato

- **PENDIENTE**: contrato creado pero sin pago confirmado.
- **PAGADO**: contrato activo con pago validado.
- **CANCELADO**: contrato anulado antes de activarse o por decisión del usuario/sistema.
- **VENCIDO**: contrato expirado por fecha de fin.

**Transiciones principales**:

- `PENDIENTE -> PAGADO`: al confirmarse pago (Stripe webhook o pago manual).
- `PENDIENTE -> CANCELADO`: cancelación permitida del contrato pendiente.
- `PAGADO -> VENCIDO`: tarea automática de vencimientos por fecha.

#### Reserva

- **PENDIENTE**: reserva vigente y gestionable.
- **CANCELADA**: reserva anulada por el usuario (respetando ventana de tiempo).
- **CERRADA**: reserva bloqueada por inicio/cercanía de la clase o cierre automático.

**Transiciones principales**:

- `PENDIENTE -> CANCELADA`: cancelación válida según reglas de tiempo.
- `PENDIENTE -> CERRADA`: cierre automático por scheduler o por reglas de negocio.

#### Relación con reglas de negocio

- Para reservar, el usuario debe tener contrato `PAGADO` vigente.
- No se permite cancelar reservas dentro de los 30 minutos previos al inicio.
- El cierre automático de reservas y vencimiento de contratos mantiene consistencia operativa.
