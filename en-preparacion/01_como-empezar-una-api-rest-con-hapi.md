# Cómo empezar una API REST con hapi.js
#### Abril 2018

Llegada la hora de implementar una API en Node tenemos varias alternativas disponibles. Muchos de nosotros quizás acudimos a las viejas herramientas confiables como [ExpressJS](http://expressjs.com/). Sin embargo, actualtualmente contamos con una amplia gama de Frameworks que nos facilitarán bastante el trabajo. Siempre es bueno probar las nuevas alternativas que nos provee la comunidad :smile:.
Al terminar este pequeño tutorial seremos capaces de utilizar los elementos necesarios para construir una API REST usando un framework prometedor: **hapijs**.

![Imagen Principal Artículo](http://nicoavila.s3.amazonaws.com/articulos/01_01como_empezar_api_rest_hapi.jpg)

[Hapi.js](https://hapijs.com/) es un framework creado por [Eran Hammer](https://twitter.com/eranhammer) que en sus palabras nos permite: 

> Crear aplicaciones y servicios enfocándonos en escribir lógica de aplicación reusable en vez de invertir tiempo en construir infraestructura.

Posee una [gran comunidad](https://hapijs.com/community) y un [repositorio muy activo](https://github.com/hapijs/hapi).

Para llevar a cabo este tutorial utilizaremos un cliente HTTP. Particularmente [Insomnia](https://insomnia.rest). Sin embargo, pueden utilizar [cualquiera en el que se sientan cómodos](https://www.getpostman.com/).

![Imagen de Insomnia](https://insomnia.rest/static/main-ac0a1732afac19acce5ad6825595c3bb-f3479.webp)

* El código disponible para este tutorial lo pueden descargar desde el siguiente repositorio: [https://github.com/nicoavila/tutorial-api-hapijs](https://github.com/nicoavila/tutorial-api-hapijs)

## Instalando todo lo necesario
Para comenzar debemos crear un nuevo proyecto en Node. Crearemos una nueva carpeta llamada **hapijs-noders**

```bash
mkdir hapijs-noders  
cd hapijs-noders  
npm init -y
```

Luego debemos instalar **hapijs** como dependencia en nuestro proyecto

``npm install hapi --save``

Una vez realizado ese paso, debemos crear un archivo llamado **app.js**

``touch app.js``

## *"Houston: aquí localhost, cambio..."*
Ya tenemos todo lo necesario para comenzar a trabajar con **hapijs**!. Ahora debemos levantar un servidor que nos permita recibir peticiones a nuestra futura API. Editaremos el archivo **app.js** agregando lo siguiente

```javascript
const Hapi = require('hapi');

const server = Hapi.server({
  port: 3000,
  host: 'localhost',
  app: {
    version: 1
  }
});

const iniciarServer = async () => {
  try {
    await server.start();
    console.log(`Servidor corriendo en: ${server.info.uri}`);
  } catch (error) {
    console.log('Error al iniciar el servidor Hapi');
  }
};

iniciarServer();
```

En la consola ejecutaremos ```node app.js``` para iniciar el servidor. Si todo sale bien veremos un mensaje: ```Servidor corriendo en: http://localhost:3000```. Si vamos a un navegador web y accedemos a la URL mencionada veremos una respuesta similar a esta:

![Imagen 404 Rutas](http://nicoavila.s3.amazonaws.com/articulos/03_03localhost_404.jpg);

¿Qué pasó aquí? :scream: Sucede que nuestro servidor no conoce la URL a la cual estamos tratando de acceder! (/). Es por ello que debemos declarar las rutas permitidas.

## Todos los caminos llegan a Roma: Routes
> Las rutas corresponden a la forma en como cierta URL en nuestra aplicación responde ante la petición HTTP de un cliente a través de los distintos métodos disponibles (GET, POST, PUT, PATCH, DELETE).

Debemos definir las rutas que nuestro servidor soportará. Esto podemos hacerlo de dos maneras: 
* En nuestro archivo ```app.js```.
* En un plugin.

Los *Plugins* en **hapijs** permiten modularizar funcionalidades para así reutilizarlas en otros lugares de la aplicación. Es la elección ideal para definir las rutas de nuestra API :smile:.

Crearemos un archivo llamado ```routes.js``` en la carpeta raíz de nuestro directorio y agregaremos lo siguiente:

```javascript
module.exports = {
  name: "ApiRoutes",
  register: async (server, options) => {
    server.route([
      {
        method: "GET",
        path: "/",
        handler: async (request, h) => {
          return "Hola Noders!";
        }
      }
    ]);
  }
}
```

En un plugin, los atributos **name** y **register** son necesarios. El atributo **name** debe ser único dentro de nuestra aplicación (no pueden existir dos plugins con el mismo nombre). La función **register** contiene la lógica necesaria para registrar el plugin en nuestro servidor Hapi.
**server.route()** puede contener una o varias rutas definidas dentro de un array.

Finalmente necesitaremos incluir las rutas en el archivo ```app.js```:

```javascript
const Hapi = require('hapi');
const routes = require("./routes"); //Agregamos esta línea

const server = Hapi.server({
  port: 3000,
  host: 'localhost',
  app: {
    version: 1
  }
});

const iniciarServer = async () => {
  try {
    await server.register(routes); //Agregamos esta línea
    await server.start();
    console.log(`Servidor corriendo en: ${server.info.uri}`);
  } catch (error) {
    console.log('Error al iniciar el servidor Hapi');
  }
};

iniciarServer();
```

Reiniciamos el servidor presionando ```Ctrl + C``` y ejecutamos nuevamente ```node app.js```. Al recargar la página ```http://localhost:3000``` en nuestro navegado obtendremos la siguiente respuesta:

![Imagen Hola Noders](http://nicoavila.s3.amazonaws.com/articulos/04_04hola_noders.jpg)

## Agregando nuevas rutas

## El camino recién comienza!
Aun quedan muchas cosas que se deben resolver: Autenticación, conexión a una base de datos, etc. Sin embargo, ya tenemos mucho camino recorrido. Ahora es el turno de ustedes para usar *hapijs* y construir aplicaciones geniales con este framework.