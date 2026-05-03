# Documentación Completa de la API - Gym DSW

## 📋 Tabla de Contenidos

1. [Información General](#información-general)
2. [Autenticación y Autorización](#autenticación-y-autorización)
3. [Endpoints por Entidad](#endpoints-por-entidad)
4. [Ver Información Adicional](#información-adicional)

---

## Información General

### URLs Base

- **Producción**: `https://backendappgym.onrender.com/api`
- **Desarrollo**: `http://localhost:5500/api`
- **Archivos estáticos**: `http://localhost:5500/public`

### Características Técnicas

- **Framework**: Express.js + TypeScript
- **Base de Datos**: MongoDB con MikroORM
- **Autenticación**: JWT (Bearer token)
- **Almacenamiento**: Imágenes en `/public/uploads/[entidad]/[id]/[archivo]`
- **CORS**: Configurado para permitir localhost:5173 y dominios Vercel

### Convenciones Generales

- Todas las entidades usan IDs en formato `ObjectId` (MongoDB) serializado a `string`
- Las respuestas incluyen `message` (descripción) y `data` (payload)
- No hay paginación implementada (retorna todos los registros)
- Saneamiento de entrada en todos los endpoints

---

## Autenticación y Autorización

### Tipos de Autenticación

#### 1. **Autenticación de Usuarios (JWT Standard)**

- **Header requerido**: `Authorization: Bearer <token>`
- **Rutas protegidas**: GET/PUT/PATCH/DELETE de Usuario, Reservas, Contratos del usuario
- **Generado en**: POST `/api/Usuarios/login`

#### 2. **Autenticación de Administrador (Admin Token)**

- **Header requerido**: `Authorization: Bearer <admin_token>`
- **Rutas protegidas**:
  - GET `/api/Contratos/filtrado` (filtro de usuarios sin contrato)
  - GET `/api/Reservas/filtrado` (filtro de reservas por clase)
  - GET `/api/clases/admin/todas-ordenadas` (acceso admin a clases)
- **Generado en**: POST `/api/admin/login`

#### 3. **Rutas Públicas**

- Todas las consultas GET de recursos
- POST `/api/Usuarios` (registro)
- POST `/api/Usuarios/login`
- POST `/api/Usuarios/solicitar-codigo`
- POST `/api/Usuarios/validar-codigo`
- POST `/api/Usuarios/restablecer-contrasena`

### Códigos de Respuesta HTTP

| Código | Significado                              | Ejemplo                                 |
| ------ | ---------------------------------------- | --------------------------------------- |
| `200`  | OK - Consulta o actualización exitosa    | GET, PUT, PATCH success                 |
| `201`  | Created - Recurso creado                 | POST success                            |
| `400`  | Bad Request - Datos inválidos            | Validación fallida                      |
| `401`  | Unauthorized - Token faltante o inválido | Sin Bearer token                        |
| `403`  | Forbidden - Permiso insuficiente         | Usuario intenta modificar reserva ajena |
| `404`  | Not Found - Recurso no existe            | GET con ID inexistente                  |
| `500`  | Server Error - Error inesperado          | Excepción en servidor                   |

---

## Endpoints por Entidad

### USUARIOS

#### 1. **POST /api/Usuarios** (Registro)

**Autenticación**: No requerida

**Body**:

```json
{
  "nombre": "Juan",
  "apellido": "Pérez",
  "tel": 1234567890,
  "mail": "juan@example.com",
  "contrasena": "password123",
  "fotoPerfil": "<archivo opcional>"
}
```

**Validaciones**:

- `nombre`: string no vacío
- `apellido`: string no vacío
- `mail`: regex `/^[^\s@]+@[^\s@]+\.[^\s@]+$/`, único
- `contrasena`: min 6 caracteres
- `tel`: solo dígitos

**Response** (201):

```json
{
  "message": "Usuario creado",
  "data": {
    "id": "507f1f77bcf86cd799439011",
    "nombre": "Juan",
    "apellido": "Pérez",
    "tel": 1234567890,
    "mail": "juan@example.com",
    "fotoPerfil": "/uploads/usuario/507f1f77bcf86cd799439011/image.jpg"
  }
}
```

**Errores**:

- 400: Email ya registrado
- 400: Nombre requerido
- 400: Mail inválido
- 400: Teléfono solo debe contener números
- 400: Contraseña mínimo 6 caracteres

---

#### 2. **POST /api/Usuarios/login** (Autenticación)

**Autenticación**: No requerida

**Body**:

```json
{
  "mail": "juan@example.com",
  "contrasena": "password123"
}
```

**Response** (200):

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "usuario": {
    "id": "507f1f77bcf86cd799439011",
    "mail": "juan@example.com"
  }
}
```

**Errores**:

- 400: Todos los campos son obligatorios
- 400: Credenciales inválidas

---

#### 3. **POST /api/Usuarios/solicitar-codigo** (Recuperación de contraseña - Paso 1)

**Autenticación**: No requerida

**Body**:

```json
{
  "mail": "juan@example.com"
}
```

**Response** (200):

```json
{
  "message": "Si el email está registrado, recibirás un código de recuperación"
}
```

**Notas**:

- Por seguridad, siempre retorna 200 aunque el email no exista
- El código se envía por email y expira en 15 minutos

---

#### 4. **POST /api/Usuarios/validar-codigo** (Recuperación de contraseña - Paso 2)

**Autenticación**: No requerida

**Body**:

```json
{
  "mail": "juan@example.com",
  "code": "123456"
}
```

**Response** (200):

```json
{
  "message": "Código válido",
  "resetToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Errores**:

- 400: Código inválido o expirado
- 400: Código ha expirado (> 15 min)
- 400: Demasiados intentos (máx 3), solicita uno nuevo

---

#### 5. **POST /api/Usuarios/restablecer-contrasena** (Recuperación de contraseña - Paso 3)

**Autenticación**: No requerida

**Body**:

```json
{
  "resetToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "nuevaContrasena": "nuevoPassword123"
}
```

**Response** (200):

```json
{
  "message": "Contraseña restablecida correctamente"
}
```

**Errores**:

- 400: Token inválido o expirado
- 400: Contraseña mínimo 6 caracteres
- 404: Usuario no encontrado

---

#### 6. **GET /api/Usuarios** (Listar todos)

**Autenticación**: ✅ Requerida (Bearer token)

**Response** (200):

```json
{
  "message": "Se encontraron todos los usuarios",
  "data": [
    {
      "id": "507f1f77bcf86cd799439011",
      "nombre": "Juan",
      "apellido": "Pérez",
      "tel": 1234567890,
      "mail": "juan@example.com",
      "fotoPerfil": "/uploads/usuario/507f1f77bcf86cd799439011/image.jpg",
      "contratos": [],
      "reservas": []
    }
  ]
}
```

---

#### 7. **GET /api/Usuarios/:id** (Obtener por ID)

**Autenticación**: ✅ Requerida

**Response** (200):

```json
{
  "message": "Usuario encontrado",
  "data": {
    /* mismo formato que GET /Usuarios */
  }
}
```

**Errores**:

- 404: Usuario no encontrado

---

#### 8. **PUT /api/Usuarios/:id** (Actualizar - Reemplazo)

**Autenticación**: ✅ Requerida

**Body**:

```json
{
  "nombre": "Juan Actualizado",
  "apellido": "Pérez",
  "tel": 9999999999,
  "mail": "juannuevo@example.com",
  "contrasena": "newPass123",
  "fotoPerfil": "<archivo opcional>"
}
```

**Response** (200): Usuario actualizado

**Notas**:

- Maneja hashing automático de contraseña
- Maneja migración de fotos desde `/temp/` a carpeta de usuario

---

#### 9. **PATCH /api/Usuarios/:id** (Actualizar - Parcial)

**Autenticación**: ✅ Requerida

**Body**: Mismo que PUT, pero solo envía campos a actualizar

**Response** (200): Usuario actualizado

---

#### 10. **DELETE /api/Usuarios/:id** (Eliminar)

**Autenticación**: ✅ Requerida

**Response** (200):

```json
{
  "message": "usuario borrado"
}
```

---

### CONTRATOS

#### 1. **POST /api/Contratos/contratar** (Contratar membresía)

**Autenticación**: No requerida

**Body**:

```json
{
  "usuarioId": "507f1f77bcf86cd799439011",
  "membresiaId": "507f1f77bcf86cd799439012"
}
```

**Response** (201):

```json
{
  "message": "Contrato creado exitosamente. Pendiente de pago.",
  "data": {
    "contrato": {
      "id": "507f1f77bcf86cd799439013",
      "fecha_hora_ini": "2025-05-03T10:00:00Z",
      "fecha_hora_fin": "2025-07-03T10:00:00Z",
      "estado": "pendiente",
      "usuario": "507f1f77bcf86cd799439011",
      "membresia": "507f1f77bcf86cd799439012"
    },
    "esRenovacion": false,
    "contratoAnterior": null
  }
}
```

**Validaciones**:

- Usuario y membresía deben existir
- Máximo 2 contratos PENDIENTE por usuario
- Si existe contrato activo vigente (PAGADO/PENDIENTE con fecha_fin > now):
  - Nuevo contrato inicia cuando termina el anterior (renovación)
  - Retorna `esRenovacion: true` y detalles del contrato anterior

**Errores**:

- 400: ID de usuario/membresía requeridos
- 400: Límite de contratos excedido (2 pendientes)
- 404: Usuario/membresía no encontrado

---

#### 2. **PATCH /api/Contratos/cancelar/:contratoId** (Cancelar contrato)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "Contrato cancelado exitosamente",
  "data": {
    "contrato": {
      /* contrato con estado: "cancelado" */
    },
    "estadoAnterior": "pendiente"
  }
}
```

**Validaciones**:

- Solo se pueden cancelar contratos PENDIENTE
- Registra `fechaCancelacion`

**Errores**:

- 404: Contrato no encontrado
- 400: Contrato no está en estado PENDIENTE

---

#### 3. **POST /api/Contratos/verificar-vencimientos** (Verificar vencimientos)

**Autenticación**: No requerida (pero se recomienda admin)

**Response** (200):

```json
{
  "message": "Se actualizaron X contratos vencidos",
  "data": {
    "contratosActualizados": 5,
    "fechaVerificacion": "2025-05-03T10:00:00Z"
  }
}
```

**Operación**: Busca contratos PAGADO con fecha_fin < now y cambia estado a VENCIDO

---

#### 4. **GET /api/Contratos/usuario/:usuarioId** (Obtener contratos del usuario)

**Autenticación**: ✅ Requerida

**Response** (200):

```json
{
  "message": "Contratos del usuario obtenidos exitosamente",
  "data": {
    "usuario": {
      "id": "507f1f77bcf86cd799439011",
      "nombre": "Juan",
      "apellido": "Pérez"
    },
    "resumen": {
      "totalContratos": 3,
      "contratosActivos": 1,
      "proximosContratos": 1,
      "contratosPendientes": 1
    },
    "contratos": {
      "activos": [
        /* contratos PAGADO vigentes */
      ],
      "proximos": [
        /* contratos PAGADO futuros */
      ],
      "pendientes": [
        /* contratos PENDIENTE */
      ],
      "cancelados": [
        /* contratos CANCELADO */
      ],
      "vencidos": [
        /* contratos VENCIDO */
      ]
    }
  }
}
```

---

#### 5. **GET /api/Contratos/admin/estadisticas** (Estadísticas globales)

**Autenticación**: No requerida (se ejecuta auto)

**Response** (200):

```json
{
  "message": "Estadísticas obtenidas exitosamente",
  "data": {
    "totalContratos": 50,
    "pendientes": 10,
    "pagados": 30,
    "cancelados": 5,
    "vencidos": 5,
    "activos": 25,
    "fechaConsulta": "2025-05-03T10:00:00Z"
  }
}
```

---

#### 6. **GET /api/Contratos/filtrado** (Usuarios sin contrato - Admin)

**Autenticación**: ✅ Admin token requerido

**Query**: `?estado=sin-contrato`

**Response** (200):

```json
{
  "data": [
    {
      "idUsuario": "507f1f77bcf86cd799439011",
      "nombre": "Juan",
      "apellido": "Pérez",
      "mail": "juan@example.com"
    }
  ]
}
```

---

#### 7. **GET /api/Contratos** (Listar todos)

**Autenticación**: No requerida

**Response** (200): Array de contratos con usuario y membresia populados

---

#### 8. **GET /api/Contratos/:id** (Obtener por ID)

**Autenticación**: ✅ Requerida

**Response** (200): Contrato individual

---

#### 9. **POST /api/Contratos** (Crear manual)

**Autenticación**: No requerida

**Body**:

```json
{
  "fecha_hora_ini": "2025-05-03T10:00:00Z",
  "fecha_hora_fin": "2025-07-03T10:00:00Z",
  "estado": "pendiente",
  "usuario": "507f1f77bcf86cd799439011",
  "membresia": "507f1f77bcf86cd799439012"
}
```

**Response** (201): Contrato creado

---

#### 10-12. **PUT, PATCH, DELETE /api/Contratos/:id**

- **PUT/PATCH**: Actualización de contrato
- **DELETE**: Eliminación

---

### RESERVAS

#### 1. **POST /api/Reservas** (Crear reserva)

**Autenticación**: ✅ Requerida

**Body**:

```json
{
  "usuario": "507f1f77bcf86cd799439011",
  "clase": "507f1f77bcf86cd799439014"
}
```

**Response** (201):

```json
{
  "message": "reserva creada",
  "data": {
    "id": "507f1f77bcf86cd799439015",
    "fecha_hora": "2025-05-03T10:00:00Z",
    "estado": "pendiente",
    "usuario": "507f1f77bcf86cd799439011",
    "clase": "507f1f77bcf86cd799439014"
  }
}
```

**Validaciones**:

- Clase existe
- `clase.cupo_disp > 0` (hay cupo)
- Actual + 30 min < fecha_inicio de clase
- Usuario tiene Contrato PAGADO vigente para esa fecha
- Decrementa automáticamente `clase.cupo_disp`

**Errores**:

- 404: Clase no encontrada
- 400: No hay cupo disponible
- 400: Las reservas se cierran 30 minutos antes
- 400: No tienes contrato vigente y pagado

---

#### 2. **PUT/PATCH /api/Reservas/:id** (Actualizar reserva)

**Autenticación**: ✅ Requerida

**Body**:

```json
{
  "estado": "cancelada"
}
```

**Response** (200): Reserva actualizada

**Validaciones**:

- Solo el propietario (usuario) puede modificar su reserva
- Si cancela, validar que no sea dentro de 30 min antes
- No permitir cancelar reserva CERRADA
- No permitir cambiar estado de reserva CANCELADA
- Si cancela: liberar cupo (`clase.cupo_disp += 1`)

**Errores**:

- 403: No tienes permiso para modificar esta reserva
- 400: No se puede cancelar dentro de 30 minutos
- 400: No se puede cancelar una reserva cerrada
- 400: No se puede cambiar estado de reserva cancelada
- 400: Estado de reserva inválido

---

#### 3. **GET /api/Reservas** (Listar reservas - Del usuario autenticado)

**Autenticación**: ✅ Requerida

**Query** (opcional): `?usuario=507f1f77bcf86cd799439011`

**Response** (200): Array de reservas del usuario (o todas si no hay filtro)

---

#### 4. **GET /api/Reservas/:id** (Obtener por ID)

**Autenticación**: ✅ Requerida

**Response** (200): Reserva individual

---

#### 5. **DELETE /api/Reservas/:id** (Eliminar reserva)

**Autenticación**: ✅ Requerida

**Response** (200): Reserva eliminada

---

#### 6. **GET /api/Reservas/filtrado** (Reservas por clase - Admin)

**Autenticación**: ✅ Admin token requerido

**Query**: `?claseId=507f1f77bcf86cd799439014`

**Response** (200):

```json
{
  "message": "Reservas filtradas por clase",
  "data": [
    {
      "idActividad": "507f1f77bcf86cd799439016",
      "nombreActividad": "Yoga",
      "idUsuario": "507f1f77bcf86cd799439011",
      "nombre": "Juan",
      "apellido": "Pérez",
      "fecha_hora_ini": "2025-05-03T10:00:00Z",
      "fecha_hora_fin": "2025-05-03T11:00:00Z",
      "fecha_hora_reserva": "2025-05-01T15:00:00Z",
      "estado_reserva": "pendiente"
    }
  ]
}
```

---

#### 7. **POST /api/Reservas/actualizar** (Actualizar estados - Internal)

**Autenticación**: No requerida

**Operación**: Busca reservas PENDIENTE cuyas clases empiezan en ≤30 min y cambia a CERRADA

**Response** (200):

```json
{
  "message": "X reservas actualizadas correctamente",
  "data": {
    "actualizadas": 5,
    "detalles": [
      {
        "reservaId": "...",
        "claseId": "...",
        "fechaInicioClase": "...",
        "minutosRestantes": 25,
        "estadoAnterior": "pendiente",
        "estadoNuevo": "cerrada"
      }
    ]
  }
}
```

---

### ACTIVIDADES

#### 1. **GET /api/actividad** (Listar todas)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "found all activities",
  "data": [
    {
      "id": "507f1f77bcf86cd799439016",
      "nombre": "Yoga",
      "descripcion": "Clase de yoga relajante",
      "cupo": 20,
      "imagenUrl": "/public/uploads/actividad/507f1f77bcf86cd799439016/yoga.jpg",
      "entrenadores": [
        /* entrenadores relacionados */
      ],
      "clases": [
        /* clases de esta actividad */
      ]
    }
  ]
}
```

---

#### 2. **GET /api/actividad/:id** (Obtener por ID)

**Autenticación**: No requerida

**Response** (200): Actividad individual

---

#### 3. **POST /api/actividad** (Crear actividad)

**Autenticación**: No requerida (pero se recomienda admin)

**Body**:

```json
{
  "nombre": "Yoga",
  "descripcion": "Clase de yoga relajante",
  "cupo": 20,
  "imagenUrl": null
}
```

**Response** (201): Actividad creada

**Validaciones**:

- Nombre único
- Descripción obligatoria

---

#### 4. **PUT/PATCH /api/actividad/:id** (Actualizar)

**Autenticación**: No requerida

**Body**: Campos a actualizar

**Response** (200): Actividad actualizada

---

#### 5. **DELETE /api/actividad/:id** (Eliminar)

**Autenticación**: No requerida

**Response** (200): Actividad eliminada

---

#### 6. **POST /api/actividad/:id/imagen** (Subir imagen)

**Autenticación**: No requerida

**Content-Type**: `multipart/form-data`

**Field**: `imagen` (archivo)

**Response** (200):

```json
{
  "message": "imagen actualizada",
  "data": {
    /* actividad actualizada */
  }
}
```

---

### CLASES

#### 1. **GET /api/clases** (Listar todas)

**Autenticación**: No requerida

**Query** (opcional): `?actividadId=507f1f77bcf86cd799439016`

**Response** (200): Array de clases con entrenador, actividad y reservas populadas

---

#### 2. **GET /api/clases/todas-ordenadas** (Listar ordenadas)

**Autenticación**: No requerida

**Query**:

- `?fecha=2025-05-03` (filtro por fecha YYYY-MM-DD)
- `?actividadId=507f1f77bcf86cd799439016` (filtro por actividad)

**Response** (200):

```json
{
  "message": "Clases obtenidas correctamente",
  "data": [
    {
      "id": "507f1f77bcf86cd799439014",
      "fecha_hora_ini": "2025-05-03T10:00:00Z",
      "fecha_hora_fin": "2025-05-03T11:00:00Z",
      "cupo_disp": 5,
      "entrenador": {
        /* detalles del entrenador */
      },
      "actividad": {
        /* detalles de la actividad */
      },
      "reservas": [
        /* reservas de esta clase */
      ]
    }
  ]
}
```

---

#### 3. **GET /api/clases/con-reservas-usuario** (Clases con flag de reserva del usuario)

**Autenticación**: No requerida

**Query**:

- `?fecha=2025-05-03`
- `?actividadId=507f1f77bcf86cd799439016`
- `?usuarioId=507f1f77bcf86cd799439011`

**Response** (200): Clases con campo adicional `reservaUsuario` (null si no tiene reserva)

---

#### 4. **POST /api/clases** (Crear clase)

**Autenticación**: No requerida

**Body**:

```json
{
  "fecha_hora_ini": "2025-05-03T10:00:00Z",
  "fecha_hora_fin": "2025-05-03T11:00:00Z",
  "cupo_disp": 20,
  "entrenador": "507f1f77bcf86cd799439017",
  "actividad": "507f1f77bcf86cd799439016"
}
```

**Response** (201): Clase creada

**Validaciones**:

- Entrenador debe estar relacionado con la actividad (M:M)

**Errores**:

- 400: El entrenador no está habilitado para dictar esta actividad

---

#### 5. **PUT/PATCH /api/clases/:id** (Actualizar)

**Autenticación**: No requerida

**Body**: Campos a actualizar

**Response** (200): Clase actualizada

**Validaciones**: Mismas que POST

---

#### 6. **PATCH /api/clases/:id/actualizar-cupo** (Decrementar cupo)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "Cupo actualizado",
  "data": {
    /* clase con cupo_disp -= 1 */
  }
}
```

**Errores**:

- 400: No hay cupos disponibles

---

#### 7. **DELETE /api/clases/:id** (Eliminar)

**Autenticación**: No requerida

**Response** (200): Clase eliminada

---

### ENTRENADORES

#### 1. **GET /api/entrenadores** (Listar todos)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "se encontraron todos los entrenadores",
  "data": [
    {
      "id": "507f1f77bcf86cd799439017",
      "nombre": "Carlos",
      "apellido": "García",
      "tel": 1234567890,
      "mail": "carlos@example.com",
      "frase": "¡Vamos a entrenar!",
      "fotoUrl": "/public/uploads/entrenador/507f1f77bcf86cd799439017/foto.jpg",
      "actividades": [
        /* actividades que dicta */
      ],
      "clases": [
        /* clases que dicta */
      ]
    }
  ]
}
```

---

#### 2. **GET /api/entrenadores/:id** (Obtener por ID)

**Autenticación**: No requerida

**Response** (200): Entrenador individual

---

#### 3. **POST /api/entrenadores** (Crear entrenador)

**Autenticación**: No requerida

**Body**:

```json
{
  "nombre": "Carlos",
  "apellido": "García",
  "tel": 1234567890,
  "mail": "carlos@example.com",
  "frase": "¡Vamos a entrenar!",
  "actividades": ["507f1f77bcf86cd799439016", "507f1f77bcf86cd799439018"]
}
```

**Response** (201): Entrenador creado

---

#### 4. **PUT/PATCH /api/entrenadores/:id** (Actualizar)

**Autenticación**: No requerida

**Response** (200): Entrenador actualizado

---

#### 5. **DELETE /api/entrenadores/:id** (Eliminar)

**Autenticación**: No requerida

**Response** (200): Entrenador eliminado

---

### MEMBRESÍAS

#### 1. **GET /api/membresias** (Listar todas)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "Se encontraron todas las membresías",
  "data": [
    {
      "id": "507f1f77bcf86cd799439012",
      "nombre": "Plan Básico",
      "descripcion": "Acceso a todas las clases",
      "precio": 4999,
      "meses": 1,
      "contratos": [
        /* contratos con esta membresía */
      ]
    }
  ]
}
```

---

#### 2. **GET /api/membresias/:id** (Obtener por ID)

**Autenticación**: No requerida

**Response** (200): Membresía individual

---

#### 3. **POST /api/membresias** (Crear membresía)

**Autenticación**: No requerida

**Body**:

```json
{
  "nombre": "Plan Básico",
  "descripcion": "Acceso a todas las clases",
  "precio": 4999,
  "meses": 1
}
```

**Response** (201): Membresía creada

---

#### 4. **PUT/PATCH /api/membresias/:id** (Actualizar)

**Autenticación**: No requerida

**Response** (200): Membresía actualizada

---

#### 5. **DELETE /api/membresias/:id** (Eliminar)

**Autenticación**: No requerida

**Response** (200): Membresía eliminada

---

### VALORACIONES

#### 1. **GET /api/valoraciones** (Listar valoraciones de entrenador)

**Autenticación**: No requerida

**Query**: `?entrenador=507f1f77bcf86cd799439017` (requerido)

**Response** (200):

```json
{
  "message": "valoraciones encontradas",
  "data": [
    {
      "id": "507f1f77bcf86cd799439019",
      "rating": 5,
      "comentario": "Excelente entrenador",
      "usuario": {
        /* detalles del usuario */
      },
      "entrenador": "507f1f77bcf86cd799439017"
    }
  ]
}
```

**Errores**:

- 400: Debe enviar el id del entrenador en query

---

#### 2. **GET /api/valoraciones/resumen** (Resumen de valoraciones)

**Autenticación**: No requerida

**Query**: `?entrenador=507f1f77bcf86cd799439017`

**Response** (200):

```json
{
  "message": "resumen de valoraciones",
  "data": {
    "promedio": 4.5,
    "cantidad": 10
  }
}
```

---

#### 3. **POST /api/valoraciones** (Crear o actualizar valoración)

**Autenticación**: No requerida

**Body**:

```json
{
  "rating": 5,
  "comentario": "Excelente entrenador",
  "usuario": "507f1f77bcf86cd799439011",
  "entrenador": "507f1f77bcf86cd799439017"
}
```

**Response** (201 o 200):

```json
{
  "message": "valoración creada" | "valoración actualizada",
  "data": { /* valoración */ }
}
```

**Validaciones**:

- `rating`: 1-5 (número entero)
- `usuario` y `entrenador`: requeridos
- Upsert: si existe, actualiza; si no, crea
- Unique constraint: no puede haber 2 valoraciones del mismo usuario al mismo entrenador

**Errores**:

- 400: Usuario y entrenador requeridos
- 400: Rating requerido
- 400: Rating debe ser número entre 1 y 5
- 400: Comentario inválido

---

### ADMIN

#### 1. **POST /api/admin/login** (Login de administrador)

**Autenticación**: No requerida

**Body**:

```json
{
  "usuario": "admin",
  "contrasena": "adminPassword123"
}
```

**Response** (200):

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Notas**:

- Las credenciales se validan contra variables de entorno
- El token se usa en header `Authorization: Bearer <admin_token>`

---

### STRIPE

#### 1. **GET /api/stripe/metodos-pago** (Obtener métodos de pago disponibles)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "Métodos de pago obtenidos",
  "data": [
    {
      "id": "stripe",
      "nombre": "Tarjeta de crédito/débito",
      "descripcion": "Pago mediante Stripe",
      "activo": true
    },
    {
      "id": "transferencia",
      "nombre": "Transferencia bancaria",
      "descripcion": "Pago simulado",
      "activo": true
    },
    {
      "id": "efectivo",
      "nombre": "Efectivo",
      "descripcion": "Pago simulado en efectivo",
      "activo": true
    }
  ]
}
```

---

#### 2. **POST /api/stripe/create-checkout-session** (Crear sesión de pago)

**Autenticación**: No requerida

**Body**:

```json
{
  "contratoId": "507f1f77bcf86cd799439013",
  "metodoPago": "stripe"
}
```

**Response** (201):

```json
{
  "message": "Sesión de pago creada",
  "data": {
    "sessionId": "cs_live_...",
    "clientSecret": "cs_live_...",
    "url": "https://checkout.stripe.com/pay/cs_live_...",
    "contratoId": "507f1f77bcf86cd799439013"
  }
}
```

**Validaciones**:

- Contrato debe existir
- Contrato debe estar en estado PENDIENTE
- Método de pago válido

**Errores**:

- 404: Contrato no encontrado
- 400: Contrato no está pendiente de pago
- 400: Método de pago inválido

---

#### 3. **GET /api/stripe/session/:sessionId** (Consultar estado de sesión)

**Autenticación**: No requerida

**Response** (200):

```json
{
  "message": "Estado de sesión obtenido",
  "data": {
    "sessionId": "cs_live_...",
    "status": "complete" | "open" | "expired",
    "paymentStatus": "paid" | "unpaid" | "no_payment_required",
    "customer": {
      "email": "juan@example.com",
      "name": "Juan Pérez"
    },
    "amount": 4999,
    "currency": "ARS",
    "contratoId": "507f1f77bcf86cd799439013"
  }
}
```

**Notas**:

- Si `paymentStatus === "paid"`, el contrato se marca automáticamente como PAGADO
- Si `status === "expired"`, la sesión ya no es válida

---

#### 4. **POST /api/stripe/pagar-transferencia** (Registrar pago por transferencia)

**Autenticación**: No requerida

**Body**:

```json
{
  "contratoId": "507f1f77bcf86cd799439013",
  "numeroComprobante": "TRX123456789",
  "banco": "Banco Nación"
}
```

**Response** (200):

```json
{
  "message": "Pago registrado exitosamente",
  "data": {
    "contrato": {
      "id": "507f1f77bcf86cd799439013",
      "estado": "pagado",
      "fechaPago": "2025-05-03T10:00:00Z",
      "metodoPago": "transferencia"
    }
  }
}
```

**Validaciones**:

- Contrato debe existir y estar PENDIENTE
- Número de comprobante único (si aplica)

**Errores**:

- 404: Contrato no encontrado
- 400: Contrato no está pendiente

---

#### 5. **POST /api/stripe/pagar-efectivo** (Registrar pago en efectivo)

**Autenticación**: No requerida

**Body**:

```json
{
  "contratoId": "507f1f77bcf86cd799439013",
  "monto": 4999
}
```

**Response** (200):

```json
{
  "message": "Pago en efectivo registrado",
  "data": {
    "contrato": {
      "id": "507f1f77bcf86cd799439013",
      "estado": "pagado",
      "fechaPago": "2025-05-03T10:00:00Z",
      "metodoPago": "efectivo"
    }
  }
}
```

**Validaciones**:

- Contrato debe existir y estar PENDIENTE
- Monto debe coinciditar con membresía (si aplica validación)

**Errores**:

- 404: Contrato no encontrado
- 400: Monto inválido

---

#### 6. **POST /api/stripe/webhook** (Webhook de Stripe)

**Autenticación**: Validación de firma Stripe

**Body** (raw JSON):

```json
{
  "id": "evt_...",
  "type": "checkout.session.completed",
  "data": {
    "object": {
      "id": "cs_live_...",
      "payment_status": "paid",
      "customer_email": "juan@example.com",
      "metadata": {
        "contratoId": "507f1f77bcf86cd799439013"
      }
    }
  }
}
```

**Eventos procesados**:

- `checkout.session.completed` → Marca contrato como PAGADO
- `charge.failed` → Mantiene contrato PENDIENTE
- `charge.refunded` → Cambia contrato a CANCELADO (opcional)

**Response** (200):

```json
{
  "received": true
}
```

**Notas**:

- Verifica firma del webhook usando `Stripe-Signature`
- Se configura en panel de Stripe
- URL webhook: `https://backendappgym.onrender.com/api/stripe/webhook`

---

## Información Adicional

### Endpoints que se ejecutan automáticamente

- `/api/Contratos/verificar-vencimientos`: Se ejecuta al iniciar el servidor (cron job)
- `/api/Reservas/actualizar`: Se ejecuta al iniciar el servidor (cron job)

### Almacenamiento de archivos

- **Ruta base**: `/public/uploads/`
- **Estructura**: `/public/uploads/[entidad]/[id]/[archivo]`
- **Formato**: URL en respuesta es `/uploads/[entidad]/[id]/[archivo]` (sin `/public/`)
- **Archivos soportados**: `.jpg`, `.jpeg`, `.png`, `.webp`, `.gif`, `.avif`

