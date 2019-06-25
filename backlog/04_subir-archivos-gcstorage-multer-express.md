# Subir archivos a Google Cloud Storage usando Multer en Express
### Junio 2018

![Imagen del Artículo](https://nicoavila.s3.amazonaws.com/articulos/04_01subir-archivos-gcstorage-multer-express.jpg)

La historia comienza así:

> Cuando tuve que realizar mi primera aplicación "grande" en [Node](https://nodejs.org/en/) elegí [Express](http://expressjs.com/). En algún punto del desarrollo de la aplicación me vi en la necesidad de implementar un upload de archivos hacia un storage en la nube ([Google Cloud Storage](https://cloud.google.com/storage/?hl=es) o [AWS S3](https://aws.amazon.com/es/s3/)). Cree el formulario, lógica necesaria para manejar el archivo, pero nada funcionaba. Debugueando no encontraba una respuesta. Finalmente, luego de un minucioso proceso de *SDD* (StackOverflow Driven Development) llegué a la respuesta. Lo que no sabía era que por defecto el paquete ```body-parser``` no maneja **multipart/form-data**. Ahi fue cuando caí en cuenta que siempre es bueno leer la documentación detalladamente antes de embarcarse en algo :sweat:

Si esta historia les resulta familiar, entonces bienvenidos! :tada:. En este post crearemos una aplicación en Express, que permita recibir datos a través de un formulario con encoding `multipart/form-data`, para así luego manejar la información y subirla a [Google Cloud Storage](https://cloud.google.com/storage/?hl=es).

## ¿Que es multipart/form-data?
multipart/form-data pertenece a uno de los tres valores posibles para el atributo ```enctype``` de una etiqueta ```<form>```.
El atributo ```enctype``` indica como la información en el cuerpo de una petición será codificada. Particularmente este tipo de codificación permite subir archivos mediante el atributo POST. Los otros valores posibles para el atributo `enctype` son:

* `application/x-www-form-urlencoded`
* `text/plain`.

Si no se declara una etiqueta ```enctype``` en un formulario, se asume por defecto el tipo ```application/x-www-form-urlencoded```.  
Para utilizar ```multipart/form-data``` es necesario incluir una etiqueta ```<input>``` de tipo **file** en el formulario.

## Express y multipart/form-data
Por defecto, el módulo ```body-parser``` (el módulo encargado de manejar los cuerpos de los requests en Express) no permite manejar **multipart/form-data** :sweat:. Citando a su documentación:

> This does not handle multipart bodies, due to their complex and typically large nature.

Es necesario entonces utilizar otra vía para poder manejar archivos cuando hagamos una petición POST.  
Utilizaremos un módulo llamado ```multer``` 
