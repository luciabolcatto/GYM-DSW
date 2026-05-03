## Documentación de la API

Esta API expone sus recursos principales bajo la ruta base `/api`. En producción se puede consumir desde:

- `https://backendappgym.onrender.com/api`

En desarrollo local, normalmente queda disponible en:

- `http://localhost:5500/api`

### Convenciones generales

- Los recursos siguen un estilo REST con métodos `GET`, `POST`, `PUT`, `PATCH` y `DELETE`.
- La mayoría de rutas de consulta son públicas, pero las operaciones de usuario autenticado y administración usan JWT.
- Las imágenes subidas se guardan en `public/uploads` y luego se sirven de forma estática desde `/public`.
- El backend usa validaciones y saneamiento de datos antes de persistir la información.

### Autenticación

Las rutas protegidas usan JWT. El token se envía en el header:

- `Authorization: Bearer <token>`

### Códigos de respuesta habituales

- `200 OK`: consulta o actualización exitosa.
- `201 Created`: recurso creado correctamente.
- `400 Bad Request`: datos inválidos o incompletos.
- `401 Unauthorized`: falta token o el token es inválido.
- `403 Forbidden`: el usuario no tiene permisos suficientes.
- `404 Not Found`: el recurso solicitado no existe.
- `500 Internal Server Error`: error inesperado del servidor.

### Recursos principales

- `Usuarios`: registro, login, recuperación de contraseña y administración de usuarios.
- `Contratos`: contratación de membresías, cancelación, consulta, filtrado y verificación de vencimientos.
- `Reservas`: creación y mantenimiento de reservas asociadas a usuarios y clases.
- `Actividades`: CRUD de actividades e imagen asociada.
- `Clases`: CRUD, ordenamiento, actualización de cupos y consultas especiales.
- `Entrenadores`: CRUD de entrenadores.
- `Membresías`: CRUD de planes o membresías.
- `Valoraciones`: creación de valoraciones y resumen de feedback.
- `Admin`: autenticación de administrador.
- `Stripe`: métodos de pago, checkout y estado de sesiones.

### Catálogo de endpoints

#### Usuarios

- `POST /api/Usuarios`: registra un usuario nuevo y permite enviar foto de perfil.
- `POST /api/Usuarios/login`: inicia sesión y devuelve el token JWT.
- `POST /api/Usuarios/solicitar-codigo`: solicita un código de recuperación de contraseña.
- `POST /api/Usuarios/validar-codigo`: valida el código de recuperación.
- `POST /api/Usuarios/restablecer-contrasena`: restablece la contraseña.
- `GET /api/Usuarios`: lista usuarios registrados, requiere autenticación.
- `GET /api/Usuarios/:id`: obtiene un usuario por id, requiere autenticación.
- `PUT /api/Usuarios/:id`: actualiza un usuario y su foto de perfil, requiere autenticación.
- `PATCH /api/Usuarios/:id`: actualiza parcialmente un usuario y su foto de perfil, requiere autenticación.
- `DELETE /api/Usuarios/:id`: elimina un usuario, requiere autenticación.

#### Contratos

- `GET /api/Contratos`: lista contratos.
- `GET /api/Contratos/:id`: obtiene un contrato por id, requiere autenticación.
- `POST /api/Contratos`: crea un contrato.
- `PUT /api/Contratos/:id`: actualiza un contrato.
- `PATCH /api/Contratos/:id`: actualiza parcialmente un contrato.
- `DELETE /api/Contratos/:id`: elimina un contrato.
- `POST /api/Contratos/contratar`: ejecuta el flujo de contratación de membresía.
- `PATCH /api/Contratos/cancelar/:contratoId`: cancela un contrato.
- `POST /api/Contratos/verificar-vencimientos`: verifica y actualiza contratos vencidos.
- `GET /api/Contratos/usuario/:usuarioId`: obtiene los contratos de un usuario, requiere autenticación.
- `GET /api/Contratos/admin/estadisticas`: devuelve estadísticas de contratos.
- `GET /api/Contratos/filtrado`: lista filtrada de contratos, requiere permisos de administrador.

