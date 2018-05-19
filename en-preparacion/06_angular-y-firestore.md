# Angular (revision 6) y Firestore
### Mayo 2018

[Firebase](https://firebase.google.com/?hl=es-419) es una plataforma de Google que permite crear aplicaciones en forma rápida con una serie de servicios disponibles. **Realtime Database** nos permite contar con un sistema de almacenamiento de datos no-relacional en tiempo real. Cuando un valor en la base de datos cambia, ese cambio se propaga a todos los clientes conectados a nuestra base de datos.  
La alta demanda del producto y la ausencia de algunas características que permitan utilizar a Realtime Database como una verdadera base de datos no-relacional (ver las diferencias entre productos [acá](https://firebase.google.com/docs/firestore/rtdb-vs-firestore)), [motivó a Google a lanzar en Octubre del 2017](https://cloud.google.com/firestore/docs/release-notes) un nuevo producto llamado [Cloud Firestore](https://firebase.google.com/docs/firestore/?hl=es-419).  
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

## Sobe Firestore
Firestore trabaja con **documentos**. Los documentos corresponden a objetos con una serie de atributos. Cada documento posee su identificador único alfanumérico.
El tipo de información que es posible guardar en un documento corresponde a:

* Strings
* Números
* Booleanos
* Null
* Objetos
* Arrays
* Referencias

Los documentos están organizados en grupos llamados **colecciones**. Una de las grandes ventajas de Firestore sobre Realtime Database, es que podemos realizar búsquedas de documentos mediante un atributo de un objeto. Es posible realizar búsquedas complejas por más de un atributo, previo a la creación de un [índice](https://firebase.google.com/docs/firestore/query-data/indexing?hl=es-419).

## Configuración de Firestore en Angular
Generaremos un nuevo proyecto de Angular con el siguiente comando

```bash
ng new tutorial-angular-firestore
```

Para instalar Firestore en Angular es necesario instalar las dependencias necesarias. El equipo de Google tiene un paquete oficial llamado [angularfire2](https://github.com/angular/angularfire2), el cual utilizaremos para integrar Firestore en nuestra aplicación.  

```bash
npm install firebase angularfire2 --save
```

Recuerdan el objeto ```config``` que vimos en pasos anteriores? Bueno, ahora será necesario ingresar esa información en el archivo ```src/app/environments/environment.ts``` de nuestra aplicación. Crearemos un nuevo atributo dentro del objeto environment llamado **firebase** y dentro agregaremos lo siguiente:

```typescript
export const environment = {
  production: false,
  firebase: {
    apiKey: "XXX",
    authDomain: "XXX",
    databaseURL: "XXX",
    projectId: "XXX",
    storageBucket: "XXX",
    messagingSenderId: "XXX"  
  }
};
```
Una vez realizado ese paso, modificaremos el archivo ```src/app/app.module.ts``` y agregaremos dos nuevos imports 

* ```import { AngularFireModule } from 'angularfire2';```
* ```import { environment } from '../environments/environment';```

Nuestro archivo ```app.module.ts``` debe contener la siguiente información:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AngularFireModule } from 'angularfire2';
import { environment } from '../environments/environment';


import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AngularFireModule.initializeApp(environment.firebase)
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Que es lo que vamos a construir?
Vamos a construir una pequeña aplicación en donde podamos listar e ingresar fotografías de gatos, las que se desplegarán en una lista. Para ello, con nuestra aplicación configurada, seguiremos una serie de pasos

![Imagen Aplicación](http://nicoavila.s3.amazonaws.com/articulos/06_06a-construir.jpg)

> El framework CSS utilizado en este tutorial es [Furtive](http://furtive.co/), un microframework con lo básico. La integración con de Angular con este framework será abordado en otro artículo <3

### Paso 1: Creación de un Service para Firestore
Para encapsular toda la lógica de comunicación con Firestore, crearemos un nuevo servicio. Ejecutaremos el siguiente comando en la terminal:

```ng g service services/firestore/firestore```

Esto creará los archivos ```firestore.service.ts``` y ```firestore.service.spec.ts``` en la ruta ```src/app/services/firestore/```.

Agregaremos lo siguiente al servicio:

```typescript
import { Injectable } from '@angular/core';
import { AngularFirestore, AngularFirestoreDocument } from 'angularfire2/firestore';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class FirestoreService {

  constructor(
    private firestore: AngularFirestore
  ) {}

  //Crea un nuevo gato
  public createCat(data: {name: string, urlImage: string}) {
    return this.firestore.collection('cats').add(data);
  }

  //Obtiene un gato
  public getCat(documentId: string) {
    return this.firestore.collection('cats').doc(documentId).snapshotChanges();
  }

  //Obtiene todos los gatos
  public getCats() {
    return this.firestore.collection('cats').snapshotChanges();
  }

  //Actualiza un gato
  public updateCat(documentId: string, data: {name?: string, urlImage?:string}) {
    return this.firestore.collection('cats').doc(documentId).set(data);
  }
}
```

Hemos definido una serie de **métodos públicos** que nos permiten realizar un CRUD sobre la colección **cats** en nuestro proyecto. A continuación revisaremos el detalle de cada uno de los métodos:

* ```constructor()```: Corresponde al constructor de nuestra clase FirestoreService. Los constructores por definición son métodos que se ejecutan cada vez que se crea una instancia de un objeto. Se ha definido el atributo firestore, el cual corresponde a una instancia de AngularFirestore, que expone todos los métodos que podemos utilizar en la librería.

* ```createCat()```: Este método añadirá un nuevo documento en la colección *cats* mediante el uso del método ```add()```.

* ```getCat()```: Este método obtiene un documento en específico de la colección *cats*. Retorna un *Observable*.

* ```getCats()```: Este método obtiene todos los documentos de la colección *cats*. Retorna un *Observable*.

* ```updateCat()```: Este médoto actualiza un documento en específico de la colección *cats*. Retorna un *Observable*.

### Paso 2: Creación del componente CatsComponent
Crearemos un nuevo componente llamado **CatsComponent** que permita utilizar el servicio **FirestoreService**. Para ello, en el terminal, ejecutaremos lo siguiente:

```bash
ng g component cats
```

Esto creará los archivos ```cats.component.ts```, ```cats.component.spec.ts```, ```cats.component.html``` y ```cats.component.css``` en la ruta ```src/app/cats/```.

En el archivo ```cats.component.ts```agregaremos lo siguiente:

```typescript
import { Component, OnInit } from '@angular/core';
import { FirestoreService } from '../services/firestore/firestore.service';

@Component({
  selector: 'app-cats',
  templateUrl: './cats.component.html',
  styleUrls: ['./cats.component.css']
})
export class CatsComponent implements OnInit {

  public cats = [];

  constructor(
    private firestoreService: FirestoreService
  ) { }

  ngOnInit() {
    this.firestoreService.getCats().subscribe((catsSnapshot) => {
      this.cats = [];
      catsSnapshot.forEach((catData: any) => {
        this.cats.push({
          id: catData.payload.doc.id,
          data: catData.payload.doc.data()
        });
      })
    });
  }
}
```


