# Ambientes Personalizados en Angular 6

![Ambientes personalizados Angular 6](http://nicoavila.s3.amazonaws.com/articulos/18_01ambientes-personalizados-angular.jpg)

Al crear una aplicación en Angular contamos con dos archivos ```environment``` en la ruta ```src/environments```. En estos archivos podemos definir variables que serán utilizadas para almacenar valores críticos para el funcionamiento de nuestra aplicación. Son archivos de gran utilidad para agregar:

* API Keys
* URL a servicios
* Parámetros de configuración global
* Valores constantes

De esta manera no declaramos los mismos valores que serán utilizados una y otra vez en nuestros componentes o servicios :smiley:.  
Al ejecutar en consola ```ng serve``` o ```ng build```, se utilizarán los valores de los atributos en el archivo **environment.ts**, mientras que si se agrega el flag ```--prod```: ```ng serve --prod```o ```ng build prod```, se utilizarán los valores de los atributos en el archivo **environment.prod.ts**. Esto es de gran utilidad cuando queremos separar valores distintos para nuestros ambientes de desarrollo y producción.

Sin embargo, cada institución posee pipelines y políticas de desarrollo que pueden variar, y dos ambientes en la gran mayoría de los casos es insuficiente :(. ¿Qué podemos hacer para remediarlo?. En este breve tutorial explicaré los pasos necesarios para la creación de un nuevo ambiente en una aplicación Angular 6 llamado **certificación**.

> El código de este tutorial pueden descargarlo desde el siguiente [repositorio](https://github.com/nicoavila/tutorial-ambientes-personalizados-angular)

## Creando lo necesario
Para efectos prácticos, procederemos a crear un nuevo proyecto para configurar así un nuevo ambiente. Ejecutaremos el siguiente comando en un terminal:

```bash
ng new tutorial-ambientes-personalizados-angular
```
Se generará un nuevo proyecto en donde procederemos a la creación del archivo **environment.cert.ts** en la ruta ```src/environments```

![Ruta environment](http://nicoavila.s3.amazonaws.com/articulos/18_02crear-nuevo-archivo.jpg)

Una vez realizado este paso, editaremos el archivo ```app.component.ts``` con lo siguiente

```typescript
import { Component } from '@angular/core';
import { environment } from '../environments/environment';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = environment.message;
}
```

En el archivo ```app.component.html``` agregaremos lo siguiente:

```html
<div class="container">
  <h1>{{title}}</h1>
</div>
```

Finalmente en el archivo ```app.component.css``` agregaremos lo siguiente:

```css
.container {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
```
## Modificando el archivo Angular CLI Workspace

Una vez tenemos todo lo necesario para recibir a nuestro nuevo ambiente, es necesario declararlo en el archivo ```angular.json```. Agregaremos el siguiente objeto en:

**projects -> NOMBRE PROYECTO -> architect -> build**

> Para entender la estructura de este archivo puedes revisar el post ["Angular CLI Workspace Bajo la Lupa"](https://medium.com/angular-chile/angular-cli-workspace-bajo-la-lupa-417b9e7eb836)

![Nueva configuracion](http://nicoavila.s3.amazonaws.com/articulos/18_03configuracion-certificacion.jpg)

Este objeto define como en compilador de Angular se comportará al realizar el build de la aplicación. Revisaremos a continuación cada uno de los atributos:

* **optimization:** Este atributo define el nivel de optimización del build. Sus valores posibles son ```true|false```.
* **outputHashing:** Este atributo permite la salida de hashing el los nombres de los archivos para así manejar correctamente el cache. Sus valores posibles son ```true|false```.
* **sourceMaps:** Este atributo permite la creación de Sourcemap. Sus valores posibles son ```true|false```.
* **extractCss:** Este atributo permite extraer el CSS de los estilos globales en archivos CSS en vez desde JS. Sus valores posibles son ```true|false```.
* **namedChunks:** Este atributo permite utilizar el nombre de los archivos para los chunks que son utilizados mediante lazy-loading. Sus valores posibles son ```true|false```.
* **aot:** Este atributo habilita el **Ahead-of-Time Compilation**. Sus valores posibles son ```true|false```.
* **extractLicenses:** Este atributo habilita la generación de un archivo que contenga las licencias. Sus valores posibles son ```true|false```.
* **vendorChunk:** Este atributo habilita la separación de un bundle que contenga solo las librerías. Sus valores posibles son ```true|false```.
* **buildOptimizer:** Este atributo permite reducir el tamaño de los bundles (los tiempos de compilación aumentan). Sus valores posibles son ```true|false```.
* **fileReplacements:** Este atributo corresponde a un objeto que a su vez contiene dos atributos: **replace** y **with**. Al momento de compilar, Angular reemplazará todas las ocurrencias del archivo declarado en el atributo **replace** por el declarado en el **with**.

Este objeto de configuración nos permite un manejo granular sobre cada uno de los parámetros configurables de un build. Para efectos de este tutorial dejaremos la misma configuración de un ambiente de producción :smiley:.

Si queremos utilizar el nuevo ambiente en ```ng serve```, debemos agregar lo siguiente en:

**projects -> NOMBRE PROYECTO -> architect -> serve -> configurations**

```json
"certification": {
  "browserTarget": "tutorial-ambientes-personalizados-angular:build:certification"
}
```

## Realizando el build con el nuevo ambiente

Para utilizar el nuevo ambiente en un build debemos ejecutar en un terminal

```bash
ng build --configuration=certification
```

Si revisamos la aplicación generada en un navegador veremos lo siguiente:

![Nuevo ambiente en aplicación](http://nicoavila.s3.amazonaws.com/articulos/18_04nuevo-ambiente.jpg)