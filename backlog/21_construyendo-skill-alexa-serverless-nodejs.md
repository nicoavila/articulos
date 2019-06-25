# Construyendo una Skill de Alexa con Serverless Framework y NodeJS
### Marzo 2019

![Alexa Skill](https://nicoavila.s3.amazonaws.com/articulos/21_01construyendo-skill-alexa-serverless-nodejs.png)

Las **Voice User Interfaces** (VUIs por sus siglas en inglés) han ganado mucha popularidad últimamente. No es fortuito que las grandes compañías de internet deseen potenciar sus asistentes de voz: Google posee su [Voice Assistant](https://assistant.google.com/), Microsoft tiene a [Cortana](https://www.microsoft.com/es-es/windows/cortana) y Amazon tiene a [Alexa](https://developer.amazon.com/es/alexa).  
Construir un programa con el cual podamos interactual con nuestra voz es una actividad entretanida y desafiante :smiley:.  
Alexa es el asistente de voz de Amazon, el cual es distribuído por la misma compañía. Podemos entregarle comandos como: *"Alexa, dime la hora"*, *"Alexa, dime como va a estar el clima hoy"*, *"Alexa, añade una entrada a mi calendario para mañana a las 8:00am"*, *"Alexa, dime quien fue el inventor del teléfono"*.
Cuando tenemos una necesidad un poco más compleja de satisfacer, se hace necesaria la creación de una [Skill Personalizada](https://developer.amazon.com/docs/custom-skills/understanding-custom-skills.html)

## ¿Qué necesitaremos?
* [NodeJS](https://nodejs.dev/) 10 o superior
* [Serverless Framework](https://serverless.com/)
* Cuenta de [Amazon Web Services](https://aws.amazon.com/es/)
* Cuenta de [Amazon Developers](https://developer.amazon.com/)

> No es necesario contar con un dispositivo con Alexa para la realización de este tutorial.

## Enfocándonos en la aplicación, no la infraestructura
La arquitectura serverless es un concepto acuñado hace relativamente poco. Esta arquitectura hace referencia a aplicaciones que dependen de servicios de terceros o código que corre en *contenedores efímeros*.  
Proveedores como Amazon Web Services, con su servicio [AWS Lambda](https://aws.amazon.com/es/lambda/), permite la ejecución de funciones, las cuales al ser invocadas son automáticamente ejecutadas, asignándose recursos computacionales para efectuar una determinada tarea.

> Para la gran mayoría de los casos de uso, el costo de una función serverless es bajo. Sin embargo, para que nunca te lleves una sorpresa, te recomiendo siempre tener en cuenta los costos asociados al servicio :credit_card:. Revisa la sección de ["Pricing"](https://aws.amazon.com/es/lambda/pricing/) para más información

## ¿Cómo funciona una Skill de Alexa?

* **Intents**:
* **Utterances**:
* **Slots**:
* **Slot Types**:

## Serverless Framework
Este increíble framework fue creado por [Austen Collins](https://twitter.com/austencollins) el 2016 [Historia de Serverless](https://youtu.be/6mNhMuBkIK8)

## Configurando lo necesario