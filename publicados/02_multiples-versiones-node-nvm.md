# Múltiples versiones de Node con NVM
### Mayo 2018

Nos encanta [Node](https://nodejs.org/)! Y cada vez que sale un nuevo release de una versión no podemos esperar para probar todas las características geniales que trae.  
Si trabajamos con Node, de seguro tenemos una versión instalada en nuestro entorno de desarrollo. El problema ocurre cuando queremos trabajar con una versión específica en un proyecto o queremos probar una característica específica de un [release](https://nodejs.org/es/about/releases/): qué estrategia podemos utilizar?. Ahi es cuando **Node Version Manager** o **[NVM](https://github.com/creationix/nvm)** sale al rescate!. Esta pequeña utilidad nos permite mantener múltiples versiones de Node en nuestro sistema las cuales pueden ser cambiadas bajo demanda.  
En este tutorial aprenderemos a instalar y usar NVM.

![Imagen Principal](http://nicoavila.s3.amazonaws.com/articulos/02_01multiples-versiones-node-nvm.jpg)

## Breve introducción al versionamiento de Node
Node utiliza [versionamiento semántico](https://semver.org/) para cada uno de sus releases. Poseen un [proceso muy estructurado](https://github.com/nodejs/Release#mandate) para realizar el lanzamiento de una nueva versión.  
El término **LTS** hace referencia a **Long Term Support**, es decir, una versión que actualmente se encuentra con soporte por un periodo de 18 meses. Luego de transcurrido ese tiempo, la versión pasará a modo mantención por 12 meses.  
Al igual que [Ubuntu](https://wiki.ubuntu.com/Releases), Node posee nombres en clave para algunas de sus versiones. Actualmente las siguientes se encuentran disponibles:

> Argon: Node 4.x  
> Boron: Node 6.x  
> Carbon: Node 8.x  

## Instalando Node con NVM (Linux / macOS)
> Este tutorial hace uso de la versión 0.33.11 de NVM. Para obtener la última versión disponible pueden visitar el [repositorio del proyecto](https://github.com/creationix/nvm).

El primer paso es instalar NVM. Para ello debemos abrir un terminal y ejecutar lo siguiente:

```curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash```

[cURL](https://curl.haxx.se/) es una utilidad de línea de comando que nos permite transferir información a través de varios protocolos (HTTPS, HTTP, FTP, etc). Si cURL no está disponible en tu sistema puedes instalarla con ```sudo [apt-get/yum] install curl``` (Linux) o ```brew install curl``` (macOS).  
Luego de instalar NVM deben cerrar y abrir nuevamente el terminal para hacer efectiva la instalación.

## Usando NVM
Al abrir un nuevo terminal, ya tendremos disponible el comando ```nvm```. si lo ejecutamos obtendremos algo similar a esto:

![Comando NVM](http://nicoavila.s3.amazonaws.com/articulos/02_02comando-nvm.jpg)

A continuación mostraré los comandos más utilizados. Pueden acceder a la lista completa de opciones disponibles escribiendo ```nvm``` en su terminal.

### nvm list
Permite listar todas las versiones instaladas en tu sistema. En mi caso tengo instaladas las versiones **8.4.0**, **8.9.1**, **8.9.4** y **8.10.0**. 

![Comando nvm list](http://nicoavila.s3.amazonaws.com/articulos/02_03comando-nvm-list.jpg)

### nvm ls-remote --lts
Permite listar todas las versiones LTS disponibles para descargar desde los repositorios oficiales de Node. Si utilizan el comando sin el flag ```--lts```, NVM listará todas las versiones disponibles de Node.

![Comando nvm list --lts](http://nicoavila.s3.amazonaws.com/articulos/02_04comando-nvm-ls-remote.jpg)

### nvm install <VERSIÓN>
Permite instalar una versión en específico. Una vez ha finalizado la instalación, NVM automáticamente cambiará la versión a la última descargada.

![Comando nvm list --lts](http://nicoavila.s3.amazonaws.com/articulos/02_05comando-nvm-install.jpg)

### nvm use <VERSIÓN>
Permite activar una versión específica de Node

![Comando nvm use](http://nicoavila.s3.amazonaws.com/articulos/02_06comando-nvm-use.jpg)

### nvm uninstall <VERSIÓN>
Permite desinstalar una versión específica de Node

![Comando nvm uninstall](http://nicoavila.s3.amazonaws.com/articulos/02_07comando-nvm-uninstall.jpg)

## ¿Que ocurre si ya tengo una versión de Node previamente instalada?
Es recomendable desinstalar previamente cualquier versión de Node antes de utilizar NVM

## ¿Cómo sacarle partido a NVM en nuestros proyectos?
Puede ser un poco laborioso para algunos tener que estar cambiando constantemente de versión de Node cuando trabajamos en múltiples proyectos. Existe una solución que permite agilizar este proceso.  
En la carpeta principal del proyecto se debe crear un archivo ```.nvmrc``` que contenga el número de versión que deseamos utilizar. Por ejemplo

```bash
8.9.1
```

Luego mediante la consola, nos dirigimos a la carpeta del proyecto y escribimos ```nvm use``` para activar la versión correspondiente.

## ¿Y si tengo Windows :scream: ?
Lamentablemente NVM está solo disponible para Linux y macOS :sad:... Pero tranquil@s! :smile: Si tu sistema operativo es Windows aun puedes utilizar NVM. Existe una versión paralela (totalmente separada de la versión oficial) llamada [nvm-windows](https://github.com/coreybutler/nvm-windows). Los comandos básicos (como listar, instalar y usar) se encuentran disponibles.
