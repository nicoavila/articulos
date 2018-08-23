# Integrando Microframeworks CSS en Angular
### Julio 2018

![Integrando Microframeworks](http://nicoavila.s3.amazonaws.com/articulos/20_01integrar-micro-frameworks-angular.jpg)

Con [Angular](https://angular.io/), en unos pocos pasos, podemos tener una webapp funcionando, gracias al [poderoso tooling](https://cli.angular.io/) del framework. Sin embargo, la aplicación generada con el comando ```ng new <NOMBRE-APP>``` no es algo que llame mucho la atención visualmente...

![Default](http://nicoavila.s3.amazonaws.com/articulos/20_02default-app.jpg)

Angular **por defecto no nos entrega un framework CSS con el cual trabajar**. Todo viene como un lienzo blanco para que podamos escribir / integrar lo que estimemos conveniente. Tenemos alternativas como [Angular Material](https://material.angular.io/), que sistematiza el uso de componentes visuales utilizando [Material Design](https://material.io/design/).  
Normalmente es una gran alternativa! Pero habrán ocasiones en las que no se necesiten frameworks tan complejos, no se requiera utilizar material design, o simplemente el objetivo sea utilizar alternativas sencillas, con bajo footprint en el proyecto. Es ahi donde los **microframeworks CSS** pueden sernos de utilidad.  
Existen muchas alternativas actualmente. Solo por nombrar una pequeña lista, podemos encontrar:

* [Bulma](https://bulma.io/)
* [Chota](https://jenil.github.io/chota/)
* [Lit](https://ajusa.github.io/lit/)
* [Miligram](https://milligram.io/)
* [MiniCSS](https://minicss.org/)
* [PureCSS](https://purecss.io/)
* [Wing](https://kbrsh.github.io/wing/)

> Existe un repositorio muy útil llamado [Awesome CSS Frameworks](https://github.com/troxler/awesome-css-frameworks) donde pueden encontrar una lista mucho más extensa. Procuren darle una :star:

¿Cuál es la mejor manera de utilizar un framework CSS con Angular? La alternativa que más se utiliza es delegar el trabajo a [Angular CLI](https://cli.angular.io), para que cuando se haga el [serve](https://github.com/angular/angular-cli/wiki/serve) o [build](https://github.com/angular/angular-cli/wiki/build) de la aplicación, las hojas de estilo que componen el framework estén dentro del proyecto.  
En este breve tutorial aprenderemos como integrar un framework CSS ([Bulma](https://bulma.io/)) en una aplicación generada con Angular CLI.

> Este tutorial asume que ya poseen **Angular CLI** instalado. Si ese no es el caso, pueden dirigirse a la [página oficial del proyecto](https://cli.angular.io/) e instalar todo lo necesario.

## Generar la nueva aplicación
El primer paso será generar una nueva aplicación. Para eso abriremos una terminal de sistema y ejecutaremos lo siguiente:

```bash
ng new mi-super-aplicacion
```
Se generará una nueva aplicación y se instalarán todas las dependencias necesarias.

## Instalando la nueva dependencia: Bulma CSS
Bulma CSS es un framework

## Modificando el archivo angular.json

## Serve / Build de la aplicación
