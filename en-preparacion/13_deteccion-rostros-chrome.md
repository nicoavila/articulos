# Detección de rostros en Google Chrome
### Junio 2018

![Detección rostros](http://nicoavila.s3.amazonaws.com/articulos/13_01deteccion-rostros-chrome.jpg)

A partir de la versión 57 de [Google Chrome](https://www.google.es/chrome/index.html) se hace disponible una API experimental llamada [Shape Detector](https://wicg.github.io/shape-detection-api/#introduction) la que nos permite, entre otras cosas, poder detectar rostros y sus características en imágenes. Esta es todavía una función experimental y por ningún motivo es recomendable usarla aun en un proyecto en producción, pero siempre es entretenido probar nuevas características :heart:.  
En esta publicación exploraremos algunos métodos de [FaceDetector](https://wicg.github.io/shape-detection-api/#face-detection-api), reconociendo características de rostros en una imágen utilizando el navegador Google Chrome.

> El repositorio de este tutorial pueden encontrarlo en [https://github.com/nicoavila/tutorial-facedetector-chrome](https://github.com/nicoavila/tutorial-facedetector-chrome)

## Primer paso: Habilitar Características Experimentales
El primer paso es dirigirnos a la siguiente URL en nuestro navegador:

```chrome://flags/```

Esto abrirá una ventana con las opciones experimentales de Chrome. En esta sección podemos habilitar / deshabilitar características que aun no están del todo integradas.  
En la barra superior de búsqueda debemos escribir **Experimental Web Platform features** para filtrar la opción que nos permitirá habilitar la API de *Shape Detection*. Seleccionaremos la opción **Enabled**.

![Habilitar Experimental Web Platform Features](http://nicoavila.s3.amazonaws.com/articulos/13_02habilitar-experimental-web-platform-features.jpg)

Chrome levantará un mensaje en la sección inferior de la ventana: **Los cambios se aplicarán la próxima vez que reinicies Google Chrome**. Debemos presionar el botón **RELAUNCH NOW** para reiniciar la instancia del navegador para que los cambios surtan efecto.

Luego de reiniciar el navegador, abriremos una consola en las herramientas de desarrollador y escribiremos ```FaceDector```. Si todo sale correctamente, la consola debe sugerir esta característica como un atributo del objeto ```Window```.

![FaceDetector en consola](http://nicoavila.s3.amazonaws.com/articulos/13_03facedetector-consola.jpg)

## Escribiendo lo necesario
Debemos crear un archivo llamado ```index.html``` que contenga lo siguiente:

```html
<!doctype html>
  <head>
    <meta charset="utf-8">
    <title>Ejemplo con FaceDetector</title>
    <meta name="description" content="Ejemplo con FaceDetector">
    <meta name="author" content="Nicolas Avila">
    <link rel="stylesheet" href="estilos.css">
  </head>

  <body>
    <h1>Ejemplo con FaceDetector</h1>
    <hr>
    <p>Seleccione una imagen desde sus documentos para detectar rostros en ella</p>
    <input type="file" name="imagen" onchange="manejaArchivo(this.files)">
    <canvas class="imagen_container" width="500px" height="358px"></canvas>
    <script src="script.js"></script>
  </body>
</html>
```

![HTML](http://nicoavila.s3.amazonaws.com/articulos/13_04html-layout.jpg)

Agregamos un input de tipo **file** que nos permita seleccionar un archivo en nuestro equipo. Adicionalmente agregamos un **canvas** que será el encargado de dibujar todo lo necesario.

Crearemos un archivo llamado ```script.js```. Este archivo va a contener toda la lógica encargada de:

* Manejar el nuevo archivo que se seleccione. De ser un archivo imagen, creará un objeto [HTMLImageElement](https://html.spec.whatwg.org/multipage/embedded-content.html#htmlimageelement) con la información de la imagen seleccionada.
* Detectar el o los rostros y sus correspondientes características.
* Dibujar los bounding box y puntos que indiquen las características de los rostros en una imagen.

Agregaremos lo siguiente:

```javascript
var preview = document.querySelector('.imagen_container');
var context = preview.getContext('2d');

// Maneja el archivo seleccionado
function manejaArchivo(files) {  
  for (file of files) {
    if (file.type.match(/image\/*/)) {
      let reader = new FileReader();
      reader.onload = function(e) {
        let image = new Image();
        image.onload = function() {
          context.drawImage(image,0,0);
          detectaRostros(image);
        }
        image.src = e.target.result;
      }
      reader.readAsDataURL(file);
    } 
  }
}
```
Esta función permite manejar los archivos de tipo imagen que se seleccionen en el input de tipo archivo, para luego crear un objeto **HTMLImageElement** con los datos obtenidos de un **FileReader**. Se llamará a otra función llamada ```detectaRostros()``` que obtiene como parámetro una instancia de la imágen seleccionada.

## FaceDetector: detectando rostros
FaceDetector corresponde al objeto principal a revisar. Posee un método llamado [detect()](https://wicg.github.io/shape-detection-api/#dom-facedetector-detect%E2%91%A0) que recibe una imagen para así identificar uno o varios [DetectedFace](https://wicg.github.io/shape-detection-api/#detectedface-section). Si el método recibe una imagen válida (es decir, una imagen que contenga rostros), el objeto DetectedFace va a contener dos atributos:

1. **boundingBox:** Define la posición respecto al origen de un rectángulo que identifica la cara.
2. **landmarks:** Define 1 o más características encontradas en el rostro, junto a su posición respecto al origen. Los tipos posibles son **eyes**, **mouth** y **nose**.

Agregaremos las siguientes funciones a nuestro archivo ```script.js```:

```javascript
// Detecta rostros y los landmarks en él
function detectaRostros(image) {
  let faceDetector = new FaceDetector({fastMode: false});
  faceDetector.detect(image).then(function(rostros) {
    rostros.forEach(function(rostro){
      dibujaCaracteristicas(rostro);
    });
  });
}

// Dibuja las caracteristicas en el canvas
function dibujaCaracteristicas(caracteristicas) {
  context.fillStyle = 'cyan';
  context.strokeStyle = 'cyan';
  context.rect(
    caracteristicas.boundingBox.x, 
    caracteristicas.boundingBox.y, 
    caracteristicas.boundingBox.width, 
    caracteristicas.boundingBox.height
  );
  context.fillText('rostro', caracteristicas.boundingBox.x, caracteristicas.boundingBox.y - 5);
  context.stroke();
  caracteristicas.landmarks.forEach(function(caracteristica){
    let tipo = '';
    switch(caracteristica.type) {
      case 'eye': tipo = 'ojo'; break;
      case 'mouth': tipo = 'boca'; break;
      case 'nose': tipo = 'nariz'; break;
    }
    
    context.fillText(tipo, caracteristica.location.x + 5, caracteristica.location.y + 5);
    context.beginPath();
    context.arc(caracteristica.location.x, caracteristica.location.y, 3, 0, Math.PI * 2, true);
    context.fill();
  });
}
```
La función ```detectaRostros()``` es la encargada de crear una nueva instancia de **FaceDetector**. Esta recibe una imagen y retorna una Promesa. Cada uno de los rostros encontrados son almacenados temporalmente en la variable **rostros**, para así luego iterar sobre ellos y ejecutar la función ```dibujaCaracteristicas()```.  
La función ```dibujaCaracteristicas()``` recibe una instancia de **DetectedFace** para así iterar sobre cada una de sus características. Primero se dibuja el rectángulo correspondiente al rostro, **boundigBox**, para luego dibujar puntos sobre cada uno de los **landmarks** detectados.

El resultado será algo similar a esto:

![Resultado FaceDetector](http://nicoavila.s3.amazonaws.com/articulos/13_05resultado.jpg)

Sin duda **Shape Detection API** es una gran iniciativa pero que aun debe madurar en su especificación. Si revisan la documentación hay características muy interesantes, como [Barcode Detection API](https://wicg.github.io/shape-detection-api/#barcode-detection-api), que en un futuro permitirá elaborar aplicaciones muy útiles solo ocupando nuestro navegador para ello :smiley:.

Si te gustó el artículo, no dudes en compartirlo y dejar algunos claps :clap:

*Front End Chile, es una comunidad chilena dedicada a compartir, enseñar y aprender sobre el desarrollo web. Encuéntranos en Facebook*