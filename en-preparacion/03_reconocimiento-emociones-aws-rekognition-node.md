# Reconocimiento de emociones con AWS Rekognition y Node
### Mayo 2018

La inteligencia artificial (IA) y Machine Learning (ML) cada vez está tomando más fuerza. Todos los días nos [asombramos de los avances en el campo](https://www.forbes.com/sites/tonybradley/2017/07/31/facebook-ai-creates-its-own-language-in-creepy-preview-of-our-potential-future/).
Para trabajar con IA se requiere un amplio conocimiento de **álgebra lineal**, **estadísticas** y **cálculo** con el fin de crear e impementar modelos que permitan a la máquina "pensar". Los gigantes tecnológicos como [Google](https://cloud.google.com/products/machine-learning/), [Microsoft](https://azure.microsoft.com/en-us/services/machine-learning-studio/) y [Amazon Web Services](https://aws.amazon.com/es/machine-learning/) (AWS) han creado sus propios servicios relacionados con *Machine Learning disponibles para desarrolladores y público general, democratizando así el acceso a las ventajas de la IA que puede tener en nuestra aplicaciones.  
En este tutorial utilizaremos la API de [AWS Rekognition](https://aws.amazon.com/es/rekognition/) para crear una pequeña aplicación de consola que nos permita determinar que emoción tienen las fotografías presentes en un directorio.

![Imagen Principal](http://nicoavila.s3.amazonaws.com/articulos/03_01reconocimiento-emociones-aws-rekognition-node.jpg)

## Preparando todo
Crearemos un nuevo proyecto en Node ejecutando lo siguiente

```bash
mkdir rekognition-node
npm init -y
cd rekognition-node
```