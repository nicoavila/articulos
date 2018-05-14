# Angular (revision 6) y Firestore
### Mayo 2018

[Firebase](https://firebase.google.com/?hl=es-419) es una plataforma de Google que permite crear aplicaciones en forma rápida con una serie de servicios disponibles. **Realtime Database** nos permite contar con un sistema de almacenamiento de datos no-relacional en tiempo real. Cuando un valor en la base de datos cambia, ese cambio se propaga a todos los clientes conectados a nuestra base de datos.  
La alta demanda del producto y la ausencia de algunas características que permitan utilizar a Realtime Database como una verdadera base de datos no-relacional, [motivó a Google a lanzar en Octubre del 2017](https://cloud.google.com/firestore/docs/release-notes) un nuevo producto llamado [Cloud Firestore](https://firebase.google.com/docs/firestore/?hl=es-419).  
**Firestore** es una base de datos orientada a documentos y colecciones. Posee las mismas características de Realtime Database (propagación de cambios en tiempo real a los clientes conectados reglas de seguridad, etc), pero con algunas mejoras que lo transforman en un producto prometedor.  
En esta guía, iremos paso a paso creando una pequeña aplicación en Angular (versión 6 lógicamente :smiley:) que lea / escriba información en Firestore.

![Angular y Firestore](http://nicoavila.s3.amazonaws.com/articulos/06_01angular-y-firestore.jpg)

## Antes de empezar: Creación de un proyecto en Firebase

El paso inicial es la creación de un nuevo proyecto en Firebase. Para ello, utilizando una cuenta Google (si no tienes una puedes [crear una aquí](https://accounts.google.com/SignUp?hl=es-419)), debemos dirigirnos a la [Consola de Administración de Firebase](https://console.firebase.google.com/). Debemos hacer click sobre el ícono **Agregar Proyecto**

![Creación de nuevo proyecto en Firebase](http://nicoavila.s3.amazonaws.com/articulos/06_02creacion-nuevo-proyecto.jpg)

Debemos ingresar un **nombre** para el nuevo proyecto. Para este tutorial usaremos el nombre **Angular Chile Firestore**. Ustedes deberán utilizar otro nombre que genere un identificador distinto, ya que los ID de proyectos en Google Cloud son únicos.  
Seleccionaremos la opción correspondiente al país y presionamos el botón **Crear proyecto**

![Modal creación proyecto](http://nicoavila.s3.amazonaws.com/articulos/06_03modal-creacion-nuevo-proyecto.jpg)

El proyecto tardará unos segundos en crearse. Al finalizar este proceso seremos redirigidos al dashboard del proyecto. Acá debemos hacer click sobre **Agregar Firebase a tu app web**

![Bienvenida Firebase](http://nicoavila.s3.amazonaws.com/articulos/06_04bienvenida-firebase.jpg)

Se desplegará un modal que contendrá toda la información necesaria para conectarnos al proyecto. El objeto ```config``` tiene una serie de atributos necesarios para configurar más adelante Firestore en Angular, por lo que debemos dejar esta información anotada en algún lado (o simplemente pueden volver a la consola y realizar el mismo paso anterior).

![Configuración Firebase](http://nicoavila.s3.amazonaws.com/articulos/06_05configuracion-firebase.jpg)






