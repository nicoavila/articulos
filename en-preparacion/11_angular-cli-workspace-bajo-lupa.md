# Angular CLI Workspace Bajo la Lupa
### Junio 2018

![Angular CLI Workspace](http://nicoavila.s3.amazonaws.com/articulos/11_01angular-cli-workspace.jpg)

Para poder trabajar con [Angular](https://angular.io/) utilizamos nuestra querida herramienta [Angular CLI](https://cli.angular.io/) :heart:. **Angular CLI** es un proyecto desarrollado por la misma gente de Google para facilitarnos el trabajo de crear una estructura nueva de proyecto, creación de componentes / servicios / directivas, servir la aplicación en un entorno de desarrollo y realizar build, entre otras útiles tareas.  
Para que Angular CLI sepa que hacer correctamente se vale de un archivo dentro de la estructura de nuestro proyecto llamado **angular.json**. En esta publicación revisaremos la estructura, contenido y función de cada uno de sus atributos.

## Estructura y función
Este archivo se encuentra en la carpeta raíz de nuestra aplicación Angular. Corresponde a un archivo que se crea al momento de generar un nuevo proyecto con el comando ```ng new NOMBRE_PROYECTO```. Antiguamente este archivo se llamaba ```angular-cli.json```, pero desde la versión 6.0.0 de **Angular CLI**