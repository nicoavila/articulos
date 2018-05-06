# Reconocimiento de emociones con AWS Rekognition y Node
### Mayo 2018

La inteligencia artificial (IA) y Machine Learning (ML) cada vez está tomando más fuerza. Todos los días nos [asombramos de los avances en el campo](https://www.forbes.com/sites/tonybradley/2017/07/31/facebook-ai-creates-its-own-language-in-creepy-preview-of-our-potential-future/).
Para trabajar con IA se requiere un amplio conocimiento de **álgebra lineal**, **estadísticas** y **cálculo** con el fin de crear e impementar modelos que permitan a la máquina "pensar". Los gigantes tecnológicos como [Google](https://cloud.google.com/products/machine-learning/), [Microsoft](https://azure.microsoft.com/en-us/services/machine-learning-studio/) y [Amazon Web Services](https://aws.amazon.com/es/machine-learning/) (AWS) han creado sus propios servicios relacionados con *Machine Learning disponibles para desarrolladores y público general, democratizando así el acceso a las ventajas de la IA que puede tener en nuestra aplicaciones.  
En este tutorial utilizaremos la API de [AWS Rekognition](https://aws.amazon.com/es/rekognition/) para crear una pequeña aplicación de consola que nos permita determinar que emoción tienen las fotografías presentes en un directorio.

![Imagen Principal](http://nicoavila.s3.amazonaws.com/articulos/03_01reconocimiento-emociones-aws-rekognition-node.jpg)

## Preparando un nuevo proyecto en Node
Crearemos un nuevo proyecto en Node para luego crear un archivo llamado ```app.js```.

```bash
mkdir rekognition-node
npm init -y
cd rekognition-node
touch app.js
```

## Amazon Web Services
Para realizar este tutorial necesitaremos contar una cuenta de [Amazon Web Services](https://aws.amazon.com/es/). Ofrecen un tier gratuito de procesamiento para 5000 imágenes mensuales, lo que basta y sobra para nuestro pequeño proyecto :smile:. Pueden crear una nueva cuenta [aquí](https://aws.amazon.com/es/free/).

### Creando un Access Key
El SDK de AWS require el uso de un **Access Key**, el cual debe ser creado desde la [Consola de Administración de AWS](http://console.aws.amazon.com/). Para ello debemos iniciar sesión con el email y contraseña de su cuenta.

![Iniciar Sesión AWS](http://nicoavila.s3.amazonaws.com/articulos/03_02iniciar-sesion-aws.jpg)

Una vez estemos autenticados, debemos dirigirnos a la administración de usuarios y grupos en AWS IAM.

> IAM permite administrar el acceso a los recursos de AWS a los usuarios, estableciendo *grupos* con *políticas de acceso*.

![Acceder a IAM](http://nicoavila.s3.amazonaws.com/articulos/03_03ingresar-iam.jpg)

Los **grupos** en IAM permiten definir "conjuntos" en donde asociaremos usuarios que tendrán ciertas **políticas de acceso**. Debemos crear un nuevo grupo el cual tendrá acceso a usar la API de AWS Rekognition.  
Hacemos click sobre la opción **Groups** y luego seleccionaremos la opción **Create New Group**. Se desplegará una nueva vista en donde debemos indicar cual será el nombre del nuevo grupo. Utilizaremos el nombre ```grupo-rekognition``` y hacemos click en el botón **Next Step**

![Creación de grupo IAM](http://nicoavila.s3.amazonaws.com/articulos/03_04creacion-grupo-iam.jpg)

En la siguiente vista debemos seleccionar la política de acceso para el nuevo grupo. Nos interesa que solo tenga acceso a la API de AWS Rekognition.  
Para buscar la política, en el campo **Filter** debemos escribir la palabra "Rekognition". El sistema filtrará todas las políticas relacionadas con esta búsqueda. Seleccionaremos la política **AmazonRekognitionFulAccess** y haremos click sobre el botón **Next Step**.

![Grupo IAM Policy](http://nicoavila.s3.amazonaws.com/articulos/03_05creacion-grupo-iam-policy.jpg)

El último paso corresponde a un review con las opciones seleccionadas. Confirmamos la creación del nuevo grupo para pasar a la creación del usuario.

![Grupo IAM Review](http://nicoavila.s3.amazonaws.com/articulos/03_06creacion-grupo-review.jpg)  

La creación del usuario se realizará desde el panel lateral izquierdo de IAM. Seleccionaremos la opción **Users** y haremos click sobre **Add User**. Se desplegará una nueva vista en donde debemos indicar el nombre del nuevo usuario. En el campo **User detail** escribiremos "rekognition-api". En la opción **Access type** debemos marcar "Programmatic access".

![Creación Usuario](http://nicoavila.s3.amazonaws.com/articulos/03_07creacion-usuario.jpg)

El siguiente paso es asignar el nuevo usuario al grupo. Marcamos el grupo y hacemos click en **Next step**

![Asignar usuario a grupo](http://nicoavila.s3.amazonaws.com/articulos/03_08asignar-grupo-usuario.jpg)

El penúltimo paso es la revisión de todos los datos para el nuevo usuario. Hacemos click en el botón **Create User** para confirmar.

![Review creación usuario](http://nicoavila.s3.amazonaws.com/articulos/03_09review-creacion-usuario.jpg)

La vista final mostrará el **Acess Key ID** y **Secret Access Key**. Debemos anotar esos dos valores para poder autenticarnos usando el AWS SDK de Node.

![Access Key](http://nicoavila.s3.amazonaws.com/articulos/03_10access_key.jpg)

## Escribiendo la aplicación: Dependencias y configuración
Luego de un árduo trabajo creando los accesos necesarios, es hora de volver a desarrollar! En una terminal, en la carpeta del proyecto, instalaremos las dependencias necesarias para que todo funcione:

```npm install aws-sdk dotenv --save```

Luego, crearemos un archivo con extensión ```.env``` el cual agregaremos la siguiente información

```
AWS_ACCESS_KEY=<TU ACCESS KEY ID>
AWS_SECRET_ACCESS_KEY=<TU SECRET ACCESS KEY>
AWS_REGION=<TU REGIÓN DE AWS>
```

En el archivo ```app.js``` agregaremos las dependencias, para luego crear una nueva instancia de **AWS Rekognition**. Los parámetros de configuración serán obtenidos gracias a la dependencia [dotenv](https://github.com/motdotla/dotenv), que hará disponibles los parámetros de configuración presentes en el archivo ```.env``` en el objeto ```proccess.env``` de Node.

```javascript
const AWS = require('aws-sdk');
const dotenv = require('dotenv').config();
const fs = require('fs');

const rekognition = new AWS.Rekognition({
  accessKeyId: process.env.AWS_ACCESS_KEY,
  secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
  region: process.env.AWS_REGION,
  apiVersion: '2016-06-27'
});
```
