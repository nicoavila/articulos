# Aplicaciones multilenguaje en Angular 7 con ngx-translate

![Gusanos parlanchines](https://nicoavila.s3.amazonaws.com/articulos/24_aplicaciones-multilenguaje-ngx-translate.png)

Normalmente cuando escribimos nuestras aplicaciones utilizando Angular lo hacemos con un lenguaje en mente. ¿Pero que ocurre cuando existe el requerimiento de soportar dos o más lenguajes?.  
En este breve tutorial te enseñaremos a utilizar la librería [ngx-translate](https://github.com/ngx-translate/core) para llevarlo a cabo :smiley:

## Qué construiremos
Crearemos una breve aplicación que muestre un texto en múltiples idiomas. Tendremos un componente llamado **TranslationComponent** encargado de realizar el cambio de lenguaje entre *español*, *inglés* y *japonés*

(IMAGEN GIF DEL PRODUCTO FINAL)

## Creando un nuevo proyecto
Para crear un nuevo proyecto en Angular, abre una terminal de tu sistema y ejecuta el siguiente comando:

```ng new tutorial-angular7-multilenguaje --style=css```

Cuando [Angular CLI](https://cli.angular.io) te pregunte **Would you like to add Angular routing?** escribe la letra "N" y presiona Enter. Esto generará un nuevo proyecto en el directorio ```tutorial-angular7-multilenguaje```.

## Internacionalización v/s Localización
Antes que sigamos con la instalación, me gustaría hacer una diferencia entre los conceptos de **internacionalización** y **localización**. Según la [W3C](https://www.w3.org/International/questions/qa-i18n), estas son las definiciones:
* **Localización**: *Corresponde a la adaptación de un producto, una aplicación o el contenido de un documento con el fin de adecuarlos a las necesidades (lingüísticas, culturales u otras) de un mercado destinatario concreto (una "localidad" o "local")*.
* **Internacionalización**: *Corresponde al diseño y desarrollo de un producto, una aplicación o el contenido de un documento de modo tal que permita una fácil localización con destino a audiencias de diferentes culturas, regiones o idiomas*.

## Instalando ngx-translate
[ngx-translate](https://github.com/ngx-translate/core) es una librería de internacionalización para Angular. Para instalarla, debemos ejecutar los siguientes comandos en una terminal de sistema:

```npm install @ngx-translate/core @ngx-translate/http-loader --save```

Esto instalará **ngx-translate/core** y **ngx-translate/http-loader** en nuestro proyecto recientemente creado. ¿Para qué sirven estas dependencias? Te lo contamos!:

* **ngx-translate/core**: Dependencia con las funcionalidades principales para aplicar internacionalización en aplicaciones Angular.
* **ngx-translate/http-loader**: Dependencia encargada de la carga de archivos de traducción desde la sura ```src/assets/i18n/``` utilizando HttpClient.

## Modificando app.module.ts
El siguiente paso es agregar *ngx-translate* en el archivo ```app.module.ts```. Para ello, abriremos el archivo y agregaremos lo siguiente:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

//Translation
import { TranslateLoader, TranslateModule } from '@ngx-translate/core';
import { TranslateHttpLoader } from '@ngx-translate/http-loader';
import { HttpClient, HttpClientModule } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    TranslateModule.forRoot({
      loader: {
        provide: TranslateLoader,
        useFactory: (http: HttpClient) => {
          return new TranslateHttpLoader(http);
        },
        deps: [ HttpClient ]
      }
    })
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Las partes a destacar son las siguientes:

* **Imports de TranslateLoader, TranslateModule y TranslateHttpLoader**: Corresponden a las módulos y providers necesarios para que ngx-translate funcione correctamente.
* **Configuración de TranslateModule**: Se debe utilizar un loader personalizado para este módulo. TranslateHttpLoader cargará archivos de idioma bajo demanda utilizando HttpClient.

## Creando los archivos de idioma
El siguiente paso es la creación de los archivos de idioma correspondiente. Para ello, debemos crear tres archivos:

* ```src/assets/i18n/en.json```
* ```src/assets/i18n/es.json```
* ```src/assets/i18n/jp.json```

La palabra que traduciremos es "**Lista de frutas**". Con la ayuda de [Google Translator](https://translate.google.com) podemos obtener la traducción al inglés y japonés:

1. Lista de frutas
2. List of fruits
3. 果物のリスト

> Como no se Japonés, no estoy seguro si la traducción correcta es esta. Si alguno de los lectores considera que existe otra mejor, hágame saber en un comentario :smiley:

![Google Translator](http://placehold.it/800x300)

Agregaremos las traducciones a los archivos recientemente creados respetando la siguiente estructura:

**src/assets/i18n/es.json**
```json
{
  "principal.texto" : "Lista de frutas"
}
```

**src/assets/i18n/en.json**
```json
{
  "principal.texto" : "List of fruits"
}
```

**src/assets/i18n/jp.json**
```json
{
  "principal.texto" : "果物のリスト"
}
```

Los archivos de traducción JSON aceptan un nombre de un objeto principal seguido de un atributo. Podemos incluso utilizar una variación de sintaxis de la siguiente manera

```json
{
  "principal" : {
    "texto": "果物のリスト"
  }
}
```

## Creando el componente TranslationComponent
Este componente estará a cargo de la traducción de nuestra aplicación. Para crearlo debemos ejecutar el siguiente comando en una terminal de sistema:

```ng generate component translation```

Esto generará una carpeta en el directorio ```src/app``` llamada ```translation```. En el archivo ```translation.component.ts``` agregaremos lo siguiente:

```typescript
import { Component, OnInit } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';

@Component({
  selector: 'app-translation',
  templateUrl: './translation.component.html',
  styleUrls: ['./translation.component.scss']
})
export class TranslationComponent implements OnInit {

  public activeLang = 'es';

  constructor(
    private translate: TranslateService
  ) {
    this.translate.setDefaultLang(this.activeLang);
  }

  ngOnInit() {
  }

  public cambiarLenguaje(lang) {
    this.activeLang = lang;
    this.translate.use(lang);
  }
}
```
Las partes importantes a considerar son las siguientes:

* **Inyección de dependencia de TranslateService:** Se debe inyectar la dependencia ```TranslateService``` desde ```@ngx-translate/core```.
* **Lenguaje por defecto:** En la función constructora de la clase del componente se debe agregar ```this.translate.setDefaultLang(this.activeLang);```. Esto establece un lenguaje por defecto (en nuestro caso Español, o 'es')
* **Función para cambiar lenguaje:** Se debe agregar una función que permita cambiar el lenguaje. Usando la función ```this.translate.use(NOMBRE_LENGUAJE)``` podemos especificar que archivo de traducción se utilizará.

## Utilizando las directivas de traducción
El siguiente paso es la modificación de la vista de nuestro componente. Para ello debemos editar el archivo ```translate.component.html``` y agregar lo siguiente:

```html
<div class="contenedor">
  <h1>{{ 'principal.texto' | translate }}</h1>
  <div class="contenedor-botones">
    <button [ngClass]="{'active': activeLang == 'es'}" (click)="cambiarLenguaje('es')">Español</button>
    <button [ngClass]="{'active': activeLang == 'en'}" (click)="cambiarLenguaje('en')">Inglés</button>
    <button [ngClass]="{'active': activeLang == 'jp'}" (click)="cambiarLenguaje('jp')">Japonés</button>
  </div>
</div>
```

Una de las partes más importantes a considerar es el uso de la directiva de traducción ```{{ 'principal.texto' | translate }}```. El valor de la etiqueta \<h1> va a corresponder al atributo ```principal.texto``` en nuestros archivos de traducción.

Al click de cada botón se llamará a la función ```cambiarLenguaje()``` que configurará el lenguaje activo de la aplicación. Esto también permite añadir una clase condicional utilizando [ngClass](https://angular.io/api/common/NgClass).

> Es importante que nuestros archivos de traducción cuenten con el mismo nombre del atributo, de lo contrario nos podemos encontrar con algunos errores al utilizar la directiva de traducción.

## Toques finales
Finalmente modificaremos el archivo ```translation.component.scss```y agregaremos lo siguiente

```scss
.contenedor {
  font-family: 'Roboto', sans-serif;

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;

  h1 {
    text-align: center;
    font-size: 68px;
  }

  .contenedor-botones {
    display: flex;
    width: 500px;
    button {
      outline: none;
      margin: 10px;
      width: 100%;
      padding: 20px 10px;
      border: 0px;
      background: rgb(255, 238, 109);
      border-radius: 30px;
      font-size: 14px;
      &.active {
        background: rgb(250, 102, 23);
      }
    }
  }
}
```
> Para esta hoja de estilos estamos utilizando la fuente [Roboto](https://fonts.google.com/specimen/Roboto). Es necesario agregar la hoja de estilos ```<link href="https://fonts.googleapis.com/css?family=Roboto" rel="stylesheet">``` en el archivo ```index.html```.

Finalmente, en el archivo ```styles.scss``` se debe agregar lo siguiente:

```scss
body, html {
  margin: 0px;
  padding: 0px;
  width: 100%;
  min-height: 100%;
}
```

Espero que hayas disfrutado esta publicación tanto como disfruté yo redactándola :smiley:. Recuerden que si tienen alguna duda pueden contactarme a través de mi Twitter [@nicoavila_a](https://twitter.com/nicoavila_a).

Si te gustó la publicación, compártela y deja algunos claps :claps:


