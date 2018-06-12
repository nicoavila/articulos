# Web Components con Angular Elements
### Mayo 2018

![Imagen del Artículo](http://nicoavila.s3.amazonaws.com/articulos/08_01webcomponents-angular-elements.jpg)

El término **Web Components** ronda hace un par de año en blogs, conferencias y mundo del desarrollo web en general. Hace referencia a una serie de APIs Web que nos permiten crear etiquetas HTML personalizables, encapsuladas y reutilizables. Pueden ser utilizados en cualquier página y aplicación web sin la necesidad de importar librerías externas de JavaScript :hooray:. Para poder utilizar Web Components, nuestro navegador debe soportar una serie de APIs Web, algunas de las cuales aun se encuentran en desarrollo:

* **Custom Elements**: Permite la creación de nuevos tipos de elementos en el DOM ([ver especificación](https://w3c.github.io/webcomponents/spec/custom/)).
* **Shadow DOM**: Permite definir como se encapsulan los estilos y el lenguaje ([ver especificación](https://w3c.github.io/webcomponents/spec/shadow/)).
* **HTML Imports**: Define la inclusión de HTML en otros documentos HTML ([ver especificación](https://w3c.github.io/webcomponents/spec/imports/)).
* **HTML Template**: Define como se declaran fragmentos de HTML cuando la página carga, pero que pueden ser utilizados después durante la ejecución ([ver especificación](https://html.spec.whatwg.org/multipage/scripting.html#the-template-element/)).

Desde la [versión 6](https://html.spec.whatwg.org/multipage/scripting.html#the-template-element/) de Angular CLI es posible crear web components nativos con Angular :eyes:. En este breve tutorial construiremos un pequeño componente web que permita mostrar una imagen aleatorea de un perro cada vez que se llama.

![ng-chile-dog-of-the-day](http://nicoavila.s3.amazonaws.com/articulos/08_02a-construir.jpg)

> El proyecto creado en esta publicación pueden descargarlo desde el siguiente repositorio.

## Generando una nueva aplicación
El paso inicial es crear una nueva aplicación. Para ello abriremos un terminal de consola y ejecutaremos el siguiente comando:

```bash
ng new dog-of-the-day --prefix ng-chile
```
Esto creará un nueva nueva estructura de proyecto Angular e instalará todas las dependencias necesarias. Hemos agregado el flag ```--prefix ng-chile``` para utilizar el prefijo *ng-chile* en todos los selectores de la aplicación.

## Agregando lo necesario para trabajar con Web Components
El siguiente paso es la dependencia de **Angular Elements**. Esto lo logramos gracias al comando ```ng add```, que nos permite invocar al administrador de paquetes (en mi caso npm) para instalar las dependencias y ejecutar scripts de instalación necesarios para la librería (implementado como un schematic). *Angular Elements* instalará el polyfill ```document-register-element.js```.

```bash
ng add @angular/elements
```

## Creando un nuevo componente
Crearemos un nuevo componente llamado **dog-of-the-day**. Este componente encapsulará toda la lógica, HTML y estilos CSS de nuestro Web Component. Ejecutaremos el siguiente comando en el terminal:

```bash
ng generate component dog-of-the-day --inline-style --inline-template --view-encapsulation Native
```

El flag ```--view-encapsulation``` nos permite especificar la forma de encapsulación de las vistas del componente. Angular por defecto no utiliza *Shadow DOM* para encapsular sus componentes, para así facilitar la compatibilidad entre múltiples navegadores que aun no soportan esta característica en forma nativa, pero podemos especificarlo. Los valores posibles son:

* **None**: (ViewEncapsulation.None) - No utiliza Shadow DOM.
* **Emulated**: (ViewEncapsulation.Emulated) - No utiliza Shadow DOM, pero emula su funcionamiento.
* **Native**: (ViewEncapsulation.Native) - Utiliza Shadow DOM con soporte Nativo del navegador.

Los flags ```--inline-style``` e ```--inline-template``` nos permiten especificar que el componente posee estilos y HTML declarados en forma inline (dentro del decorador *@Component()*, en los atributos **styleUrls** y **template** respectivamente).

Para traer una imagen aleatórea de un perro, usaremos la API pública [Dog API](https://dog.ceo/dog-api/) de [Dog CEO](https://twitter.com/dog__CEO). 

![Dog.ceo](http://nicoavila.s3.amazonaws.com/articulos/08_03dog-api.jpg)

Cada vez que se realiza una petición GET al endpoint ```https://dog.ceo/api/breeds/image/random```, obtenemos un mensaje similar a este

```json
{
  "status": "success",
  "message": "https://images.dog.ceo/breeds/deerhound-scottish/n02092002_3400.jpg"
}
```

Nuestro componente debe poder realizar una llamada GET a la API. Para ello utilizaremos [HttpClient](https://angular.io/guide/http). La definición oficial de Angular de HttpClient es:

> The HttpClient in @angular/common/http offers a simplified client HTTP API for Angular applications that rests on the XMLHttpRequest interface exposed by browsers

Nuestro componente debe tener lo siguiente:

```typescript
import { Component, OnInit, ViewEncapsulation } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { environment } from '../../environments/environment';

@Component({
  selector: 'ng-chile-dog-of-the-day',
  template: `
    <link href="https://fonts.googleapis.com/css?family=Nanum+Pen+Script" rel="stylesheet">
    <div class="component_container">
      <h1>{{message}}</h1>
      <img src="{{imagen}}" *ngIf="imagen"/>
    </div>
  `,
  styles: [`
    .component_container {
      font-family: 'Nanum Pen Script', cursive;
      width: 580px;
      height: auto;
      border: 1px solid #CECECE;
      padding: 20px;
      text-align: center;
      background: #F2F2F2;
    }
    .component_container img {
      width: 100%;
      height: auto;
    }
  `],
  encapsulation: ViewEncapsulation.Native
})
export class DogOfTheDayComponent implements OnInit {

  public imagen;
  public message = 'Dog of The Day';

  constructor(
    public http: HttpClient
  ) { }

  ngOnInit() {
    this.http.get(environment.api_url).subscribe((dotd: any) => {
      this.imagen = dotd.message;
    });
  }
}
```

Los puntos importantes a considerar en el componente son:

* Realizar el import de **HttpClient** desde ```@angular/common/http```.
* Inyectar la dependencia de **HttpClient** en el constructor del Componente.
* Detectar que el decorador del componente posee un atributo llamado **encapsulation** donde su valor corresponde a ```ViewEncapsulation.Native```. Esto permite declarar que el componente utilizará Shadow DOM en forma nativa.
* Importar ```environment``` para utilizar variables de ambiente. En el archivo ```environment.ts``` y ```environment.prod.ts``` agregaremos un nuevo atributo al objeto llamado **api_url** con el valor **https://dog.ceo/api/breeds/image/random**

El siguiente paso es **eliminar** los siguientes archivos:

* ```app.component.ts```
* ```app.component.spec.ts```
* ```app.component.html```

Debemos dejar solo el archivo ```app.module.ts```, el cual modificaremos en el siguiente paso.

## Modificando app.module.ts
Este es el paso crucial en la creación de un componente web utilizando Angular. Debemos modificar este archivo de la siguiente manera:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, Injector } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';
import { createCustomElement } from '@angular/elements';
import { DogOfTheDayComponent } from './dog-of-the-day/dog-of-the-day.component';

@NgModule({
  declarations: [DogOfTheDayComponent],
  imports: [BrowserModule, HttpClientModule],
  entryComponents: [DogOfTheDayComponent]
})
export class AppModule {
  constructor(
    private injector: Injector
  ) {
    const imagenEspacio = createCustomElement(DogOfTheDayComponent, { injector });
    customElements.define('ng-chile-dotd', imagenEspacio);
  }
  ngDoBootstrap() {}
}
```

Los puntos importantes son los siguiente:

* Importar **Injector** para utilizarlo dentro del constructor.
* Importar **HttpClientModule**, ya que nuestro componente web realizará una llamada utilizando **HttpClient**.
* Importar **createCustomElement**. Nos permite crear una clase que permite utilizar la funcion ```createCustomElement.define()```, la cual expone un componente web para poder ser utilizado más tarde.
* Agregar ```DogOfTheDayComponent``` al array de ```entryComponents```, ya que este componente no es parte de un componente padre y es un componente que debe ser parte del bootstraping de la aplicación.
* En la función ```createCustomElement()``` debemos agregar el import de **DofOfTheDayComponent**
* Definiremos que la etiqueta HTML personalizada para el componente web será **ng-chile-dotd**.

## Building de la aplicación
Para realizar el build de la aplicación ejecutaremos el siguiente comando en la terminal:

```bash
ng build --prod --output-hashing=none && cat dist/dog-of-the-day/{runtime,polyfills,scripts,main}.js | gzip > elements.js.gz
```

En este paso, lo importante a considerar es lo siguiente:

* Utilizaremos el flag ```output-hashing=none``` para no generar hashing en nuestros archivos
* El resultado del comando ```cat``` para todos los archivos se pasa al comando ```gzip```que generará el archivo **elements.js.gz** para ser utilizado como standalone. Finalmente tenemos un web component que podemos utilizar en una aplicación web. A continuación escribiremos un pequeño archivo HTML que haga uso de nuestro resultado :smiley:

## Utilización del web
Crearemos un archivo ```index.html``` el cual tendrá el siguiente contenido:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Dog of The Day - Angular Chile</title>
  </head>

  <body>
    <ng-chile-dotd></ng-chile-dotd>
    <script src="elements.js"></script>
  </body>
</html>
```

Estamos utilizando directamente la etiqueta ```<ng-chile-dotd></ng-chile-dotd>``` en nuestro nuevo archivo HTML. Debemos agregar el archivo ```elements.js.gz``` bajo la declaración de la etiqueta *<ng-chile-dotd>* para que funcione correctamente. Recuerden probar el archivo sobre un servidor que soporte GZIP, de lo contrario no funcionará correctamente.