# Angular 6 y Firestore
### Mayo 2018

![Angular y Firestore](http://nicoavila.s3.amazonaws.com/articulos/06_01angular-y-firestore.jpg)

[Firebase](https://firebase.google.com/?hl=es-419) es una plataforma de Google que permite crear aplicaciones en forma rápida con una serie de servicios disponibles. **Realtime Database** nos permite contar con un sistema de almacenamiento de datos no-relacional en tiempo real. Cuando un valor en la base de datos cambia, ese cambio se propaga a todos los clientes conectados a nuestra base de datos.  
La alta demanda del producto y la ausencia de algunas características que permitan utilizar a Realtime Database como una verdadera base de datos no-relacional (ver las diferencias entre productos [acá](https://firebase.google.com/docs/firestore/rtdb-vs-firestore)), [motivó a Google a lanzar en Octubre del 2017](https://cloud.google.com/firestore/docs/release-notes) un nuevo producto llamado [Cloud Firestore](https://firebase.google.com/docs/firestore/?hl=es-419).  
**Firestore** es una base de datos orientada a documentos y colecciones. Posee las mismas características de Realtime Database (propagación de cambios en tiempo real a los clientes conectados reglas de seguridad, etc), pero con algunas mejoras que lo transforman en un producto prometedor.  
En esta guía, crearemos paso a paso una pequeña aplicación en Angular (versión 6 lógicamente :smiley:) que lea / escriba información en Firestore.

> **Update Agosto 2018**: Muchas gracias a todos por el feedback del artículo. Se agregó el paso necesario para las reglas de autenticación y se corrigieron algunos problemas en la edición.

> El repositorio de la aplicación abordada en esta guía puden encontrarlo en [https://github.com/nicoavila/tutorial-angular-firestore](https://github.com/nicoavila/tutorial-angular-firestore)

## Antes de empezar: Creación de un proyecto en Firebase

El paso inicial es la creación de un nuevo proyecto en Firebase. Para ello, utilizando una cuenta Google (si no tienes una puedes [crear una aquí](https://accounts.google.com/SignUp?hl=es-419)), debemos dirigirnos a la [Consola de Administración de Firebase](https://console.firebase.google.com/). Debemos hacer click sobre el ícono **Agregar Proyecto**

![Creación de nuevo proyecto en Firebase](http://nicoavila.s3.amazonaws.com/articulos/06_02creacion-nuevo-proyecto.jpg)

Ingresaremos un **nombre** para el nuevo proyecto. Para este tutorial usaremos el nombre **Angular Chile Firestore**. Ustedes deberán utilizar otro nombre que genere un identificador distinto, ya que los ID de proyectos en Google Cloud son únicos.  
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
  public createCat(data: any) {
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
  public updateCat(documentId: string, data: any) {
    return this.firestore.collection('cats').doc(documentId).set(data);
  }

  //Borra un gato
  public deleteCat(documentId: string) {
    return this.firestore.collection('cats').doc(documentId).delete();
  }
}
```

Hemos definido una serie de **métodos públicos** que nos permiten realizar un CRUD sobre la colección **cats** en nuestro proyecto. A continuación revisaremos el detalle de cada uno de los métodos:

* ```constructor()```: Corresponde al constructor de nuestra clase FirestoreService. Los constructores por definición son métodos que se ejecutan cada vez que se crea una instancia de un objeto. Se ha definido el atributo firestore, el cual corresponde a una instancia de AngularFirestore, que expone todos los métodos que podemos utilizar en la librería.

* ```createCat()```: Este método añadirá un nuevo documento en la colección *cats* mediante el uso del método ```add()```.

* ```getCat()```: Este método obtiene un documento en específico de la colección *cats*. Retorna un *Observable*.

* ```getCats()```: Este método obtiene todos los documentos de la colección *cats*. Retorna un *Observable*.

* ```updateCat()```: Este médoto actualiza un documento en específico de la colección *cats*. Retorna un *Observable*.

* ```deleteCat()```: Este método elimina un documento en específico de la colección *cats*. Retorna un *Observable*.

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
  ) {}

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
Los tres elementos importantes en el componente hasta ahora son los siguiente:

* La variable pública ```cats```, que corresponde a un array con todos los gatos que obtendremos desde Firestore.
* La inyección del servicio **FirestoreService** en nuestro componente.
* El llamado de la función ```.getCats()```. Esta función retorna un observable con todos los documentos en la colección *cats*. Se itera sobre cada documento para así obtener su **id** y sus **datos**, los cuales son guardados como un objeto en el array ```cats```.

En el template del componente, el archivo ```cats.component.html``` agregaremos lo siguiente:

```html
<div class="grd">
  <div class="grd-row">
    <div class="grd-row-col-2">
        <h1>🐱 Gatos 🐱</h1>

        <form>
          <label for="nombre">Nombre</label>
          <input type="text" name="nombre">

          <label for="url_img">Imagen</label>
          <input type="url" name="url_img">

          <input type="submit" class="btn--blue btn--add" value="Agregar">
        </form>
    </div>
    <div class="grd-row-col-4">
      <div class="cats_container">
        <div class="media" *ngFor="let cat of cats">
          <div class="media-figure">
            <img [src]="cat.data.url">
          </div>
          <div class="media-body">
            <p>{{cat.data.nombre}}</p>
            <div class="btn--group">
              <button class="btn--green btn--s btn1">Editar</button>
              <button class="btn--red btn--s btn2">Eliminar</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

La parte importante por el momento es esta:

```html
<div class="media" *ngFor="let cat of cats">
  <div class="media-figure">
    <img [src]="cat.data.url">
  </div>
  <div class="media-body">
    <p>{{cat.data.nombre}}</p>
    <div class="btn--group">
      <button class="btn--green btn--s btn1">Editar</button>
      <button class="btn--red btn--s btn2">Eliminar</button>
    </div>
  </div>
</div>
```

La directiva ```*ngFor```iterará por cada elemento del array **cats** para asi crear tantos bloques como documentos en nuestra colección *cats* existan. 

> Si deseas saber más sobre *ngFor te recomendamos leer el artículo [Directivas Estructurales en Angular 6](https://medium.com/angular-chile/directivas-estructurales-en-angular-33529aa9dd31)

El atributo ```[src]``` de la etiqueta ```<img>``` mostrará el valor de *url*, del objeto *cat* que hemos definido en el ```*ngFor```.  
El valor de la etiqueta ```<p>``` mostrará el valor de *nombre*, del objeto *cat* que hemos definido en el ```*ngFor```.

### Paso 3: Reglas
Firestore posee un completo sistemas de reglas que nos permitirán otorgar accesos de **lectura** y **escritura** a nuestros usuarios basado en su estado de autenticación u otra condición. Las reglas se manejan en una estructura similar al JSON.
Para efectos de nuestro tutorial, dejaremos las reglas tal como indica la imagen:

![Reglas Firestore](http://nicoavila.s3.amazonaws.com/articulos/06_11reglas.jpg)

> De esta manera estamos permitiendo que cualquier persona que posea nuestra configuración de Firebase puede leer y escribir en nuestro proyecto. Lo dejaremos de esta manera solo para fines académicos. Sin embargo no se recomienda dejar esta regla en producción. Para leer un poco más respecto a la autenticación pueden visitar la [documentación oficial](https://firebase.google.com/docs/auth/?hl=es-419)

### Paso 4: Creación de una colección y documento
Con lo anterior, seremos capaces de mostrar todos los documentos que se encuentren en nuestra colección *cats* :cat:. Para probar que todo funciona correctamente nos dirigiremos nuevamente a la [Consola de Administración de Firebase](https://console.firebase.google.com/) y crearemos manualmente una nueva **colección** y **documento**

![Consola Administración](http://nicoavila.s3.amazonaws.com/articulos/06_07consola-administracion.jpg)

Haremos click en el botón que dice **Agregar colección**. Se desplegará un nuevo modal en donde debemos agregar la información que nos solicita. En primera instancia debemos crear una nueva colección, la que nombraremos *cats*. Para continuar, haremos click en el botón **Siguiente**

![Nueva colección](http://nicoavila.s3.amazonaws.com/articulos/06_08creacion-coleccion-cats.jpg)

El siguiente paso es la creación de un nuevo documento. Cada documento en Firestore posee un ID único por colección. Dejaremos que Firebase cree un ID para el nuevo documento haciendo click sobre el botón **ID Automático**.
Luego, debemos agregar todos los atributos del nuevo documento. Agregaremos dos campos con la siguiente información:

1. Campo: nombre | Tipo: (String) | Valor: "Hey! I'm a cat!"
2. Campo: url | Tipo: (String) | Valor: "https://pbs.twimg.com/profile_images/602729491916435458/hSu0UjMC_400x400.jpg"

Para finalizar, haremos click en el botón **Guardar**.

![Nuevo documento](http://nicoavila.s3.amazonaws.com/articulos/06_09creacion-nuevo-documento.jpg)

Al agregar el documento y revisar lo que hemos construído hasta el momento

![Nuevo gatito](http://nicoavila.s3.amazonaws.com/articulos/06_10resultado-creacion-documento.jpg)

### Paso 5: Crear nuevos documentos desde Angular
Nuestra aplicación Angular se comunica correctamente con Firestore y es capaz de mostrar los documentos que agreguemos en la colección *cats*. Sin embargo, aun no podemos agregar, editar y eliminar documentos desde la aplicación haciendo uso de los métodos que creamos en el servicio ```firestore.service.ts```.

El primer paso es agregar el **módulo de formularios** a la aplicación. Para ello debemos importar el módulo ```FormsModule``` y ```ReactiveFormsModule``` desde ```@angular/forms``` y agregarlo en el array ```imports``` del decorador ```@NgModule``` en el archivo **app.module.ts**.

```typescript
@NgModule({
  declarations: [...],
  imports: [
    ...
    FormsModule,
    ReactiveFormsModule
    ...
  ],
  providers: [...],
  bootstrap: [...]
})
```

Luego en el archivo ```cats.component.ts``` agregaremos las siguientes líneas sobre el constructor de la clase:

```typescript
public documentId = null;
public currentStatus = 1;
public newCatForm = new FormGroup({
  nombre: new FormControl('', Validators.required),
  url: new FormControl('', Validators.required),
  id: new FormControl('')
});
```

En el constructor de la clase agregaremos lo siguiente:

```typescript
this.newCatForm.setValue({
  id: '',
  nombre: '',
  url: ''
});
```

Al tener solo un formulario como punto de entrada, manejaremos dos estados de la aplicación. ```currentStatus = 0```indicará que la aplicación se encuentra en **modo de creación de documentos**, mientras que ```currentStatus = 1```indicará que la aplicación se encuentra en **modo de edición de documentos**.
El template del componente, el archivo ```cats.component.html``` debemos modificarlo con lo siguiente:

```html
<form (ngSubmit)="newCat(newCatForm.value)" [formGroup]="newCatForm">
  <input type="hidden" formControlName="id">

  <label for="nombre">Nombre</label>
  <input type="text" formControlName="nombre">

  <label for="url_img">Imagen</label>
  <input type="url" formControlName="url">

  <input type="submit" class="btn--blue btn--add" [disabled]="!newCatForm.valid" [value]="(currentStatus == 1) ? 'Agregar' : 'Editar'">
</form>
```

Las partes importantes del template son las siguientes:

* ```(ngSubmit)="newCat(newCatForm.value)"```: Al realizar el submit del formulario, se ejecutará el método ```newCat()```, el cual toma como argumento el *valor actual del formulario*.
* ```[formGroup]="newCatForm"```: Indica que el formulario pertenece al FormGroup *newCatForm*, declarado en la clase de nuestro componente.
* ```formControlName="nombre" y formControlName="url"```: Indica que los inputs *nombre* y *url* pertenecen al formulario *newCatForm*.

Finalmente, en el archivo ```cats.component.ts``` agregaremos el siguiente método público:

```typescript
public newCat(form, documentId = this.documentId) {
  console.log(`Status: ${this.currentStatus}`);
  if (this.currentStatus == 1) {
    let data = {
      nombre: form.nombre,
      url: form.url
    }
    this.firestoreService.createCat(data).then(() => {
      console.log('Documento creado exitósamente!');
      this.newCatForm.setValue({
        nombre: '',
        url: '',
        id: ''
      });
    }, (error) => {
      console.error(error);
    });
  } else {
    let data = {
      nombre: form.nombre,
      url: form.url
    }
    this.firestoreService.updateCat(documentId, data).then(() => {
      this.currentStatus = 1;
      this.newCatForm.setValue({
        nombre: '',
        url: '',
        id: ''
      });
      console.log('Documento editado exitósamente');
    }, (error) => {
      console.log(error);
    });
  }
}
```

En esta sección, la acción del botón submit será distinta en relación al valor de la variable **currentStatus**. Si el valor es **1**, la aplicación estará en modo creación, permitiendo llamar al método ```createCat()``` que posibilita guardar un nuevo documento en la colección *cats*. Por el contrario, si el valor de **currentStatus** es **2**, la aplicación estará en modo de edición, permitiendo llamar al método ```updateCat()``` que posibilita la modificación de un documento en la colección *cats*.

### Paso 6: Edición de documentos
Para editar documentos, modificaremos el archivo ```cats.component.html```, añadiendo lo siguiente a la etiqueta del botón editar:

```html
<button class="btn--green btn--s btn1" (click)="editCat(cat.id)">Editar</button>
```

En el archivo ```cats.component.ts``` añadiremos un nuevo método público llamado **editCat()**

```typescript
public editCat(documentId) {
  let editSubscribe = this.firestoreService.getCat(documentId).subscribe((cat) => {
    this.currentStatus = 2;
    this.documentId = documentId;
    this.newCatForm.setValue({
      id: documentId,
      nombre: cat.payload.data().nombre,
      url: cat.payload.data().url
    });
    editSubscribe.unsubscribe();
  });
}
```

Este método permite consultar por un documento en la colección *cats* basado en la variable **documentId**. Una vez se obtiene el documento, la aplicación pasará a modo de edición (currentStatus = 2), para así cargar el contenido del documento en el formulario.  
Como en el paso anterior hemos agregado una lógica que permite la edición de documentos, al modificar el formulario se guardaran nuevos valores para el documento que se encuentra en modo edición.

### Paso 7: Eliminación de documentos
Para poder eliminar documentos, modificaremos ligeramente el archivo ```cats.component.html``` agregando lo siguiente:

```html
<button class="btn--red btn--s btn2" (click)="deleteCat(cat.id")>Eliminar</button>
```

Luego, en la clase del componente (archivo ```cats.component.ts```) agregaremos el siguiente método público

```typescript
public deleteCat(documentId) {
  this.firestoreService.deleteCat(documentId).then(() => {
    console.log('Documento eliminado!');
  }, (error) => {
    console.error(error);
  });
}
```

Este método llamará a la función ```deleteCat()``` del servicio **FirestoreService**, que permite eliminar un documento de una colección. Como cada acción en la base de datos de Firestore se propaga a todos los clientes conectados, cuando elíminemos un documento presionando el botón **Eliminar**, este automáticamente desaparecerá de la lista.

Espero que esta guía les sirva para iniciarse en **Firestore**. Aun queda mucho por revisar: reglas de acceso, storage, y lo nuevo de Firebase: [ML Kit](https://youtu.be/ejrn_JHksws), que abordaremos en próximas publicaciones. Si tienen alguna duda, pueden contactarme a través de twitter ([@nicoavila_a](https://twitter.com/nicoavila_a)).