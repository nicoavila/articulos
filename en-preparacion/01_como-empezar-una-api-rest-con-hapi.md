# Cómo empezar una API RESTful con hapi.js
#### Abril 2018

Llegada la hora de implementar una API en Node tenemos varias alternativas disponibles. Muchos de nosotros quizás acudimos a las viejas herramientas confiables como [ExpressJS](http://expressjs.com/). Sin embargo, actualmente contamos con una amplia gama de Frameworks que nos facilitarán bastante el trabajo. Siempre es bueno probar las nuevas alternativas que nos provee la comunidad :smile:.  
Al terminar este pequeño tutorial seremos capaces de utilizar los elementos necesarios para construir una API REST usando el framework **hapijs**.

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
Ya tenemos todo lo necesario para comenzar a trabajar con **hapijs**. Ahora debemos levantar un servidor que nos permita recibir peticiones a nuestra futura API. Editaremos el archivo **app.js** agregando lo siguiente

```javascript
const Hapi = require('hapi');

const server = Hapi.server({
  port: 3000,
  host: 'localhost',
  app: {}
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

La variable ```server``` contiene la nueva instancia del servidor **hapi**. Los atributos **port** y **host** son obligatorios, mientras que **app** es opcional. Este último es un objeto que contine una serie de atributos que pueden ser compartidos a lo largo de la aplicación usando ```server.settings.app.NOMBRE_PROPIEDAD```.

En la consola ejecutaremos ```node app.js``` para iniciar el servidor. Si todo sale bien veremos un mensaje: ```Servidor corriendo en: http://localhost:3000```. Si vamos a un navegador web y accedemos a la URL mencionada veremos una respuesta similar a esta:

![Imagen 404 Rutas](http://nicoavila.s3.amazonaws.com/articulos/03_03localhost_404.jpg);

¿Qué pasó aquí? :scream: Sucede que nuestro servidor no conoce la URL a la cual estamos tratando de acceder! (/). Es por ello que debemos declarar las rutas permitidas.

## Todos los caminos llegan a Roma: Routes
> Las rutas corresponden a la forma en como cierta URL en nuestra aplicación responde ante la petición HTTP de un cliente a través de los distintos métodos disponibles (GET, POST, PUT, DELETE).

Debemos definir las rutas que nuestro servidor soportará. Esto podemos hacerlo de dos maneras: 
* En nuestro archivo ```app.js```.
* En un plugin.

Los *Plugins* en **hapijs** permiten modularizar funcionalidades para así reutilizarlas en otros lugares de la aplicación. Es la elección ideal para definir las rutas de nuestra API :smile:.

Crearemos un archivo llamado ```routes.js``` en la carpeta raíz de nuestro directorio y agregaremos lo siguiente:

```javascript
module.exports = {
  name: 'ApiRoutes',
  register: async (server, options) => {
    server.route([
      {
        method: 'GET',
        path: '/',
        handler: async (req, res) => {
          return 'Hola Noders!';
        }
      }
    ]);
  }
}
```

En un plugin los atributos **name** y **register** son necesarios. El atributo **name** debe ser único dentro de nuestra aplicación (no pueden existir dos plugins con el mismo nombre). La función **register** contiene la lógica necesaria para registrar el plugin en nuestro servidor Hapi.
**server.route()** puede contener una o varias rutas definidas dentro de un array.

Finalmente necesitaremos incluir las rutas en el archivo ```app.js```:

```javascript
const Hapi = require('hapi');
const routes = require("./routes"); //Agregamos esta línea

const server = Hapi.server({
  port: 3000,
  host: 'localhost',
  app: {}
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
Nuestro servidor ya responde ante peticiones de un cliente. Debemos agregar nuevas rutas para que nuestra API responda ante otros métodos como POST, PUT y DELETE.

### GET con parámetros
Agregaremos un nuevo objeto en el array de la función ```server.route([])``` **routes.js** con la siguiente información

```javascript
{
  method: 'GET',
  path: '/usuarios/{nombre?}',
  handler: async (req, res) => {
    const nombre = (req.params.nombre) ? req.params.nombre : 'invitado';
    return `Hola ${nombre}!`;
  }
}
```

Esta nueva ruta permite recibir **parámetros**. La propiedad *path* de nuestra ruta posee el nombre del parámetro que deseamos recibir (en este caso *nombre*). Cada parámetro a recibir en el path de la ruta debe ser declarado entre llaves ```{}```. El signo de interrogación ```?``` al final del nombre del parámetros nos indica que este parámetro es *optativo*.

Al volver a nuestro navegador web e ingresar la siguiente dirección: ```http://localhost:3000/usuarios/noders``` veremos el mensaje *Hola noders!*. Si solo ingresamos ```http://localhost:3000/usuarios``` veremos el mensaje *Hola invitado!*

![Imagen GET Parámetros](http://nicoavila.s3.amazonaws.com/articulos/05_05get_parametros.jpg)

### POST
Ahora agregaremos una nueva ruta para recibir una petición POST

```javascript
{
  method: 'POST',
  path: '/usuarios',
  handler: async (req, res) => {
    const newUser = {
      nombre: req.payload.nombre,
      apellido: req.payload.apellido
    };
    return res.response({
      datos: newUser
    }).type('application/json');
  }
}
```

Esta ruta es ligeramente distinta, ya que hemos agregado la posibilidad de recibir parámetros mediante POST, los que son capturados a través de ```req.payload.NOMBRE_DEL_PARAMETRO```. La ruta devuelve una respuesta en formato [JSON](https://json.org/json-es.html) mediante el uso de ```res.response({DATOS}).type('application/json')```, donde ```{DATOS}``` corresponde al objeto que deseamos incluir en la respuesta.  

Para poder probar esta nueva ruta, usaremos el cliente HTTP Insomnia. Abrimos el programa y creamos una nueva petición haciendo click en el botón con el signo (+), opción *New Request*:

![Imagen creación petición](http://nicoavila.s3.amazonaws.com/articulos/06_06insomnia_nueva_peticion.jpg)

Se desplegará una ventana modal en donde debemos indicar un *nombre amigable para la petición*, el *método* que utilizaremos y si tiene cuerpo o no. Luego de ingresar la información presionaremos el botón *Create*:

![Imagen creación petición](http://nicoavila.s3.amazonaws.com/articulos/07_07insomnia_dialogo_nueva_peticion.jpg)

Ahora procederemos a indicar a que URL debe apuntar nuestra nueva petición. Ingresaremos ```http://localhost:3000/usuarios``` y seleccionaremos la opción *POST* desde el menú dropdown.

![Imagen creación petición](http://nicoavila.s3.amazonaws.com/articulos/08_08insomnia_configuracion_peticion.jpg)

Para poder enviar información mediante POST es necesario especificar que utilizaremos un *cuerpo en esta petición*. Seleccionaremos la opción *Form URL Encoded* desde el menú dropdown.

![Imagen creación petición](http://nicoavila.s3.amazonaws.com/articulos/09_09insomnia_cuerpo.jpg)

En la configuración del cuerpo de la petición agregaremos el atributo *nombre* y *apellido* con los valores indicados en la imagen. Finalmente para enviar la petición a nuestro servidor presionaremos el botón *Send*

![Imagen creación petición](http://nicoavila.s3.amazonaws.com/articulos/10_10insomnia_enviar.jpg)

Nuestro servidor nos responderá esto:

![Imagen creación petición](http://nicoavila.s3.amazonaws.com/articulos/11_11insomnia_respuesta.jpg)

### PUT y DELETE
La utilización de métodos PUT y DELETE es muy similar al ejemplo anterior. Para ello debemos definir nuevas rutas:

```javascript
{
  method: 'PUT',
  path: '/usuarios/{id}',
  handler: async (req, res) => {
    const newUser = {
      nombre: req.payload.nombre,
      apellido: req.payload.apellido
    };
    return res.response({
      datos: newUser,
      mensaje: `Usuario ID: ${req.params.id} modificado exitósamente!`
    }).type('application/json');
  }
},
{
  method: 'DELETE',
  path: '/usuarios/{id}',
  handler: async (req, res) => {
    return res.response({
      mensaje: `Usuario ID: ${req.params.id} eliminado exitósamente!`
    }).type('application/json');
  }
}
```

Para probar estos métodos pueden crear nuevas peticiones en **Insomnia*** utilizando el ejemplo anterior de POST o pueden descargar diréctamente el [proyecto Insomnia](https://github.com/nicoavila/tutorial-api-hapi/blob/master/insomnia.json) desde el repositorio de este artículo y probar con las peticiones ya creadas :smile:

## Esto recién comienza!
Aun quedan muchas cosas que se deben resolver: Autenticación, conexión a una base de datos, etc. Sin embargo, ya tenemos mucho camino recorrido.  
Ahora es el turno de ustedes para usar *hapijs* y construir aplicaciones geniales con este framework.