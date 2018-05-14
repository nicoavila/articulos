# Angular (revision 6) y Firestore
### Mayo 2018

[Firebase](https://firebase.google.com/?hl=es-419) es una plataforma de Google que permite crear aplicaciones en forma rápida con una serie de servicios disponibles. **Realtime Database** nos permite contar con un sistema de almacenamiento de datos no-relacional en tiempo real. Cuando un valor en la base de datos cambia, ese cambio se propaga a todos los clientes conectados a nuestra base de datos.  
La alta demanda del producto y la ausencia de algunas características que permitan utilizar a Realtime Database como una verdadera base de datos no-relacional, [motivó a Google a lanzar en Octubre del 2017](https://cloud.google.com/firestore/docs/release-notes) un nuevo producto llamado [Cloud Firestore](https://firebase.google.com/docs/firestore/?hl=es-419).  
**Firestore** es una base de datos orientada a documentos y colecciones. Posee las mismas características de Realtime Database (propagación de cambios en tiempo real a los clientes conectados reglas de seguridad, etc), pero con algunas mejoras que lo transforman en un producto prometedor.  
En esta guía, iremos paso a paso creando una pequeña aplicación en Angular (versión 6 lógicamente :smiley:) que lea / escriba información en Firestore.

![Angular y Firestore](http://nicoavila.s3.amazonaws.com/articulos/06_01angular-y-firestore.jpg)

## Antes de empezar: Creación de un proyecto en Firebase

El paso inicial es la creación de un nuevo proyecto en Firebase. Para ello, utilizando una cuenta Google (si no tienes una puedes crear una aquí), 