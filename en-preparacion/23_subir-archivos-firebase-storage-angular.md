# Subir archivos a Firebase Cloud Storage con Angular 7

![Subir Archivos](http://nicoavila.s3.amazonaws.com/articulos/23_01subir-archivos-firebase-storage-angular.png)

Angular provee dos formas de manejar el input generado por el usuario: con [Reactive Forms](https://angular.io/guide/reactive-forms) o [Template-driven Forms](https://angular.io/guide/forms). El manejo de la información es relativamente sencillo para ambos casos.  
Los problemas aparecen cuando necesitamos manejar información en formularios con un enctype distinto al habitual: [multipart/form-data](https://dev.to/sidthesloth92/understanding-html-form-encoding-url-encoded-and-multipart-forms-3lpa).  
En este tutorial veremos como implementar un formulario que contenga un elemento ```<input type="file">``` para subir su contenido a Firebase Storage utilizando [Angular](https://angular.io).

## ¿Qué es Firebase?
Firebase es un ecosistema de servicios para desarrolladores de Google. Desde su adquisición el año 2012, Google se ha centrado en proveer una plataforma robusta para suplir la necesidad de casos de uso comunes de nuestros sistemas.

> ¿Sabias qué en Angular Chile tenemos un artículo que te enseña a utilizar Cloud Firestore? Haz click en el [siguiente vínculo](https://medium.com/angular-chile/angular-6-y-firestore-b7f270adcc96) para saber más.

## Firebase Cloud Storage
En las propias palabras de Google:
> Cloud Storage para Firebase es un servicio de almacenamiento de objetos potente, simple y rentable construido para la escala de Google

Para utilizar Cloud Firestore en Angular es necesario que tengas una cuenta Google. Si aun no posees una cuenta, puedes [crear una aquí](https://accounts.google.com/signup/v2/webcreateaccount?hl=es-419&flowName=GlifWebSignIn&flowEntry=SignUp)

## Creando un proyecto en Firebase
Con tu cuenta Google ingresa a la [Consola de Administración de Firebase](https://console.firebase.google.com) y crea un nuevo proyecto, haciendo clik sobre 

## Creando nuestro proyecto de Angular para trabajar