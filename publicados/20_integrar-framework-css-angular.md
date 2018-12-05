# Integrando Frameworks CSS en Angular
### Agosto 2018

![Integrando Frameworks](http://nicoavila.s3.amazonaws.com/articulos/20_01integrar-micro-frameworks-angular.jpg)

Con [Angular](https://angular.io/), en unos pocos pasos, podemos tener una aplicación web funcionando, gracias al [poderoso tooling](https://cli.angular.io/) del framework. Sin embargo, la aplicación generada con el comando ```ng new <NOMBRE-APP>``` no es algo que llame mucho la atención visualmente...

![Default](http://nicoavila.s3.amazonaws.com/articulos/20_02default-app.jpg)

Angular **por defecto no nos entrega un framework CSS con el cual trabajar**. Todo viene como un lienzo blanco para que podamos escribir / integrar lo que estimemos conveniente. Tenemos alternativas como [Angular Material](https://material.angular.io/), que sistematiza el uso de componentes visuales utilizando [Material Design](https://material.io/design/).  
Normalmente es una gran alternativa! Pero habrán ocasiones en las que no se necesiten frameworks tan complejos, no se requiera utilizar material design, o simplemente el objetivo sea utilizar opciones sencillas, con bajo footprint en el proyecto. Es ahi donde los **Frameworks CSS** pueden sernos de utilidad.  
Existen muchas alternativas actualmente. Solo por nombrar una pequeña lista, podemos encontrar:

* [Bulma](https://bulma.io/)
* [Chota](https://jenil.github.io/chota/)
* [Lit](https://ajusa.github.io/lit/)
* [Miligram](https://milligram.io/)
* [MiniCSS](https://minicss.org/)
* [PureCSS](https://purecss.io/)
* [Wing](https://kbrsh.github.io/wing/)

> Existe un repositorio muy útil llamado [Awesome CSS Frameworks](https://github.com/troxler/awesome-css-frameworks) donde pueden encontrar una lista mucho más extensa. Procuren darle una :star:

¿Cuál es la mejor manera de utilizar un framework CSS con Angular? La alternativa que más se utiliza es delegar el trabajo a [Angular CLI](https://cli.angular.io), para que cuando se realice el [serve](https://github.com/angular/angular-cli/wiki/serve) o [build](https://github.com/angular/angular-cli/wiki/build) de la aplicación, las hojas de estilo que componen el framework estén dentro del proyecto.  
En este breve tutorial aprenderemos como integrar un framework CSS ([Bulma](https://bulma.io/)) en una aplicación generada con Angular CLI.

> Este tutorial asume que ya poseen **Angular CLI** instalado. Si ese no es el caso, pueden dirigirse a la [página oficial del proyecto](https://cli.angular.io/) e instalar todo lo necesario.

## Generar la nueva aplicación
El primer paso será generar una nueva aplicación. Para eso abriremos una terminal de sistema y ejecutaremos lo siguiente:

```bash
ng new mi-super-aplicacion
```
Se generará una nueva aplicación y se instalarán todas las dependencias necesarias.

## Instalando la nueva dependencia: Bulma CSS
Bulma es un framework CSS creado por [Jeremy Thomas](https://jgthms.com/), un desarrollador front-end radicado en Londres. El framework se basa en el uso de Flexbox.

![Bulma](http://nicoavila.s3.amazonaws.com/articulos/20_03bulma.png)

Para utilizar Bulma en Angular, es necesario primero instalar la dependencia. En una terminal del sistema, en la carpeta del proyecto recientemente creada ejecuta lo siguiente:

```
npm install bulma
```

## Modificando el archivo angular.json
El siguiente paso es indicarle a Angular CLI que utilice esa dependencia al momento de realizar el **serve** o **build**. Para ello se debe modificar el archivo ```angular.json``` ubicado en la raíz del proyecto. Agrega la siguiente línea dentro del atributo *styles* en *architect* -> *build* -> *options* -> *styles*

```
"node_modules/bulma/css/bulma.min.css"
```

> Para saber más sobre cada uno de los atributos presentes en este archivo te invitamos a leer nuestro artículo [Angular CLI Workspace bajo la lupa](https://medium.com/angular-chile/angular-cli-workspace-bajo-la-lupa-417b9e7eb836)

## Serve / Build de la aplicación

Finalmente, luego de haber realizado los pasos anteriores, nos debemos dirigir a un terminal de sistema y escribir lo siguiente en la carpeta de nuestro proyecto:

```
ng serve
```

Si todo sale correctamente veremos el siguiente resultado:

![Resultado Bulma](http://nicoavila.s3.amazonaws.com/articulos/20_04resultado.png)

Los estilos por defecto del proyecto notoriamente han cambiado!  
Agregaremos un [progress bar]() para probar si todo efectivamente está integrado. Modificaremos el archivo ```app.component.html```, borraremos todo su contenido y lo reemplazaremos por lo siguiente:

```html
<div class="container">
  <div class="columns">
    <div class="column">
      <progress class="progress is-primary" value="15" max="100">15%</progress>
    </div>
  </div>
</div>
```

En nuestro navegador debiese aparecer ahora una barra de progreso estilizada con Bulma

![Progreso Bulma](http://nicoavila.s3.amazonaws.com/articulos/20_05progress_bar.png)