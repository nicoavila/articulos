# Subir archivos a Google Cloud Storage usando Multer en ExpressJS
### Junio 2018

![Imagen del Artículo](http://nicoavila.s3.amazonaws.com/articulos/04_01subir-archivos-gcstorage-multer-express.jpg)

La historia comienza así:

> Cuando tuve que realizar mi primera aplicación "grande" en [Node](https://nodejs.org/en/) elegí el framework [Express](http://expressjs.com/). En algún punto del desarrollo de la aplicación me vi en la necesidad de implementar un upload de archivos hacia un storage en la nube (Google Cloud Storage o AWS S3). Lo que no sabía era que por defecto el paquete ```body-parser``` no maneja **multipart/form-data**. Ahi fuí cuando caí en cuenta que siempre es bueno leer la documentación detalladamente antes de embarcarse en algo :sweat:

Si esta historia les resulta familiar, entonces bienvenidos! :tada:. En esta publicación crearemos una aplicación en Express, que permita recibir datos a través de un formulario con encoding multipart/form-data, para así luego manejar la información y subirla a Google Cloud Storage.

## ¿Que es multipart/form-data?
multipart/form-data pertenece a uno de los tres valores posibles para el atributo ```enctype``` de una etiqueta ```<form>```. Permite subir archivos mediante el atributo POST, siguiendo la especificación del estándar [RFC7578](https://tools.ietf.org/html/rfc7578). Los otros valores posibles para el atributo *enctype* son:

* application/x-www-form-urlencoded
* text/plain.

Si no se declara una etiqueta ```enctype``` en un formulario, se asume por defecto el tipo ```application/x-www-form-urlencoded```.  
Para utilizar ```multipart/form-data``` es necesario incluir un input de tipo **file** en el formulario.

## Express y multipart/form-data
Por defecto, el paquete ```body-parser``` no permite manejar **multipart/form-data** :sweat:. Citando a su documentación:

> This does not handle multipart bodies, due to their complex and typically large nature.

Es necesario