#### Reservas

- `GET /api/Reservas`: lista reservas, requiere autenticación.
- `GET /api/Reservas/:id`: obtiene una reserva por id, requiere autenticación.
- `POST /api/Reservas`: crea una reserva, requiere autenticación.
- `PUT /api/Reservas/:id`: actualiza una reserva, requiere autenticación.
- `PATCH /api/Reservas/:id`: actualiza parcialmente una reserva, requiere autenticación.
- `DELETE /api/Reservas/:id`: elimina una reserva, requiere autenticación.
- `GET /api/Reservas/filtrado`: lista filtrada de reservas, requiere permisos de administrador.
- `POST /api/Reservas/actualizar`: actualiza reservas según la lógica de negocio.

#### Actividades

- `GET /api/actividad`: lista actividades.
- `GET /api/actividad/:id`: obtiene una actividad por id.
- `POST /api/actividad`: crea una actividad.
- `PUT /api/actividad/:id`: actualiza una actividad.
- `DELETE /api/actividad/:id`: elimina una actividad.
- `POST /api/actividad/:id/imagen`: sube la imagen asociada a una actividad.

#### Clases

- `GET /api/clases`: lista clases.
- `GET /api/clases/:id`: obtiene una clase por id.
- `GET /api/clases/todas-ordenadas`: lista clases ordenadas.
- `GET /api/clases/con-reservas-usuario`: lista clases con reservas del usuario.
- `GET /api/clases/admin/todas-ordenadas`: lista clases ordenadas, requiere permisos de administrador.
- `POST /api/clases`: crea una clase.
- `PUT /api/clases/:id`: actualiza una clase.
- `PATCH /api/clases/:id`: actualiza parcialmente una clase.
- `DELETE /api/clases/:id`: elimina una clase.
- `PATCH /api/clases/:id/actualizar-cupo`: actualiza el cupo de una clase.

#### Entrenadores

- `GET /api/entrenadores`: lista entrenadores.
- `GET /api/entrenadores/:id`: obtiene un entrenador por id.
- `POST /api/entrenadores`: crea un entrenador.
- `PUT /api/entrenadores/:id`: actualiza un entrenador.
- `PATCH /api/entrenadores/:id`: actualiza parcialmente un entrenador.
- `DELETE /api/entrenadores/:id`: elimina un entrenador.

#### Membresías

- `GET /api/membresias`: lista membresías.
- `GET /api/membresias/:id`: obtiene una membresía por id.
- `POST /api/membresias`: crea una membresía.
- `PUT /api/membresias/:id`: actualiza una membresía.
- `PATCH /api/membresias/:id`: actualiza parcialmente una membresía.
- `DELETE /api/membresias/:id`: elimina una membresía.

#### Valoraciones

- `GET /api/valoraciones`: lista valoraciones.
- `GET /api/valoraciones/resumen`: devuelve un resumen agregado de valoraciones.
- `POST /api/valoraciones`: crea o actualiza una valoración.

#### Administración

- `POST /api/admin/login`: autentica al administrador.

#### Stripe

- `GET /api/stripe/metodos-pago`: devuelve los métodos de pago disponibles.
- `POST /api/stripe/create-checkout-session`: crea una sesión de pago de Stripe.
- `GET /api/stripe/session/:sessionId`: consulta el estado de una sesión de pago.
- `POST /api/stripe/pagar-transferencia`: registra un pago simulado por transferencia.
- `POST /api/stripe/pagar-efectivo`: registra un pago simulado en efectivo.
- `POST /api/stripe/webhook`: webhook de Stripe configurado directamente en `src/app.ts` con body raw.

### Archivos públicos

Las imágenes subidas se sirven desde `/public`, por ejemplo:

- `http://localhost:5500/public/uploads/...`

