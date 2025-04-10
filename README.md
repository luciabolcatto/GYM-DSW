# GYM-DSW
 Sistema de Gestión para Gimnasios 
# Propuesta TP DSW
Este es un sistema web full stack diseñado para la administración de gimnasios. Permite gestionar usuarios, planes de entrenamiento, reservas de clases y planes. 
## Grupo
### Integrantes
* 51027 - Arce Nahuel
* 50947 - Bolcatto Lucía Belén
* 51392 - Rallip Sánchez Ismael
  
### Repositorios
* [frontend app](http://hyperlinkToGihubOrGitlab)
* [backend app](http://hyperlinkToGihubOrGitlab)
*Nota*: si utiliza un monorepo indicar un solo link con fullstack app.

## Tema
### Descripción
Sistema web para la gestión de gimnasios. Permite la administración de usuarios (socios, entrenadores, administradores), rutinas de entrenamiento, reservas de clases y control de pagos y membresías.

### Modelo
![imagen del modelo](images/DER.png)

*Nota*: incluir un link con la imagen de un modelo, puede ser modelo de dominio, diagrama de clases, DER. Si lo prefieren pueden utilizar diagramas con [Mermaid](https://mermaid.js.org) en lugar de imágenes.

## Alcance Funcional 

### Alcance Mínimo

*Nota*: el siguiente es un ejemplo para un grupo de 3 integrantes para un sistema de hotel. El 

Regularidad:
|Req|Detalle|
|:-|:-|
|CRUD simple|1. CRUD Actividad <br>2. CRUD Plan <br>3. CRUD Usuario |
|CRUD dependiente|1. CRUD Precio {depende de} CRUD Plan <br>2. CRUD Clase {depende de} CRUD actividad |
|Listado<br>+<br>detalle| 1. Listado de usuarios fltrado por planes, muestra idU Usuario, id Actividad y detalles del mismo. => detalle CRUD Plan, CRUD Actividad, Crud Usuario <br> 2. Listado de reservas filtradas por clases, muestra id Actividad, idU Usuario, fecha-hora-ini y fecha-hora-fin Clase => detalle CRUD Usuario y CRUD actividad |
|CUU/Epic|1. Reservar una clase. <br>2. Contratar plan |


Adicionales para Aprobación
|Req|Detalle|
|:-|:-|
|CRUD | a definir <br><br><br><br>n<br><br>|
|CUU/Epic| a definir <br>2. <br>|


### Alcance Adicional Voluntario

*Nota*: El Alcance Adicional Voluntario es opcional, pero ayuda a que la funcionalidad del sistema esté completa y será considerado en la nota en función de su complejidad y esfuerzo.

|Req|Detalle|
|:-|:-|
|Listados |1. Estadía del día filtrado por fecha muestra, cliente, habitaciones y estado <br>2. Reservas filtradas por cliente muestra datos del cliente y de cada reserve fechas, estado cantidad de habitaciones y huespedes|
|CUU/Epic|1. Consumir servicios<br>2. Cancelación de reserva|
|Otros|1. Envío de recordatorio de reserva por email|
