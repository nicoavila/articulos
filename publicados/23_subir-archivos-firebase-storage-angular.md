# Subir archivos a Firebase Cloud Storage con Angular 7

![Subir Archivos](http://nicoavila.s3.amazonaws.com/articulos/23_01subir-archivos-firebase-storage-angular.png)

Angular provee dos formas de manejar el input generado por el usuario: con [Reactive Forms](https://angular.io/guide/reactive-forms) o [Template-driven Forms](https://angular.io/guide/forms). El manejo de la información es relativamente sencillo para ambos casos.  
Los problemas aparecen cuando necesitamos manejar información en formularios distinto a texto: archivos.  
En este tutorial veremos como implementar un formulario que contenga un elemento ```<input type="file">``` para subir su contenido a Firebase Cloud Storage utilizando [Angular 7](https://angular.io).

## ¿Qué es Firebase?
Firebase es un ecosistema de servicios para desarrolladores de Google. Desde su adquisición el año 2012, Google se ha centrado en proveer una plataforma robusta para suplir la necesidad de casos de uso comunes de nuestros desarrollos.

> ¿Sabias qué en Angular Chile tenemos un artículo que te enseña a utilizar Cloud Firestore? Haz click en el [siguiente vínculo](https://medium.com/angular-chile/angular-6-y-firestore-b7f270adcc96) para saber más.

## Firebase Cloud Storage
En las propias palabras de Google:
> Cloud Storage para Firebase es un servicio de almacenamiento de objetos potente, simple y rentable construido para la escala de Google

Para utilizar Cloud Firestore en Angular es necesario que tengas una cuenta Google. Si aun no posees una cuenta, puedes [crear una aquí](https://accounts.google.com/signup/v2/webcreateaccount?hl=es-419&flowName=GlifWebSignIn&flowEntry=SignUp)

## Creando un proyecto en Firebase
Con tu cuenta Google ingresa a la [Consola de Administración de Firebase](https://console.firebase.google.com) y crea un nuevo proyecto, haciendo click sobre el contenedor "**Agregar proyecto**"

![Agregar proyecto](https://nicoavila.s3.amazonaws.com/articulos/23_02agregar-proyecto.png)

Al hacer click, se desplegara un modal que solicita el nombre del proyecto. Ingresen un nombre descriptivo para su nuevo proyecto de Firebase

![Nuevo proyecto Firebase](https://nicoavila.s3.amazonaws.com/articulos/23_03nuevo-proyecto.png)

Al crear el proyecto debemos hacer click sobre el menu "**Storage**". Presionamos el botón "Comenzar" para así habilitar el Firebase Storage en nuestro proyecto

![Menu](https://nicoavila.s3.amazonaws.com/articulos/23_04storage.png)

Al habilitar el Storage, veremos una ventana similar a esta:

![Detalle Storage](https://nicoavila.s3.amazonaws.com/articulos/23_05detalle-storage.png)

* **Subir archivo**: Permite subir un archivo en forma manual.
* **Ícono carpeta**: Crea una nueva carpeta en el Storage.
* **Lista de archivos**: Lista los archivos presentes en el Storage.

## Reglas en Cloud Storage
Una de las grandes ventajas de utilizar Firebase es la capacidad de manejar en detalle el acceso a cada uno de sus recursos.
Cloud Storage, al igual que [Realtime Database](https://firebase.google.com/docs/database/?hl=es-419) o [Cloud Firestore](https://firebase.google.com/docs/firestore/?hl=es-419), permite el uso de [reglas](https://firebase.google.com/docs/storage/security/start?hl=es-419), las cuales indican el acceso a servicios o partes de él solo a usuarios autenticados.
Para efectos de este tutorial utilizaremos *reglas permisivas*. Permiten que **cualquier persona que posea acceso a nuestros datos de conexión pueda escribir archivos en Cloud Storage**. Esto, si bien es cierto __es inseguro y no está recomendado para su uso en sistemas de ambiente de producción__, nos permitirá entender la interacción de Angular y Cloud Firestore para este tutorial.

En la sección de Cloud Storage, en la consola de administración de Firebase, debemos dirigirnos a la pestaña **"Reglas"** y dejar el contenido de la siguiente manera:

![Reglase Cloud Firestore](https://nicoavila.s3.amazonaws.com/articulos/23_05reglas-storage.png)

Para finalizar este proceso debemos hacer click sobre el botón **"Publicar".**

> La autenticación en Firebase es un tema que escapa a los contenidos abordados en este tutorial. ¡Pero tranquilos! Estamos preparando un tutorial dedicado exclusivamente a abordar la autenticación en Firebase :smiley:

## Creando un nuevo proyecto de Angular
La creación de un nuevo proyecto en Angular es sencillo gracias a [Angular CLI](https://cli.angular.io). Si no tienes instalado Angular CLI en tu computador ejecuta el siguiente comando en un terminal de tu sistema:

```npm install -g @angular/cli```

> Debes tener instalado previamente Node en tu computador. Para instalarlo, sigue las instrucciones presentes en la siguiente URL: [https://nodejs.org](https://nodejs.org)

Luego, para generar un nuevo proyecto ejecutaremos el siguiente comando:

```ng new tutorial-angular7-cloud-storage --style=scss```

Cuando angular CLI te pregunte *"Would you like to add Angular routing?"*, escribe "N" y presiona la tecla Enter. Esto generará un nuevo proyecto e instalará las dependencias necesarias para que todo funcione correctamente. Debes ejecutar el comando ```cd tutorial-angular7-cloud-storage``` para navegar a la carpeta del proyecto.

## Instalando y configurando AngularFire 2
[AngularFire2](https://github.com/angular/angularfire2) es la librería oficial de Firebase para Angular. Para instalarla abre un terminal de sistema, navega hasta el proyecto recientemente creado y ejecuta el siguiente comando.

```npm install firebase @angular/fire --save```

> Puedes acceder a la documentación completa de AngularFire 2 en el [siguiente vínculo](https://github.com/angular/angularfire2/tree/master/docs)

Para poder configurar los módulos, es necesario contar con los datos de conexión a nuestro proyecto de Firebase. Para ello, debemos dirigirnos a la consola de Firebase en la siguiente URL: https://console.firebase.google.com. Abrimos nuestro proyecto y hacemos click sobre el botón "+ Agregar App". Se debe seleccionar el ícono web ```(</>)```

![Información Firebase](https://nicoavila.s3.amazonaws.com/articulos/23_06informacion-firebase.gif)

Al hacer click sobre el ícono, se desplegará un modal con la información que debemos utilizar en nuestra aplicación Angular. Debemos seleccionar y copiar en el portapapeles la información marcada con verde (objeto *config*).

![Información de proyecto Firebase](https://nicoavila.s3.amazonaws.com/articulos/23_07info-agregar-firebase.png)

Editaremos el archivo ```environment.ts``` y agregaremos la información que copiamos desde **Firebase Console**, como muestra la imagen a continuación:

![Información en environment.ts](https://nicoavila.s3.amazonaws.com/articulos/23_08environment-firebase.png)

Para finalizar la configuración, debemos indicarle a nuestra aplicación que módulos debe utilizar.
Abriremos el archivo ```app.module.ts``` y agregaremos la siguiente información:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AngularFireModule } from '@angular/fire';
import { AngularFireStorageModule } from '@angular/fire/storage';
import { environment } from '../environments/environment';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AngularFireModule.initializeApp(environment.firebase),
    AngularFireStorageModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Las partes a considerar son las siguientes:

* Se importa ```AngularFireModule``` y ```AngularFireStorageModule```. Estos módulos son fundamentales para que nuestra aplicación Angular pueda comunicarse con Clout Storage de Firebase.
* Se importa ```environment.ts```. En este archivo guardamos los datos de conexión de nuestro proyecto Firebase.
* Se agregan al array de ```imports``` los módulos ```AngularFireModule``` y ```AngularFireStorageModule```. Es importante llamar al método *initializeApp* para inicializar la aplicación de Firebase. Este método acepta como argumento un *objeto de configuración de Firebase*, el cual obtuvimos en pasos anteriores.

## Creando una estructura de Formulario para subir archivos
Para poder subir los archivos a **Cloud Storage**, es necesario crear la estructura HTML necesaria.
Para simplificar el proceso, utilizaremos el framework CSS [Bulma](https://bulma.io).

> ¿Sabias qué en **Angular Chile** tenemos un artículo que te enseña a integrar frameworks CSS en Angular? Haz click en el [siguiente vínculo](https://medium.com/angular-chile/integrar-frameworks-css-en-angular-d2cc98a8284f) para saber más.

Crearemos una estructura similar a la imágen a continuación en el archivo ```app.component.html```

![Estructura del formulario Angular](https://nicoavila.s3.amazonaws.com/articulos/23_09estructura-formulario.png)

Debemos agregar el siguiente markup:

```html
<div class="contenedor">
  <div class="contenedor_formulario">
    <h1 class="is-size-1">Subir archivos a Cloud Storage</h1>
    <p class="download">URL de descarga: <a href="#" target="_blank">Descargar</a></p>
    <form>
      <div class="file has-name is-boxed">
        <label class="file-label">
          <input class="file-input" type="file" name="resume">
          <span class="file-cta">
            <span class="file-label">
              Elegir un archivo…
            </span>
          </span>
          <span class="file-name">
            No hay un archivo seleccionado
          </span>
        </label>
      </div>
      <hr>
      <progress class="progress is-large is-success" value="0" max="100">0%</progress>
      <button class="button is-success is-large">Subir archivo</button>
    </form>
  </div>
</div>
```
En el archivo ```app.component.scss``` debemos agregar lo siguiente:

```scss
.contenedor {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;

  .contenedor-formulario {
    width: 400px;
  }

  .download {
    background: #1081ff;
    color: #FFFFFF;
    padding: 10px 20px;
    margin-bottom: 20px;
    a {
      text-decoration: underline;
      color: #FFFFFF;
    }
  }
}
```
Esto centrará el contenido al centro de la página utilizando [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/).  
Si lo notan, aun no hemos agregado ninguna directiva de Angular para manejar los formularios, pero eso cambiará en breve :smiley:.

## Formularios reactivos en Angular
Los formularios reactivos en Angular permiten manejar el estado de un formulario en un momento en particular. Cada vez que cambia el valor de un control dentro de un formulario, el estado completo del formulario cambia completamente.  
Dada esta definición podemos identificar los siguientes elementos principales:

* **FormControl:** Elemento capaz de seguir el valor y estado de validación de un control de formulario individual. Puedes revisar su documentación en el [siguiente vínculo](https://angular.io/api/forms/FormGroup)
* **FormGroup:** Elemento capaz de seguir el valor y estado de validación de múltiples controles de formularios individuales. Puedes revisar su documentación en el [siguiente vínculo](https://angular.io/api/forms/FormControl).

Para poder utilizar formularios reactivos en Angular es necesario importar algunos módulos. En el archivo ```app.module.ts``` debemos agregar lo siguiente:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

//Agrega estos módulos
import { AngularFireModule } from '@angular/fire';
import { AngularFireStorageModule } from '@angular/fire/storage';
import { environment } from '../environments/environment';
//Formularios
import { ReactiveFormsModule } from '@angular/forms';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AngularFireModule.initializeApp(environment.firebase),
    AngularFireStorageModule,
    ReactiveFormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
La parte más importante a considerar es la siguiente

* Se importa ```ReactiveFormsModule```. Este módulo permite utilizar [Formularios Reactivos](https://angular.io/guide/reactive-forms) en nuestra aplicación de Angular. Luego el módulo se agrega al array de ```imports``` en el decorador ```@NgModule```.

El siguiente paso es la modificación del archivo ```app.component.ts``` agregando lo siguiente:

```typescript
import { Component } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {

  public archivoForm = new FormGroup({
    archivo: new FormControl(null, Validators.required),
  });
  
  public mensajeArchivo = 'No hay un archivo seleccionado';
  public datosFormulario = new FormData();
  public nombreArchivo = '';
  public URLPublica = '';
  public porcentaje = 0;
  public finalizado = false;

}
```
Las partes más importantes a considerar son las siguientes:

* Se realiza el import de ```FormGroup```, ```FormControl``` y ```Validators```. Esto nos permitirá utilizar lo necesario para funcionar con formularios reactivos.
* Se crea un nuevo objeto ```FormGroup``` el cual se almacena en la variable ```archivoForm```. El objeto **FormGroup** permitirá agrupar múltiples **FormControl**.
* Dentro del constructor del objeto ```FormGroup``` se pasa un objeto como argumento, el cual posee todos los ```FormControl``` pertenecientes a ese FormGroup. En este caso se crea un FormControl llamado **archivo**, el cual posee un valor inicial de ```null```, utilizando una regla de validación de tipo **required** (Validators.required).
* Se crea una variable de tipo *string* pública llamada ```mensajeArchivo``` la cual almacenará el mensaje de estado para el archivo que se desea cargar.
* Se crea una variable pública llamada ```datosFormulario``` que almacena una instancia de [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData).
* Se crea una variable pública llamada ```nombreArchivo```. Como su nombre lo indica, esta variable nos permitirá almacenar el nombre del archivo que deseamos subir a Cloud Firestore.
* Se crea una variable pública llamada ```URLPublica```. Esta variable almacenará la URL al archivo en Cloud Firestore.
* Se crea una variable pública llamada ```porcentaje```. Esta variable almacenará un número entero que permite saber cual es el porcentaje de subida del archivo a Cloud Storage.
* Se crea una variable pública booleana llamada ```finalizado``` que permitirá determinar cuando un archivo ha terminado de subirse a Cloud Firestore.

Finalmente debemos modificar el archivo ```app.component.html``` agregando las directivas necesarias para trabajar con formularios. El archivo debe quedar de la siguiente manera:

```html
<div class="contenedor">
  <div class="contenedor_formulario">
    <h1 class="is-size-1">Subir archivos a Cloud Storage</h1>
    <p class="download" *ngIf="finalizado">URL de descarga: <a href="{{ URLPublica }}" target="_blank">Descargar</a></p>
    <form [formGroup]="archivoForm" (ngSubmit)="subirArchivo()">
      <div class="file has-name is-boxed">
        <label class="file-label">
          <input class="file-input" type="file" formControlName="archivo" (change)="cambioArchivo($event)">
          <span class="file-cta">
            <span class="file-label">
              Elegir un archivo…
            </span>
          </span>
          <span class="file-name">
            {{ mensajeArchivo }}
          </span>
        </label>
      </div>
      <hr>
      <progress *ngIf="porcentaje > 0 && porcentaje < 100" class="progress is-large is-success" value="{{porcentaje}}" max="100">{{porcentaje}}%</progress>
      <button [ngClass]="{'button': true, 'is-success': true, 'is-large': true, 'is-loading': porcentaje > 0 && porcentaje < 100}" [disabled]="!archivoForm.valid && (porcentaje > 0 && porcentaje < 100)">Subir archivo</button>
    </form>
  </div>
</div>
```
Las partes importantes a considerar son las siguientes:

* Se crea un párrafo con un vínculo que permitirá mostrar la URL de descarga pública del archivo cuando termine la carga a Cloud Storage.
* Se utiliza la directiva **\[formGroup\]** la cual indica que la etiqueta ```<form>``` pertenece a un **FormGroup** llamado **archivoForm**
* El atributo ```formControlName``` indica que el control de tipo archivo se llama **archivo**, el cual está declarado dentro del FormGroup **archivoForm**.
* Se agrega el evento ```(change)``` el cual invoca la función ```cambioArchivo()``` la que se agregará a la clase del componente.
* Se imprime el contenido de la variable ```mensajeArchivo``` declarado en el archivo ```app.component.ts```.
* Se mostrará la barra de progreso utilizando la directiva ```*ngIf```. Si el porcentaje de carga del archivo es mayor a 0 y menor a 100. En el atributo **value** se mostrará el valor de la variable ```porcentaje```.
* Se utiliza la directiva **\[ngClass\]**, la que nos permite controlar la presencia de clases en forma dinámica. La clase **is-loading** estará presente cuando el valor de la variable porcentaje sea mayor a 0 y menor a 100.
* Se utiliza la directiva **\[disabled\]**. Esta directiva deshabilita el botón **Subir Archivo** cuando el formulario es inválido (es decir, no posee un archivo seleccionado).

## Creando un servicio de Firebase Cloud Storage
Dejaremos de lado brevemente la modificación de nuestro componente principal para enfocarnos en la creación de un servicio que nos permita subir archivos a **Cloud Storage**.
Debemos dirigirnos a nuestro terminal de sistema y en la carpeta del proyecto de Angular ejecutar el siguiente comando:

```bash
ng generate service firebase-storage
```

Se crearán dos nuevos archivos: ```firebase-storage.service.ts``` y ```firebase-storage.service.spec.ts```. El archivo **firebase-storage.service.ts** debe ser modificado con el siguiente contenido:

```typescript
import { Injectable } from '@angular/core';
import { AngularFireStorage } from '@angular/fire/storage';

@Injectable({
  providedIn: 'root'
})
export class FirebaseStorageService {

  constructor(
    private storage: AngularFireStorage
  ) { }

  //Tarea para subir archivo
  public tareaCloudStorage(nombreArchivo: string, datos: any) {
    return this.storage.upload(nombreArchivo, datos);
  }

  //Referencia del archivo
  public referenciaCloudStorage(nombreArchivo: string) {
    return this.storage.ref(nombreArchivo);
  }
}
```

Las partes importantes a considerar son las siguientes:

* Se realiza el import de ```AngularFireStorage```. Esto permitirá inyectar la dependencia en el componente principal.
* Se inyecta la dependencia en el constructor de ```AngularFireStorage``` para utilizarla en el servicio con la variable privada ```storage```.
* Se crea un método llamado ```tareaCloudStorage()```. Este método acepta dos argumentos: el **nombre del archivo** y los **datos del formulario**. Corresponde a la tarea de subida del archivo, que permitirá revisar el porcentaje de carga del archivo.
* Se crea un método llamado ```referenciaCloudStorage()```. Este método acepta un argumento: el **nombre del archivo**. Corresponde a la referencia del archivo, que nos permitirá obtener la URL de descarga cuando el proceso de carga del archivo finalice.

## ¿Cómo funciona Cloud Storage?
A grandes rasgos para poder trabajar con Cloud Storage debemos crear una **referencia**. Las referencias en Firebase Cloud Storage permiten trabajar con jerarquía de archivos del mismo modo como trabajaríamos en nuestro disco duro. La creación de una referencia permitirá subir un archivo, modificar sus metadatos o eliminarlo.
El siguiente esquema muestra los pasos necesarios para poder trabajar con Cloud Storage:

![Esquema pasos](https://nicoavila.s3.amazonaws.com/articulos/23_11esquema-pasos.png)

## Evento al subir un archivo
Debemos crear un evento que nos permita manejar el cambio del mensaje de estado del archivo y que nos permite preparar todo para poder enviar el contenido a Cloud Storage.  
Para ello debemos modificar el archivo ```app.component.ts``` y agregar lo siguiente:

```typescript
import { Component } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { FirebaseStorageService } from './firebase-storage.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {

  constructor (
    private firebaseStorage: FirebaseStorageService
  ) {}

  public archivoForm = new FormGroup({
    archivo: new FormControl(null, Validators.required),
  });
  public mensajeArchivo = 'No hay un archivo seleccionado';
  public datosFormulario = new FormData();
  public nombreArchivo = '';
  public URLPublica = '';
  public porcentaje = 0;
  public finalizado = false;

  //Evento que se gatilla cuando el input de tipo archivo cambia
  public cambioArchivo(event) {
    if (event.target.files.length > 0) {
      for (let i = 0; i < event.target.files.length; i++) {
        this.mensajeArchivo = `Archivo preparado: ${event.target.files[i].name}`;
        this.nombreArchivo = event.target.files[i].name;
        this.datosFormulario.delete('archivo');
        this.datosFormulario.append('archivo', event.target.files[i], event.target.files[i].name)
      }
    } else {
      this.mensajeArchivo = 'No hay un archivo seleccionado';
    }
  }

  //Sube el archivo a Cloud Storage
  public subirArchivo() {
    let archivo = this.datosFormulario.get('archivo');
    let referencia = this.firebaseStorage.referenciaCloudStorage(this.nombreArchivo);
    let tarea = this.firebaseStorage.tareaCloudStorage(this.nombreArchivo, archivo);

    //Cambia el porcentaje
    tarea.percentageChanges().subscribe((porcentaje) => {
      this.porcentaje = Math.round(porcentaje);
      if (this.porcentaje == 100) {
        this.finalizado = true;
      }
    });

    referencia.getDownloadURL().subscribe((URL) => {
      this.URLPublica = URL;
    });
  }
}
```

Las partes más importantes a considerar son las siguientes:

* Se agrega un método llamado ```cambioArchivo()``` el cual se ejecuta cada vez que el ```<input type="file">``` cambia. Este método procesa los archivos presentes en el input, agregando información a la variable pública **FormData**.
* Se agrega un método llamado ```subirArchivo()```. Este método se llamará cuando el formulario sea válido. Obtendrá la información presente en la variable **FormData** y luego utilizará los métodos presentes en el servicio ```firebase-storage.service.ts```. Con estos métodos se obtendrá el porcentaje de carga del archivo y la URL pública una vez termine la carga a Cloud Storage.

Si todo sale bien nuestra aplicación ya será capaz de subir archivos a Cloud Storage como muestra la imagen a continuación:

![Aplicacion funcionando](https://nicoavila.s3.amazonaws.com/articulos/23_12aplicacion-funcionando.gif)

## Conclusiones
Sin duda utilizar Cloud Storage nos facilita la tarea de almacenar archivos en un repositorio remoto. Una de las grandes ventajas es la facilidad de uso de su SDK y su escalabilidad.
Con AngularFire2 podemos integrar cualquier servicio del ecosistema de Firebase en nuestros proyectos de Angular. Es un proyecto muy maduro y [ampliamente documentado](https://github.com/angular/angularfire2/tree/master/docs).
Recuerden que utilizar reglas inseguras no es recomendado en ambientes productivos. Esto solo debe utilizarse para efectos de desarrollo / laboratorio, como lo ha demostrado este tutorial.  
Trabajaremos en un post que explique en profundidad los mecanismos de autenticación en Firebase.
