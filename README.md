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


## Tema
### Descripción
Sistema web para la gestión de gimnasios. Permite la administración de usuarios (socios, entrenadores, ), rutinas de entrenamiento, reservas de clases y control de pagos y planes.

### Modelo
![imagen del modelo](images/DER.png)



## Alcance Funcional 

### Alcance Mínimo



Regularidad:
|Req|Detalle|
|:-|:-|
|CRUD simple|1. CRUD Actividad <br>2. CRUD Plan <br>3. CRUD Usuario |
|CRUD dependiente|1. CRUD Precio {depende de} CRUD Plan <br>2. CRUD Clase {depende de} CRUD Actividad |
|Listado<br>+<br>detalle| 1. Listado de usuarios fltrado por planes, muestra idU Usuario, id Actividad y detalles del mismo. => detalle CRUD Plan, CRUD Actividad, Crud Usuario <br> 2. Listado de reservas filtradas por clases, muestra id Actividad, idU Usuario, fecha-hora-ini y fecha-hora-fin Clase => detalle CRUD Usuario y CRUD Actividad |
|CUU/Epic|1. Reservar una clase. <br>2. Contratar plan |


Adicionales para Aprobación
|Req|Detalle|
|:-|:-|
|CRUD | A definir <br><br>|
|CUU/Epic| A definir <br><br>|


### Alcance Adicional Voluntario


|Req|Detalle|
|:-|:-|
|Listados | A definir <br>|
|CUU/Epic|1. Cancelacón reserva <br>|
|Otros|1. Notificación previa a turno |
