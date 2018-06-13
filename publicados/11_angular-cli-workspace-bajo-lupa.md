# Angular CLI Workspace Bajo la Lupa
### Junio 2018

![Angular CLI Workspace](http://nicoavila.s3.amazonaws.com/articulos/11_01angular-cli-workspace.jpg)

Para poder trabajar con [Angular](https://angular.io/) utilizamos nuestra querida herramienta [Angular CLI](https://cli.angular.io/) :heart:. **Angular CLI** es un proyecto desarrollado por la misma gente de Google para facilitarnos el trabajo de crear una estructura nueva de proyecto, creación de componentes / servicios / directivas, servir la aplicación en un entorno de desarrollo y realizar build, entre otras útiles tareas.  
Para que Angular CLI sepa que hacer, se vale de un archivo llamado **Angular CLI Workspace**. Este archivo se encuentra dentro de la estructura de nuestro proyecto y se identifica con el nombre **angular.json**.  
En esta publicación revisaremos la estructura, contenido y función de cada uno de sus atributos. Se asume la ejecución previa de ```ng new angular-workspace-test``` para generar una nueva aplicación angular y así revisar el contenido del archivo *angular.json*.

> Cada uno de los contenidos del archivo Angular CLI Workspace se encuentra definido en la [documentación oficial](https://github.com/angular/angular-cli/wiki/angular-workspace) de ```angular-cli```

## Estructura y función
Este archivo se encuentra en la carpeta raíz de nuestra aplicación Angular. Corresponde a un archivo que se crea al momento de generar un nuevo proyecto con el comando ```ng new NOMBRE_PROYECTO```. Antiguamente este archivo se llamaba ```angular-cli.json```, pero desde la versión 6.0.0 de **Angular CLI** cambió de nombre (y de estructura).  
Si migramos un proyecto a Angular 6 con el comando ```ng update```, Angular CLI tomará el contenido del archivo *angular-cli.json* y lo copiará en un nuevo archivo *angular.json*, respetando la nueva estructura y eliminando el archivo con la estructura antigua.

![Ubicación angular.json](http://nicoavila.s3.amazonaws.com/articulos/11_02ubicacion-archivo.jpg)

La función de este archivo es servir de guía para Angular CLI, declarando una serie de atributos que permiten a esta herramienta realizar operaciones sobre nuestra aplicación.
A continuación revisaremos cada uno de los atributos de este archivo:

### $schema
Este atributo de tipo string define el **JSON Schema** a utilizar en este archivo. [JSON Schema](http://json-schema.org/) es un vocabulario que permite anotar y validar documentos JSON. Permite a Angular CLI respetar y asegurar la especificación de **Angular Workspace Schema** declarado en ```node_modules/@angular/cli/lib/config/schema.json```.

![Angular CLI Schema](http://nicoavila.s3.amazonaws.com/articulos/11_03angular-schema.jpg)

La especificación de una estructura define todos los atributos posibles que puede contener el archivo **angular.json**, así como sus valores posibles. También ayuda al autocompletado :+1:.

### version
Número entero que declara cual es la versión actual de la especificación de Angular Workspace. Actualmente es la **1**.

### newProjectRoot
String que corresponde al path donde los nuevas aplicaciones o librerías serán creadas.

> **¿Por qué múltiples proyectos?** Si nos fijamos en el atributo **projects**, vamos a tener 2 proyectos. Esto es porque Angular CLI creará un proyecto principal basado en el nombre del proyecto que declaramos en la ejecución del comando ```ng new```. También se genera un proyecto **e2e** (End-to-End Testing), ya que corresponde a un tipo de testing que utiliza [Protractor](https://www.protractortest.org/#/) totalmente desacoplado de la aplicación principal.

### defaultProject
String que declara el nombre del proyecto principal a utilizar.

### projects
Objeto que declara la configuración para cualquier proyecto en nuestro workspace. Por defecto vienen 2 proyectos: el **proyecto principal** que hemos generado y un **proyecto e2e (End-To-End)** para realizar testing.  
Cada proyecto es configurado con los siguientes atributos:

* **root:** Propiedad que define cual es el directorio principal con todos los archivos de los proyectos. Normalmente es una string vacía.
* **sourceRoot:** Propiedad que especifica el directorio donde se encontrará el código de fuente escrito por nosotros.
* **projectType:** Propiedad que especifica el tipo de proyecto. Desde la versión 6 de Angular CLI es posible generar proyectos internos que viven dentro de nuestro proyecto principal, así como **librerías** (abordaremos la creación de librerías utilizando Angular en un post futuro :smiley:).
* **prefix:** Propiedad que declara el prefijo utilizado al momento de crear nuestros *componentes* y *directivas*. La [guía de estilo de Angular](https://angular.io/guide/styleguide) especifica el uso de un prefijo para así evitar posible colisiones de nombres.
* **schematics:** Propiedad que permite definir la configuración del módulo de Schematics a nivel de proyecto.
* **architect:** Cada proyecto permite definir una serie de acciones como el building, serve de la aplicación, testing y linting. Estas tareas están basadas en "**builders**" predeterminados definidos dentro de Angular CLI. Estas configuraciones son llamadas **Architect Targets**

![Serve Architect Target](http://nicoavila.s3.amazonaws.com/articulos/11_04serve-architect-target.jpg)

## Atributos no presentes por defecto
Existen ciertos atributos no presentes en un archivo Angular CLI Workspace por defecto. Los describiremos brevemente a continuación:

### cli
Objeto que permite definir opciones de configuración para Angular CLI. Posee los siguientes atributos:

* **defaultCollection:** Propiedad que permite definir que set de Schematics está en uso. Permite declarar el uso de Schematics de terceras partes.
* **packageManager:** Propiedad que permite declarar el package manager a utilizar. Comúnmente se utiliza ```npm```, pero también es posible declarar otro como ```yarn```.
* **warnings:** Propiedad que permite manejar los warnings que aparecen ante la ejecución de comandos de Angular CLI.

![Warnings](http://nicoavila.s3.amazonaws.com/articulos/11_05warnings.jpg)

### schematics
Para generar los archivos y sus relaciones, Angular se vale del módulo ```schematics```, que permite construir los archivos respetando la estructura necesaria. Este módulo le dice a Angular CLI, por ejemplo, cómo debe generar la estructura de un nuevo componente.  
En esta propiedad es posible declarar configuraciones para el módulo de **schematics** particulares. Para más información de Schematics, pueden visitar el [repositorio oficial de angular-cli](https://github.com/angular/angular-cli/tree/master/packages/schematics)




